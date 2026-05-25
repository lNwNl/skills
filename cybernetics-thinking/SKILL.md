---
name: cybernetics-thinking
description: Use when diagnosing system instability, designing adaptive systems, reverse-engineering black-box systems, evaluating monitoring/alerting strategies, or analyzing systems that exhibit feedback loops, emergent behavior, or cascading failures
---

# Cybernetics Thinking

## Overview

Cybernetics provides a universal language for systems thinking: feedback, information, control, variety, and stability. Applying these concepts exposes structural flaws that domain-specific analysis often misses.

## When to Use

```
Problem involves feedback loops (cyclic dependencies)?
  ├─ Yes → Apply feedback analysis
  └─ No

Problem space is partially unobservable (black boxes)?
  ├─ Yes → Apply black-box methodology
  └─ No

System must handle unknown future variety?
  ├─ Yes → Apply Ashby's Law as design constraint
  └─ No

Need to distinguish signal from noise in observations?
  └─ Yes → Apply information-theoretic framing
```

Use this skill when encountering: cascading failures, rates that "shouldn't be" near limits, alert fatigue, concept drift in models, reverse-engineering external systems, or debugging problems that mutate when you fix one cause.

## The Agent as a Cybernetic System

You — the AI agent — are also a control system. Apply these concepts to yourself:

