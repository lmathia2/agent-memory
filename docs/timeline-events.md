# Personal Timeline: Digital Work Events

*Event contract for capability 1 in the [architecture](architecture.md).*

The personal timeline records **the user's involvement in work and the material changes needed to carry it forward**: who they met, what they discussed, which artifacts they used or changed, what was decided, who accepted responsibility, and what happened next. It is a durable work ledger, not an archive of every connected application.

The digital equivalents of wearable observations are **people engaged with, discussions participated in, artifacts interacted with, and responsibilities advanced**. Capture observable events and attributed statements; the memory agent computes their significance.

## Four distinct records

| Record | Contains |
| --- | --- |
| **Timeline** | Selected events, compact payloads, evidence references, and provenance. |
| **Source content** | Emails, transcripts, documents, slides, pages, and issue histories in their source systems; permitted versioned excerpts or snapshots when required. |
| **Derived memory** | The memory agent's summaries, relationships, working views, and interpretations, with dependencies on evidence. |
| **Durable task state** | Accepted agent responsibilities, execution state, waits, approvals, and retries. A timeline entry records a transition; it does not replace authoritative runtime state. |

These are logical boundaries, independent of files, databases, or vector stores. Both synchronous memory queries and asynchronous maintenance use the same timeline. Full content is resolved through governed tools when needed.

## Core event primitives

Use a small, extensible vocabulary of **work events**, rather than one schema per connector or a fixed taxonomy of memory.

| Primitive | Examples | Minimum useful payload |
| --- | --- | --- |
| **Meeting lifecycle / participation** | Scheduled, rescheduled, cancelled; attendance observed or reported | Meeting reference, topic, organizer/participants, interval, lifecycle change; attendance evidence when available |
| **Communication / discussion** | Message sent or received; reply, substantive comment, meeting discussion captured | Thread/session reference, speaker, audience, bounded topic or statement, reply/evidence links |
| **Artifact interaction** | Authored, reviewed, annotated, presented, or consulted | Artifact and version reference, actor, interaction, relevant section/slide; observed or reported basis |
| **Artifact change** | Document published, slide revised, wiki section changed | Artifact/version references, material change or bounded diff, author, relation to ongoing work |
| **Work-item transition** | Assigned, accepted, blocked, dependency changed, completed, reopened | Work-item reference, changed fields, owner, status, due date or blocker where relevant |
| **Work assertion** | Proposal, decision, commitment, request, approval, reported outcome | Attributed statement, assertion kind, scope, owner/due date if stated, supporting event/span, verification status |
| **Agent execution** | Responsibility accepted; operation attempted, succeeded, failed, or left ambiguous | Task/operation ID, authorized scope, effect/result reference, outcome and continuation dependency |
| **Correction / control change** | User corrects a claim; source deleted; access, retention, or sharing setting changes | Target event/source/policy, changed constraint or superseding evidence, effective time, invalidation scope |

Communication and artifact events describe activity. Work assertions describe what a source **says** that activity established. For example, a discussion can contain a proposed deadline without an accepted commitment. Record that distinction.

Event types are transport contracts; the memory agent remains free to organize the evidence into whatever views the model finds useful. Extend payloads for new work signals without prescribing cognitive categories.

## Connector mapping

The following are capture candidates, conditional on available authorized signals—not claims that every connector exposes every interaction.

| Connector | Admit to the timeline | Resolve from source when needed |
| --- | --- | --- |
| **Calendar** | Relevant meeting lifecycle, participants and purpose; links to related work | Invite details and attachments. Acceptance establishes RSVP, not attendance. |
| **Outlook** | Relevant sent/received messages and replies; attributed requests, commitments, decisions | Message bodies and attachments. Delivery does not establish reading or agreement. |
| **OneDrive / PowerPoint** | Relevant artifact versions, sharing/review activity, substantive slide changes | Files, individual slides, comments and version content. Presence in storage does not establish use or presentation. |
| **Internal wikis / Confluence** | Relevant publication, revision, discussion or review events; changed decision/procedure references | Pages, sections, comments and version history. Capture consultation only when observed or explicitly reported. |
| **Jira** | Relevant ownership/status/deadline/dependency changes, blockers, resolution and substantive comments | Full descriptions, attachments and histories. “Done” is a source status, not independent verification of success. |

Meeting attendance and spoken discussion need an additional authorized signal, such as an attendance record, transcript, notes, or user report. Record its basis. Missing signals remain unknown.

## Event envelope

Every event carries:

- **Identity:** stable event ID, schema/type version, source object/version/event ID, and deduplication key.
- **Time:** occurred/effective time and observed/ingested time; preserve intervals and uncertainty.
- **Participants and scope:** actor/speaker, relevant participants, user involvement, project/account/work-item links.
- **Evidence:** source reference and section/span, compact payload, capture method, trust, and extractor version when used.
- **Relations:** thread/correlation IDs, supports/responds-to/supersedes links, task and operation IDs where applicable.
- **Governance:** classification, access/retention policy references, and source dependencies used for current authorization and invalidation.

An extracted assertion references its supporting source observation. Preserve attribution and whether it is explicit, reported, or inferred. Model confidence does not turn an inference into a verified fact. User confirmation is new evidence; correction normally appends a superseding event.

## Admission and content budget

1. **Select work scope.** Admit events tied to the user's participation, owned work, explicit subscriptions, or standing responsibilities. Access to a source alone is insufficient.
2. **Select material change.** Capture developments that affect preparation, understanding, ownership, dependencies, decisions, or follow-up. Coalesce edit bursts; skip autosaves, formatting noise, duplicate notifications, and irrelevant bulk content.
3. **Retain a compact record.** Prefer changed fields, a bounded statement/diff, and precise references. Fetch source content temporarily for extraction; ingestion does not imply permanent full-content retention.
4. **Preserve necessary evidence.** Where later reconstruction matters, retain a permitted versioned excerpt or snapshot in a separate evidence store. A URL or hash cannot recover overwritten content. Otherwise mark unavailable evidence and qualify dependent answers.
5. **Apply policy before persistence and reuse.** Scope admission using user settings within enterprise authority. Treat sensitive payloads and even metadata as governed data.

Selection must be inspectable and adjustable. Keep connector cursors, filters, and coverage gaps in ingestion state; do not represent a selectively captured timeline as exhaustive. Support authorized backfill when scope changes. Start with explicit participation, owned work, and subscriptions; model-assisted selection can evolve with measured omission rates.

Deduplicate source events before extraction. Correlate related observations across connectors without discarding their distinct provenance. Apply replay-safe ingestion and retention/deletion propagation to evidence copies and derived dependencies.

## Example: carrying a review forward

A calendar event records a scheduled launch review. An attendance record later establishes participation. Meeting notes attribute “Sam will revise slide 8 by Friday” to Sam; extraction creates a supported commitment claim. A linked Jira item changes owner and due date. A new PowerPoint version records the relevant slide revision. A reviewer message records approval; Jira subsequently records completion.

These are linked events, not copies of the meeting, mailbox, issue history, and deck. The memory agent can answer “What is still outstanding?” by reconciling the claims and transitions, retrieving the cited source spans when necessary. “Sam usually handles launch messaging” remains a derived interpretation, not a new historical fact.

## Acceptance checks

Verify that the ledger distinguishes RSVP from attendance, receipt from agreement, proposals from commitments, and reported completion from verified outcomes. Test duplicate delivery, out-of-order events, edit coalescing, corrections, missing source versions, changed scope, deletion and revoked access. Measure missed material developments, attribution/extraction accuracy, evidence reconstruction, and bytes retained per useful event.
