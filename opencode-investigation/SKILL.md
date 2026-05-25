---
name: opencode-investigation
description: 调查 opencode Agent 的执行记录，排查 Agent 行为异常。使用当需要查看 Agent 实际做了什么、排查执行失败原因、追踪 session 中的 tool call 详情、分析 Agent 输出文件为何缺失时。例如："Agent 为什么没有写出文件"、"查看容器内的 session 记录"、"conclude 做了什么"、"排查超时原因"
---

# OpenCode Agent 执行调查

## 数据位置

| 路径 | 说明 |
|------|------|
| `~/.local/share/opencode/opencode.db` | SQLite 数据库，存储全部结构化数据 |
| `~/.local/share/opencode/log/` | 运行日志（每次启动一个文件，保留 10 个） |

容器内路径为 `/root/.local/share/opencode/`。以下用 `$DB` 代表数据库路径。

```bash
# 从宿主机查询容器内数据库
podman exec <container> sqlite3 /root/.local/share/opencode/opencode.db "SQL"
```

## 数据库核心表

排查只用三张表。`session_message` 仅存 agent/model 切换事件，忽略。

### session — 定位会话

| 字段 | 排查价值 |
|------|----------|
| `id` | 会话 ID (`ses_xxx`)，关联 message/part |
| `title` | 快速识别内容 |
| `directory` | Agent 工作目录，**最常用的查找条件** |
| `time_created` / `time_updated` | 时间定位（epoch ms） |

### message — 对话时间线

`data` 为 JSON，关键字段：

| 字段 | 说明 |
|------|------|
| `role` | `user` / `assistant` |
| `finish` | `tool-calls` / `stop` / 不存在（被 kill） |
| `cost` | 调用成本（被 kill 时为 0） |
| `tokens.input/output/reasoning` | token 用量（被 kill 时全 0） |
| `time.created` / `time.completed` | 精确时间（epoch ms） |

**判断进程被 kill**：`finish` 不存在 + `cost=0` + `tokens` 全 0。

### part — tool call 详情（**最核心**）

`data` 为 JSON，`type` 字段区分类型：

| type | 关键字段 |
|------|----------|
| `tool` | `tool`(工具名), `state.input`(输入), `state.output`(输出), `state.status` |
| `reasoning` | `text` — Agent 推理过程 |
| `text` | `text` — 文本内容 |
| `step-start/step-finish` | 步骤边界，含 token/cost |

write 工具的 `state.input` 中：`filePath` = 写入路径，`content` = 写入内容。

## 查询模板

```bash
# 1. 快速列出所有 session
podman exec <container> opencode session list --format json

# 2. 按目录查找 session
podman exec <container> sqlite3 $DB \
  "SELECT id, title, datetime(time_created/1000,'unixepoch'), directory
   FROM session WHERE directory LIKE '%task_1' ORDER BY time_created"

# 3. 查看 session 对话时间线（判断执行状态）
podman exec <container> sqlite3 $DB \
  "SELECT id, datetime(time_created/1000,'unixepoch'),
          json_extract(data,'$.role') as role,
          json_extract(data,'$.finish') as finish,
          json_extract(data,'$.cost') as cost
   FROM message WHERE session_id='ses_xxx' ORDER BY time_created"

# 4. 查看某 message 的 tool call 详情
podman exec <container> sqlite3 $DB \
  "SELECT id, json_extract(data,'$.type') as type,
          json_extract(data,'$.tool') as tool,
          substr(json_extract(data,'$.state.input'),1,500) as input,
          substr(json_extract(data,'$.state.output'),1,200) as output
   FROM part WHERE message_id='msg_xxx' ORDER BY time_created"

# 5. 查看 Agent 写入的文件路径和内容
podman exec <container> sqlite3 $DB \
  "SELECT json_extract(data,'$.state.input.filePath') as filepath,
          json_extract(data,'$.state.input.content') as content
   FROM part WHERE message_id='msg_xxx'
     AND json_extract(data,'$.type')='tool' AND json_extract(data,'$.tool')='write'"

# 6. 查看 Agent 推理过程
podman exec <container> sqlite3 $DB \
  "SELECT json_extract(data,'$.text')
   FROM part WHERE message_id='msg_xxx'
     AND json_extract(data,'$.type')='reasoning'"

# 7. 按 session 查找所有 write tool call
podman exec <container> sqlite3 $DB \
  "SELECT p.id, datetime(p.time_created/1000,'unixepoch'),
          json_extract(p.data,'$.state.input.filePath') as filepath
   FROM part p WHERE p.session_id='ses_xxx'
     AND json_extract(p.data,'$.type')='tool' AND json_extract(p.data,'$.tool')='write'
   ORDER BY p.time_created"
```

## 日志辅助

日志是辅助手段，主要排查查数据库。日志用于确认启动参数、LLM 耗时、错误警告。

```bash
# 日志在容器内：/root/.local/share/opencode/log/
# 格式：LEVEL  TIMESTAMP +DIFFms key=value message

# 按 session 过滤
podman exec <container> grep 'session.id=ses_xxx' /root/.local/share/opencode/log/*.log

# 查看错误
podman exec <container> grep -E 'ERROR|WARN' /root/.local/share/opencode/log/*.log

# 查看启动参数（含完整 opencode run 命令）
podman exec <container> grep 'args=' /root/.local/share/opencode/log/*.log
```

## 排查 Workflow

```
1. opencode session list --format json  → 找到目标 session
2. 查 message 表 → 看 finish/cost 判断是否被 kill
3. 查 part 表 → 看 type=tool 的 input/output（Agent 实际做了什么）
4. 查 part 表 type=reasoning → 看 Agent 的推理过程
5. 辅助：grep 日志 → 看启动参数和错误
```

## 排查实例：conclude 结果丢失

**现象**：Act 超时后数据库记录"任务超时，未产出结果"，但容器内有 `act_output.json`。

**关键查询**：
```bash
# 查 conclude 阶段的 tool call → 发现 Agent 写入了 act_output.json 而非 conclude_output.json
sqlite3 $DB "SELECT json_extract(data,'$.tool'), json_extract(data,'$.state.input.filePath')
  FROM part WHERE message_id='conclude的msg_id' AND json_extract(data,'$.type')='tool'"
# → write, /root/workspace/task_1/act_output.json
```

**根因**：conclude prompt 没指定输出文件路径，Agent 从 session 上下文记住了主执行路径。代码读 `conclude_output.json`，Agent 写 `act_output.json`，路径不匹配 → 读不到 → 异常被 catch 吞掉 → 超时消息写入数据库。