- **Your feedback loop**: output → user response → adjustment. Negative feedback when user corrects you; positive feedback when user builds on your output.
- **Your variety**: your toolkit (bash, file ops, web search, subagents) is your variety against the disturbance space (the user's problem). If a problem exceeds your toolkit variety, you need to reduce environmental variety (ask clarifying questions, scope down) or increase internal variety (use subagents, fetch docs).
- **Your black-box**: the user's mind is opaque. Model their intent through their inputs and reactions, not by guessing their internal mental state.
- **Your information probes**: design questions to eliminate large uncertainty. A yes/no question that splits the hypothesis space in half is high-information. An open-ended "what do you want?" is low-information when the solution space is large.

## Red Flags — STOP and Apply the Framework

- You've proposed a solution without identifying any feedback loops
- You're tuning parameters (thresholds, cache sizes, timeouts) without asking about the feedback structure
- You're treating a rate limiter trip / alert / error as the problem rather than a signal
- You keep "fixing" the same problem in different places
- You've designed a controller (monitor, rate limiter, agent) without checking Ashby's Law
- You're guessing about internals of a system you haven't probed systematically
- "The metrics look fine but the system is broken"

**All of these mean: Apply the three diagnostic questions before proceeding.**

## Rationalization Countermeasures

| Excuse | Reality |
|--------|---------|
| "This is just a simple bug, not a systems problem" | Simple bugs in coupled systems ARE systems problems. The feedback structure matters. |
| "I understand the architecture already" | Understanding code structure ≠ understanding feedback dynamics. Map the loops. |
| "Ashby's Law is too abstract to apply here" | Ask: "Can my controller distinguish all the states it needs to?" If not, variety deficit. Concrete. |
| "Black-box methodology is for competitors, not our own system" | Any system you can't fully introspect — including legacy code, sub-agents, and users — is a black box. |
| "Information theory is overkill for alerting" | Every alert that fires when nothing's wrong IS a zero-information signal. That's the definition of alert fatigue. |
| "I'll think about stability boundaries later" | "Later" = after the collapse. Stability boundaries are the most important thing you don't measure. |

## Violating the Three Questions = Violating the Skill

**If you propose a solution without explicitly answering the three diagnostic questions, you have not applied this skill.** The questions are not optional — they are the minimum viable application of cybernetic thinking.

No exceptions:
- Not for "quick fixes" — quick fixes that ignore feedback loops become long problems
- Not for "obvious" problems — "obvious" problems have hidden feedback you're not seeing
- Not for "I already know the answer" — knowing the mechanism ≠ knowing the loop structure

## Core Concepts

### Feedback Loops

Every adaptive system has feedback. Before diagnosing any problem, ask: "What are the feedback loops?"

| Loop type | What it does | Danger sign |
|-----------|-------------|-------------|
| **Negative feedback** | Dampens deviation, maintains stability | Too strong → sluggish, never adapts |
| **Positive feedback** | Amplifies deviation, drives change | Too strong → runaway, explosion, cascade |

**Red flag**: If a "fix" moves the problem to a different subsystem, the root cause is likely a positive feedback loop you haven't identified, not the symptom you're treating.

**Ask yourself**:
- Where is amplification happening? (retry storms, cache avalanches, herd behavior)
- Where should damping exist but doesn't? (rate limiters without `Retry-After`, load shedders without backpressure)
- Is there a lag between cause and effect that hides the loop? (delayed metrics, batch processing windows)

### Ashby's Law of Requisite Variety

> Only variety can absorb variety. A controller's internal state space must be at least as rich as the disturbance space it must regulate.

**Apply this as a design constraint**:

- A monitoring system with 5000 metrics → detectors need variety to match. One static threshold per metric = variety deficit → alert fatigue.
- An agent facing 100 failure modes → its strategy repertoire must cover 100 scenarios. A single fallback path = variety deficit → brittleness.
- A rate limiter facing diverse traffic patterns → must distinguish retries from new requests, bursts from sustained load. Binary counter = variety deficit → false trips.

**The constraint flows both ways**:

- If you cannot increase your system's variety, you must reduce the environment's variety (e.g., normalize inputs, constrain the API surface, add gateways).
- If you cannot reduce environmental variety, you must increase internal variety (e.g., add detectors, add strategy branches, enrich state representation).

### Black-Box Methodology

When you cannot (or should not) open a system's internals, model it entirely through input-output relationships.

**Steps**:
1. Define the observable input space dimensions
2. Probe systematically — not random sampling, but structured perturbation along each dimension independently
3. Record outputs. Look for: sensitivity patterns (which inputs dominate?), discontinuities (where do small input changes cause large output shifts?), and invariants (what outputs never change?)
4. Build a surrogate model that reproduces the observed I/O mapping
5. Validate: does the surrogate predict new inputs correctly? Where it fails → the black box has hidden internal state or signals you haven't identified

**When to use black-box**:
- Competitor's system you can't access
- Legacy system with no docs (and changing it is too risky)
- Human user you're serving (their mind is the ultimate black box)
- Sub-agents or LLM chains whose intermediate reasoning is noisy/unreliable

**Key insight**: "Model the behavior, don't guess the mechanism." A correct I/O model is more useful than a wrong internal model.

### Information, Noise, and Entropy

Frame observations in information-theoretic terms:

- **Information**: What does this observation eliminate as impossible? Shannon's definition — information is reduction of uncertainty.
- **Noise**: Variation that doesn't reduce uncertainty about what you care about.
- **Signal-to-noise ratio**: How much each observation matters for your goal.

**Application to monitoring/alerting**:
- An alert carries zero information if it fires equally often during incidents and non-incidents → reduce alert rate or add correlation gating.
- A metric carries high information if its behavior during incidents is radically different from baseline.
- Use this to rank detectors: prefer high-information metrics, deprecate low-information ones.

**Application to debugging**:
- Each test/observation should eliminate at least half the remaining hypotheses. If it doesn't, it's a low-information probe and you're wasting time.

### Stability and Boundaries

Every stable system has a domain of stability — a range of inputs/disturbances within which it returns to equilibrium. Beyond that boundary, the system undergoes qualitative change (phase transition, cascade, collapse).

**Ask yourself**:
- What is this system's stability domain? At what load does it switch from graceful degradation to total failure?
- Is the boundary hard (precipitous cliff) or soft (gradual degradation)?
- Are we operating closer to the boundary than we think? (Metrics that look fine at steady-state may mask proximity to collapse.)

### Isomorphism and Cross-Domain Transfer

Systems from different domains can share the same structural organization (isomorphism) — same feedback structure, same variety constraints, same stability properties.

**Apply this to transfer solutions**:
- Retry storm in distributed systems ↔ autoimmune response in biology (positive feedback where defense mechanism becomes the problem)
- Rate limiting without backpressure ↔ traffic congestion without ramp metering
- Alert fatigue ↔ sensory habituation in neuroscience

When stuck on a problem, ask: "What other domain has this same feedback structure?" The solution there may transfer.

## Quick Reference

| When you see... | Apply this lens |
|----------------|-----------------|
| Problem mutates after each fix | Positive feedback loop — find the amplifier |
| False positives everywhere | Variety deficit (Ashby) — enrich detector space |
| Can't access internals | Black-box methodology — structured I/O probing |
| Too many metrics/alerts | Information theory — rank by uncertainty reduction |
| System fine then suddenly broken | Stability boundary — find the phase transition |
| Unknown how a rival works | Black-box probing → surrogate modeling |
| System never adapts | Negative feedback too weak or lag too long |
| Same pattern, different domain | Isomorphism — transfer the known solution |

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Treating symptoms without modeling feedback loops | Ask "what loop is this symptom embedded in?" first |
| Adding more monitoring without increasing control variety | Match Ashby: if you can't act on new signals, they're noise |
| Assuming internal structure matches observed behavior | Use black-box: model the I/O, don't guess the internals |
| Tuning thresholds instead of redesigning the feedback structure | Thresholds are local; feedback structure is global |
| Ignoring stability boundaries until collapse | Ask "at what load does this stop degrading gracefully?" |
| Adding detectors without measuring information content | Each new metric should eliminate uncertainty about a specific failure mode |

## The Three Diagnostic Questions

When approaching any system problem, answer these before proposing solutions:

1. **What are the feedback loops?** (positive and negative)
2. **Does the control system have enough variety?** (Ashby check)
3. **Where are the stability boundaries?** (how close are we to collapse?)