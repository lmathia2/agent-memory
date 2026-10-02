# A Possible Architecture for a Durable Personal Agent

*Technical expansion of the [five capabilities in the brief](../README.md#five-capabilities-within-the-agent-plane).*

The memory agent represents the user across tasks. It maintains memory from personal timeline evidence and mediates every task's access to that memory. Task agents receive only the context appropriate to their query, scope, and the user's constitution; they have no direct access to personal memory or its backing stores.

The design expands the brief's five capabilities without prescribing a fixed cognitive-memory taxonomy. Models can change how they organize evidence and construct representations. The runtime preserves work, enforces access, and makes those operations durable.

> **The timeline is evidence. Memory is computed from it. Work is durable. Context is compiled. Authority is external.**

![Expansion of the five capabilities: timeline-grounded memory agent with asynchronous maintenance and synchronous context retrieval, governed by the user constitution and enterprise authority.](../assets/agent-memory.svg)

## 1. Timeline: the grounding evidence

Record relevant messages, document versions, observations, decisions, corrections, approvals, agent actions, and outcomes with source, time, trust, entitlement, and retention metadata. Evidence is recorded before ingestion is acknowledged. Subsequent interpretations reference this record rather than replacing it.

New events can wake memory maintenance. A correction can invalidate a derived view and schedule its repair. Retention, deletion, and legal holds determine which underlying evidence remains available.

## 2. Agentic memory: the user's representative

The memory agent maintains continuity for the user, while task agents work on scoped responsibilities. This distinction is enforced through tool capabilities and data access; it does not require different model weights or a particular deployment topology.

The memory agent has two operational paths:

| Path | Tools and behavior | Result |
| --- | --- | --- |
| **Asynchronous maintenance** | Write and update memory; dream or reflect over experience; consolidate, reorganize, and maintain indexes. Run on events, schedules, or explicit requests. | Versioned derived artifacts and programs grounded in timeline evidence. |
| **Synchronous context service** | Query and retrieve memory; resolve evidence references; select relevant information and choose a representation for the requesting task. | A permitted context package for active reasoning, with sources, versions, and freshness. |

“Synchronous” means the task waits for a bounded context response. A request can use model reasoning and several retrieval operations. “Asynchronous” means maintenance can continue independently of the task's current turn. Both paths use the durable runtime.

Dreaming produces candidate interpretations or procedures from retained experience; it does not make an inferred claim verified merely by writing it. Useful artifacts may persist as files, database views, vector indexes, or programs. The model chooses their organization and can revise it as needs change.

### The user constitution

The constitution is an explicit, inspectable, versioned record of the user's access and sharing instructions. It specifies what may be reused, which tasks or purposes may receive it, exclusions and project boundaries, and preferences about the representation of context.

For each query, the memory agent determines what is relevant within that permitted scope and what to return: for example, source evidence, a summary, structured state, or resolvable references. These are possible representations, not a compulsory menu.

The constitution's enforceable restrictions are protected policy, not notes the memory agent can rewrite to grant itself permission. Authorized user changes update it. Enterprise entitlements and information barriers remain an independent upper bound; user instructions can further restrict sharing but cannot expand enterprise authority.

The memory agent applies these settings; runtime checks enforce them on reads and disclosure. Explicit exclusions override inferred usefulness. A model's decision to summarize or redact material does not by itself authorize release.

### No task-side bypass

Task agents receive a memory-query interface rather than unrestricted store tools or credentials. Requests include the task identity, purpose, scope, and desired context. The runtime binds those fields to the actual task and caller; prose claiming a broader identity or purpose does not grant access.

Returned packages carry source dependencies, sharing restrictions, memory versions, and the constitution revision used. Further retrieval of personal evidence or referenced memory goes through the same interface. A reference is not a capability to bypass it.

On a timeout or unavailable memory service, the task can use an explicitly permitted prior package, continue with limited context, or pause. It cannot fall back to direct personal-memory access.

## 3. Durable work: execute both paths reliably

Checkpoint memory-maintenance jobs, context requests, task plans, waits, retries, and commitments independently of model context. Event subscriptions and timers wake authorized work. Cancellation, expiry, and changed permissions apply to both memory and task operations.

Asynchronous updates commit versioned results. A synchronous query reads a consistent permitted version and reports its freshness. If the task requires a particular update, it can wait for that dependency instead of treating unfinished consolidation as complete. Long maintenance jobs should not block unrelated queries.

Durability does not guarantee exactly-once external effects. Use stable operation IDs, idempotency where supported, and reconciliation for ambiguous outcomes. Unsafe retries pause for resolution. Rebuilding a memory view or context never re-executes external effects.

Deleting a commitment from a note does not cancel the operational responsibility. Cancellation requires an explicit runtime state transition. Recovery rechecks current authority and constitution settings before continuing.

## 4. Skills: reusable memory and task procedures

Versioned skills can implement retrieval programs, consolidation, verification, context presentation, and task workflows. The memory agent can select and develop procedures through the same governed tool interfaces.

Local artifact edits are ordinary execution. Promoting a procedure for reuse is a separate step: verify its evidence, preserve its information boundaries, and evaluate it before deployment, initially with human review. Sharing a skill across users requires explicit clearance; abstraction alone does not prove that it contains no confidential information.

## 5. Context compiler: materialize what this task may see now

The compiler combines the memory agent's permitted response with task state, authorized skills, and protected policy. It checks current restrictions, budget, versions, and source eligibility before materializing the next model input.

The task model can edit or reorganize the approved working view and decide when more context is needed. Personal-memory access remains mediated by the memory agent. Editing context cannot change permissions or relabel provenance, which remain runtime metadata.

“Compiled” describes materialization and validation, not a fixed relevance policy. The memory agent controls selection and representation; the task model can adapt the working set it has been permitted to use.

Compaction may use summaries when omitted evidence remains recoverable under retention policy. Keep a permitted source version or snapshot when audit reconstruction requires the original observation. Log the compiled context or a reconstructable manifest, integrity hashes, model and program versions, policy revisions, and resulting operations. A hash cannot reconstruct missing content or hidden model reasoning.

## Separate storage, query operations, and memory policy

| Concern | System contract | Adaptable choices |
| --- | --- | --- |
| **Storage: how to persist** | Commit, recovery, and retention semantics. | SQLite, JSONL, database or object-store adapters. |
| **Access: how to operate and query** | Governed tool interfaces available to the memory agent. | File operations, SQL, text and semantic search, vector operations, reference resolution. |
| **Memory: what to organize and surface** | Timeline grounding, provenance, constitution, and enterprise boundaries. | Model-chosen structures, queries, maintenance procedures, and context representations. |

The task agent does not inherit the memory agent's store access. The model can choose among storage and query capabilities exposed to its role, while the underlying evidence and operational-state contracts remain stable.

[Pi Durable](https://earendil.com/posts/pi-durable/) supplies precedents for checkpointed tool execution, typed application documents, and interchangeable storage backends. [Context Language Models](https://arxiv.org/html/2609.37725v1) support giving models control over editable working context. The user-representing memory agent and constitution-mediated sharing are the enterprise design proposed here.

## Enterprise constraints

**No amount of accumulated memory increases authority.**

| Constraint | Required behavior |
| --- | --- |
| **Changed constitution** | Apply current restrictions to new requests; invalidate or rebuild active context packages affected by changed sharing settings. |
| **Retention and deletion** | Propagate through source copies, indexes, derived artifacts, and cached context, subject to legal holds and approved audit policy. |
| **Revoked access** | Invalidate affected artifacts and context packages, clear dependent serving caches, and stop or re-scope affected work. |
| **Information barriers** | Preserve source restrictions through summaries and reusable procedures. Transformation alone does not authorize disclosure. |
| **Untrusted content** | Keep provenance outside editable text. Retrieved instructions cannot change the constitution or create capabilities. |
| **Auditability** | Retain permitted inputs or references, task identity, versions, results, operation IDs, policy decisions, and approvals sufficient to establish an action's basis. |

## Build and evaluate

| Stage | Build | Exit criterion |
| --- | --- | --- |
| **Durability** | Evidence timeline, checkpointed work, event ingestion, and governed connectors. | Work survives failures and model changes; tested effects are neither lost nor duplicated, and ambiguous outcomes are resolved or paused. |
| **Memory and context** | Async maintenance tools, sync context service, constitution enforcement, and context compilation. | Test retrieval usefulness, representation quality, freshness, latency, and cost. Verify exclusions, purpose boundaries, policy changes, revocation, and the absence of task-side bypass. |
| **Proactivity** | Standing intents, lifecycle controls, and notification policy. | Measure relevant developments surfaced and missed alongside unnecessary interruptions; honor cancellation and changed scope. |
| **Learning** | Candidate memory procedures and skills mined from verified traces. | Reviewed changes improve held-out outcomes within sharing, safety, and cost bounds and support rollback. |

Compare memory organizations, retrieval programs, context representations, and maintenance strategies under matched tasks and budgets. Worker placement and model choice can vary; the user-representing memory interface and its enforced access boundary remain part of this design.
