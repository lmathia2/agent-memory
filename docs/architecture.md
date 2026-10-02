# A Possible Architecture for a Durable Personal Agent

*Technical companion to the [product thesis](../README.md).*

This note proposes one implementation of the product. It separates runtime guarantees from model-controlled memory and context strategies. The memory-agent role, store choices, tool surface, and context-management policy remain design choices to evaluate.

> **The timeline is evidence. Memory is computed from it. Work is durable. Context is compiled. Authority is external.**

## Keep the substrate durable and the memory strategy adaptable

Model capabilities and context strategies will change. The system should preserve the evidence and work needed to benefit from those changes, while allowing the model to improve how it organizes and uses them.

Two recent systems sharpen this separation.

**[Pi Durable](https://earendil.com/posts/pi-durable/)** checkpoints model calls, tool calls, and application work. It retains transcripts after compaction and commits typed application documents alongside them. SQLite and JSONL are alternative storage backends beneath a common harness interface; documents and extensions determine what the application tracks. Exactly-once submission is distinct from safely replaying a tool.

**[Context Language Models](https://arxiv.org/html/2609.37725v1)** make live context an editable file. Models can develop context transformations through general code, rather than choosing from a fixed menu of compaction and retrieval strategies. Their coding and research evaluations support adaptive context management. Extending this control to persistent personal memory is our architectural proposal, not a result established by the paper.

These ideas fit together: preserve evidence and execution state independently of the model, then give the model tools to construct the memory and context it needs.

| Concern | What the system provides | What can evolve |
| --- | --- | --- |
| **Storage: how to persist** | Backend interfaces with explicit commit, recovery, and retention semantics. | SQLite, JSONL, or enterprise database and object-store adapters. |
| **Access: how to operate and query** | Governed tools for files, databases, vector stores, and source references. | Available query engines, indexes, and execution environments. |
| **Memory: what to organize and use** | An authorized workspace over retained evidence. | Model-chosen notes, structures, queries, consolidation procedures, and context strategies. |

Files, databases, and vector stores provide different operations. The model can choose among them, combine them, and write programs over them. Preferences, entities, episodes, and summaries are useful possible representations; they need not be prescribed memory categories. The tool interfaces define capabilities and guarantees, while the organization of memory can evolve with the model.

## Memory becomes an agentic service

A memory agent maintains the employee's continuity across tasks. A task agent works on a scoped responsibility. These are logical roles; they can use the same underlying model while having different context and tool access.

Writing notes, updating artifacts, consolidating experience, querying evidence, and supplying active context become tool operations executed through the durable runtime. Incoming evidence is recorded before acknowledgment. Derived-memory maintenance can run asynchronously, with versioned results. A task can wait for a relevant update or use a view whose freshness is explicit.

When preparing an account brief, for example, the memory agent may query recent correspondence, recover a prior decision, update an account note, and pass the task agent a package of relevant developments and unresolved commitments. The user's instructions determine which personal information may be shared for that purpose. The task agent can request more evidence through the same interface.

The memory agent selects relevance and proposes the content to share. Sharing restrictions and enterprise entitlements are enforced outside the model. An explicit user exclusion overrides inferred usefulness; model-generated redaction alone does not authorize disclosure. Context packages and derived artifacts carry source dependencies, versions, and restrictions so that corrections or revoked access can invalidate them.

Memory is therefore computed through agent-selected operations over evidence. Useful artifacts can persist, but consolidation does not replace the evidence record. Where exact source lineage cannot be established, artifacts remain conservatively restricted to their input scope.

## Context is compiled, with the model choosing the working set

“Compiled” describes how a validated model input is materialized; it does not require a hand-engineered relevance policy.

The task model can retain, offload, reorganize, or reread information, including editing its working context and creating reusable context-management programs. The runtime turns that working view into the next request, checks access and budgets, and preserves protected policy and provenance outside the editable content.

Compaction can use summaries when omitted evidence remains recoverable under retention policy. A source version or permitted snapshot is needed when the original observation must be reconstructed; a changing URL is insufficient. Nothing necessary for continuity exists only in the prompt.

Log the context or a reconstructable manifest, its integrity hash, relevant versions, and the resulting operation. A hash verifies retained content; it cannot reconstruct missing content or explain hidden model reasoning.

![Agentic memory blocks: model-chosen strategy uses durable, governed tool interfaces over files, databases, and vector stores.](../assets/agent-memory.svg)

## Work and authority remain explicit

A model can remove a commitment from its context without cancelling the actual responsibility. Task status, waits, deadlines, triggers, approvals, and action outcomes remain authoritative operational state. Changing them requires explicit runtime operations.

Subscriptions and timers wake authorized work. Intent lifecycle includes scope, expiry, cooldown, and cancellation. Event handling needs deduplication and recovery from missed delivery. Completed work can update the workspace silently, wait for the next interaction, or trigger a notification; evaluate useful developments surfaced and missed alongside unnecessary interruptions.

Durability does not guarantee exactly-once external effects. Connectors need stable operation IDs, idempotency where available, and reconciliation when an action may have succeeded before its result was recorded. Ambiguous non-idempotent operations pause for resolution. Recomputing memory or context never re-executes external effects.

The authority plane evaluates current identity, entitlements, purpose, classification, information barriers, and approval scope for reads, sharing, and actions. **No amount of accumulated memory increases authority.**

| Constraint | Required behavior |
| --- | --- |
| **Retention and deletion** | Propagate through source copies, indexes, derived artifacts, and cached context, subject to legal holds and approved audit policy. |
| **Revoked access** | Invalidate affected artifacts, rebuild future contexts, clear dependent serving caches, and stop or re-scope affected work. |
| **Information barriers** | Preserve contributing sources' restrictions through summaries and reusable procedures. Transformation does not itself permit sharing. |
| **Untrusted content** | Keep provenance as runtime metadata. Retrieved instructions and context edits cannot create capabilities or approvals. |
| **Auditability** | Retain permitted inputs or references, versions, results, operation IDs, policy decisions, and approvals sufficient to establish an action's basis. |

## Build and evaluate in stages

**Durability → Agentic memory and context → Proactivity → Learning**

| Stage | Build | Exit criterion |
| --- | --- | --- |
| **Durability** | Evidence timeline, checkpointed tasks, typed state, event ingestion, and governed connectors. | Multi-session work survives injected failures and a model swap, incorporates a new event, and preserves state. Tested effects are neither lost nor duplicated; ambiguous outcomes are resolved or paused. |
| **Memory and context** | Store and query tools, asynchronous memory operations, scoped context delivery, and editable task context. | Compare agent-managed strategies with a fixed compiler on matched tasks and budgets. Measure completion, omissions, false inferences, freshness, latency, and cost. Sharing, deletion, revocation, and injection tests must pass. |
| **Proactivity** | Standing intents, lifecycle controls, and notification policy. | Measure development coverage and interruption precision; honor cancellation, expiry, and changed authority. |
| **Learning** | Mine verified traces into candidate memory procedures, skills, and harness improvements. | Reviewed candidates improve held-out outcomes within safety and cost bounds, preserve sharing restrictions, and support rollback. |

Local memory and context edits are ordinary task execution. Promoting a procedure for broader reuse is a separate step: verify its evidence, check its information boundaries, and evaluate it before deployment, initially with human review. The execution corpus improves the system only within its permitted use and retention boundaries.

Other systems provide complementary precedents: [Manus](https://manus.im/blog/Context-Engineering-for-AI-Agents-Lessons-from-Building-Manus) for recoverable external context; [OpenClaw](https://docs.openclaw.ai/concepts/memory-architecture) for provenance and explicit intent lifecycle; [Muse](https://introducing.muse.ai/) for persistent workspaces and background work with an [independent security boundary](https://security.muse.ai/); and [Meta-Harness](https://arxiv.org/abs/2603.28052) and [Growing Harness](https://arxiv.org/abs/2609.26760) for learning procedures from execution feedback.

## Decisions to evaluate

Compare a separate memory agent with memory tools used directly by task agents; model-managed context with a fixed compiler; and different store and query combinations under matched workloads. The first two choices concern cognitive policy and agent topology, while the third concerns access and persistence. They should be evaluated independently against the same continuity, sharing, recovery, and cost requirements.
