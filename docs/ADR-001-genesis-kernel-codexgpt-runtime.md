# ADR-001 — Genesis Governance Kernel + CodexGPT Runtime

Status: Proposed for Owner merge approval
Decision date: 2026-08-29
Decision source: closed Issue #3 POC / R5 acceptance

## Context

Project Genesis Bridge started as a build-vs-adopt investigation for a durable Owner → ChatGPT PMO → governed bridge → Codex workflow on Windows.

The decisive live evidence came from CodexGPT R3/R4/R5: stable App recovery survived Windows reboot; replacement PMO state recovery worked; handoff-only PMO → Codex → evidence → PMO review worked; a genuine two-round corrective loop worked; PMO-side root/secret/write/shell controls were strong. R5 also proved important gaps: exact Owner Approval → plan digest → executor authorization was missing, isolated-workspace orchestration/evidence binding was not yet a kernel invariant, full attempt/idempotency was incomplete, executor containment remained partial, and browser Smoke still needed a managed CLI-compatible solution.

R5 therefore concluded `HYBRID_GENESIS_KERNEL_PLUS_CODEXGPT`.

## Decision

Use the existing `itachy1986/project-genesis-bridge` PlanBridge-derived repository as the lineage for the **Genesis Governance Kernel**.

Adopt **CodexGPT** as the preferred Windows-native ChatGPT ↔ local executor runtime/transport substrate, initially pinned to the live-verified baseline:

- repository: `chatGPT-10/codexgpt`
- version: `1.0.4`
- verified source commit: `c43ec8ecae9782598ebc9cf90d8df8cdde1035c1`

Codex CLI remains the Developer executor behind that transport.

```text
Owner
  ↓
ChatGPT PMO
  ↓
Genesis Governance Kernel
  ├─ Project Registry / PMO Bootstrap
  ├─ Owner Gate
  ├─ Task → Plan → Approval → Attempt
  ├─ Workspace lifecycle / base protection
  ├─ Executor Profile / context
  ├─ Evidence binding / PMO Decision
  ├─ attempt budget / idempotency
  └─ durable audit / recovery
  ↓
CodexGPT runtime / transport
  ├─ bounded MCP project access
  ├─ handoff transport
  ├─ Codex CLI launch/wait/result
  ├─ stable Windows endpoint integration
  └─ PMO-side path/tool restrictions
  ↓
Codex CLI Developer
  ↓
Tests / managed verification / Browser Smoke
  ↓
PMO review
  ↓
Owner UAT or next Owner Gate
```

Genesis decides **whether work is authorized, exactly what is authorized, which workspace is writable, which attempt is running and what evidence can be accepted**. CodexGPT transports already-governed work to Codex and returns execution evidence.

## PlanBridge role

PlanBridge is no longer the preferred Windows runtime, but remains the Kernel lineage/upstream reference, a source of governance/worktree/fail-closed concepts, and a fallback runtime candidate. The current repository remains PlanBridge-derived during Genesis-1.0 foundation.

## CodexGPT integration policy

CodexGPT is a replaceable runtime adapter, not governance truth. Genesis pins a verified version/commit, detects drift at startup, avoids hidden defaults for model/CODEX_HOME/workspace/approval/security, and keeps an adapter boundary so another runtime can replace it later without replacing the Kernel.

## Executor context policy

Every governed Codex attempt must eventually record: Codex CLI version/binary, requested and actual model, reasoning effort, CODEX_HOME, effective Global AGENTS source/digest, effective project/nested AGENTS chain/digests, selected task Skills context, task workspace identity/HEAD, and plan/approval/attempt identity.

Model selection is configurable at global/project/task level and capability-checked; Genesis does not permanently hard-code one model alias.

## Workspace policy

Target invariant: `One Task = One isolated task workspace = N governed attempts`.

Corrective attempts normally reuse the same workspace. Genesis owns workspace creation/admission, base protection, lifecycle and evidence binding.

## Verification policy

Target chain: `Codex technical tests → configured API/CLI/managed Browser Smoke → PMO independent evidence review → Owner UAT when required`.

Owner UAT never substitutes for Developer/PMO technical Smoke. A Genesis-managed browser runner is required so CLI migration does not regress browser verification.

## Governance compatibility

Project Genesis Bridge is governed through `itachy1986/ai-pmo-control` after formal enrollment. Registered target projects may point to `ai-pmo-control` or another configured governance source. PMO_INTERNAL remains separate from the minimum EXECUTOR_VISIBLE contract sent to Codex.

## Alternatives

### Adopt CodexGPT alone
Rejected as final architecture. R5 proved it is the strongest Windows runtime candidate but not a complete Governance Kernel.

### Continue PlanBridge-only
Retained as fallback/reference, not primary runtime. Its governance concepts are useful, but the POC exposed Windows/runtime and conversation recovery disadvantages that CodexGPT stable-endpoint testing did not reproduce.

### Permanent ChatGPT PMO + Codex Desktop
Retained as migration fallback only. It preserves the current interactive/browser workflow but keeps the Owner in the transport loop and does not meet the automated PMO↔Developer/recovery/audit goal.

### PatchBay direct Windows base
Rejected after native worker POC failed on POSIX process-supervision assumptions. Durable worker/worktree ideas remain references.

## Genesis-1.0 required work

PROGRAM Issue #12 and task Issues #4–#11 define the formal backlog:
1. Project Registry / PMO Bootstrap / capability detection.
2. Exact-plan Owner Approval ledger, attempt model and idempotency.
3. Isolated workspace orchestration, base protection and evidence binding.
4. Executor context: model/reasoning/CODEX_HOME/AGENTS/skills identity.
5. Managed Browser Smoke before Owner UAT.
6. Executor security containment and no-secret runtime policy.
7. Multi-project Windows one-click runtime and stable endpoint.
8. Migration pilots for `project-managerment-new` and `agent-risk-intelligence`, retaining Codex Desktop rollback until acceptance.

## Revisit triggers

Re-evaluate the runtime choice if CodexGPT loses maintainable Windows/ChatGPT MCP support, another runtime proves materially better while preserving Kernel invariants, a first-party OpenAI local orchestration layer subsumes the transport layer, or managed browser verification cannot be integrated safely with the CLI path.
