# Personal Timeline: Connector Event Primitives

*Capture contract for the timeline in the [architecture](architecture.md).*

The timeline is a compact ledger of relevant interactions across Outlook email, Calendar, OneDrive, Word, and LLMSuite. Each primitive is a **bounded, attributed natural-language statement**, accompanied by structured metadata for identity, time, source retrieval, work relationships, and governance. Full emails, documents, and conversations remain in their source systems and are retrieved through authorized connector tools, including MCP, when needed.

The capture vocabulary specifies evidence available to the memory agent; it does not prescribe how the agent organizes derived memory. An interaction need not belong to a task. LLMSuite includes ordinary conversation and one-off assistance as well as explicit delegation and durable execution.

## Record contract

| Field | Capture requirement |
| --- | --- |
| Identity | Internal event ID, schema version, connector, interaction kind, source event ID when available, and deduplication key. Interaction kinds aid ingestion and filtering; the statement carries the meaning. |
| Statement | Short natural-language description of what happened, with explicit attribution and material dates, conditions, or changes. Do not replace missing facts with inferred certainty. |
| Timeline scope | Tenant and timeline owner. The employee whose timeline holds the record need not be the actor. |
| Time | Occurred time and observed/ingested time; preserve timezone, precision, and uncertainty. For meetings, retain the scheduled interval separately. |
| People | Stable actor and relevant participant references, with roles such as sender, recipient, organizer, reviewer, or assignee. |
| Source locator | Connector/account/container, object ID, version or change token where available, and message/turn/comment/section locator. Display URLs are supplementary. |
| Related work | Conversation, thread, meeting, artifact, project, account, task, or responsibility references when known. Task IDs are optional. Distinguish explicit links from inferred associations. |
| Evidence | Supporting source/event references and basis: connector-observed, explicitly reported, or extracted from content. Preserve whether a statement is proposed, stated, inferred, disputed, or withdrawn. |
| Lineage | Supporting events, corrections and supersession links; extraction method/version for model-produced statements. |
| Governance | Access, sensitivity and retention policy references. References do not grant access; current permissions are checked during retrieval and disclosure. |
| Capture reason | Why this interaction was retained: relevant correspondence, meeting preparation, user selection, ongoing responsibility, material correction, etc. |

Connector observations and extracted work statements are distinct linked records. A selected email may produce a receipt record and a commitment record, each supported by the same message. Multiple sources can support one assertion without creating multiple commitments.

## Outlook email

| Natural-language primitive | Information to retain beyond the common envelope |
| --- | --- |
| “[Person] sent me / I sent [person] a message about [topic].” | Message and thread IDs, direction, relevant sender/recipient roles, bounded subject/topic, reply-to reference where available, related work. |
| “[Person] asked [person] to [action], by [date or condition].” | Requester, requested actor if specified, bounded action, deliverable reference, deadline/condition as stated. |
| “[Person] committed to [action], by [date or condition].” | Committing actor, beneficiary if relevant, promised action, deadline/condition, supporting message locator. |
| “[Person] approved, rejected, decided or proposed [item], subject to [conditions].” | Explicit disposition, actor, target and version if applicable, scope, conditions, effective date if stated. |
| “[Person] reported [progress, blocker or outcome] for [work].” | Attributed report, affected work, dependency or outcome, supporting message locator. Keep reported and verified outcomes distinct. |
| “[Person] shared [artifact] in connection with [work].” | Attachment or linked artifact references, sender, related work, supporting message. |

Capture selected work-relevant correspondence, not every received message. Receipt does not establish reading; a reply does not establish agreement. A request is not a commitment and a proposal is not a decision. Resolve full bodies, surrounding threads, and attachments through Outlook tools when needed.

## Calendar

| Natural-language primitive | Information to retain beyond the common envelope |
| --- | --- |
| “I am scheduled to meet [people] about [purpose] at [time].” | Meeting occurrence ID, series reference if recurring, organizer, relevant participants, purpose, scheduled interval/timezone. |
| “[Person] accepted, declined or tentatively accepted [meeting].” | Responding person, previous/new RSVP when available, response time, meeting reference. |
| “[Meeting] moved from [old time] to [new time], or its [purpose/participants] changed.” | Meaningful changed fields and old/new values, occurrence/series scope, meeting reference. |
| “[Meeting] was cancelled.” | Meeting reference, cancellation time, actor and reason when available. |
| “I attended [meeting], according to [attendance signal or explicit report].” | Participation basis, observed interval if available, supporting evidence reference. |
| “[Meeting] is linked to [task, responsibility or artifact].” | Related work/artifact references and evidence for the association. |

An invitation or RSVP is not attendance. Decisions and commitments require linked notes, communications, or explicit reports; the calendar entry alone does not establish them. Retrieve agendas, invite details, and linked notes on demand.

## OneDrive

| Natural-language primitive | Information to retain beyond the common envelope |
| --- | --- |
| “[Person] created [artifact] for [work].” | File identity, bounded name/type, actor/time when available, related work if known. |
| “[Artifact] acquired a new version.” | File identity, previous/new version references where available, actor/time when available. |
| “[Person] shared [artifact] with [person/group].” | Relevant sharing scope or grantee, access level when available, artifact reference. |
| “Access to [artifact] changed or was revoked.” | Artifact reference, affected scope, observed access change and effective time if known. |
| “[Artifact] was moved, renamed or deleted.” | Stable file identity, relevant old/new name or location, or deletion status. |

OneDrive records artifact lifecycle and collaboration. A version change does not establish a substantive change in meaning. Coalesce edit bursts; retrieve file content, permissions, and available version history on demand.

## Word

| Natural-language primitive | Information to retain beyond the common envelope |
| --- | --- |
| “[Person] revised [section] of [document] to [bounded description of change].” | Document/version references, section anchor, actor if known, substantive change statement and supporting evidence. |
| “[Person] commented on [section], asking or proposing [change].” | Comment/thread ID, author, section anchor, bounded request/proposal. |
| “[Person] replied to [review discussion] with [position or clarification].” | Reply/thread ID, attributed statement, related review issue. |
| “[Person] resolved [comment or review issue].” | Comment reference, resolver/time when available; explanation only when evidenced. |
| “[Person] recorded [decision or commitment] in [document].” | Attributed statement, decision maker or responsible actor, applicable scope/date/condition, precise evidence locator. |

Capture meaningful revisions and review interactions, not formatting changes or autosaves. Comment resolution is not necessarily approval. OneDrive and Word records share artifact/version identity: lifecycle and substantive interaction may both be useful, but should not duplicate the same observation. Retrieve relevant passages, comments, and available revisions on demand. If semantic changes cannot be established, record only the file version change.

## LLMSuite conversations and assistance

LLMSuite is a conversational assistant as well as an agent interface. **Do not convert every prompt into a task, every conversation into memory, or every draft into an external action.**

| Natural-language primitive | Information to retain beyond the common envelope |
| --- | --- |
| “The user discussed [topic] and clarified [point].” | Conversation/turn references and a bounded statement only when relevant to continuity. Casual or unrelated exchanges need not enter the work timeline. |
| “The user asked for a summary of [email/document].” | Bounded request, source references, conversation/turn reference; substantive follow-up instructions or corrections when relevant. |
| “The agent drafted a response to [person/thread] about [topic].” | Request, target/source references, draft reference, producing turn. Draft wording remains proposed, not communicated. |
| “The user explored [question] using [sources]; the agent produced [result].” | Bounded question, source references, result/artifact reference. Attribute conclusions to their author and evidence basis. |
| “The user clarified or corrected [instruction/claim] to [new meaning].” | Target request/claim/output reference, bounded clarification/correction, supporting turn. |
| “The user preferred [approach] or stated [sharing constraint] for [scope].” | Attributed instruction, precise scope, supporting turn, resulting settings revision if authorized and applied. Do not generalize local feedback into a global preference. |
| “The user accepted, rejected or revised [result].” | Result/version reference, explicit disposition, bounded feedback, supporting turn. |

Conversation and artifact links exist independently of task IDs. An agent proposal does not become a user preference. A sharing instruction is evidence for an authorized settings change; it does not itself bypass the settings mechanism or enterprise policy. Acceptance of a draft does not establish that it was sent.

## LLMSuite delegation and durable tasks

These primitives apply only when a responsibility is explicitly delegated or a durable task actually exists.

| Natural-language primitive | Information to retain beyond the common envelope |
| --- | --- |
| “The user delegated responsibility for [scope], under [conditions].” | Authorizing instruction, responsibility ID, scope, success/end condition, standing triggers and notification instructions, applicable settings references. |
| “The agent accepted [task] within [responsibility].” | Task ID, parent task/responsibility if any, scope, owner, authorization, success condition, deadline if specified. |
| “The agent completed [step]; [remaining work] is next.” | Task/run ID, bounded progress, completed output references, next step, durable checkpoint reference. |
| “[Task] is waiting for [event, person, dependency or approval].” | Wait/dependency ID, unblock condition, timeout if applicable, approval request reference where relevant. |
| “[Event] satisfied the waiting condition for [task].” | Task/wait IDs and matching event reference. |
| “The agent attempted [action] on [target], with [outcome].” | Operation ID, task/run, tool/action and target reference, approval reference if required, receipt, confirmed/failed/uncertain outcome. |
| “[Task] completed, failed, was cancelled or changed scope.” | Prior/new state, reason, result/evidence references, authorizing instruction for scope changes. |

The timeline records execution history. Authoritative task state, checkpoints, triggers, and recovery remain in the durable runtime. Detailed traces and tool outputs are referenced rather than copied into the personal timeline. An uncertain external outcome remains uncertain until reconciled.

## Selection, retrieval, and maintenance

1. **Select candidate interactions.** Use user scope, relevant correspondence, meeting participation/preparation, explicit subscriptions, selected artifacts, and responsibilities. Ordinary conversation can qualify without a task.
2. **Extract only material information.** Fetch source content selectively when needed to identify a request, commitment, decision, correction, or substantive change. Retain the compact attributed statement and precise references, not the entire source.
3. **Avoid redundant capture.** Deduplicate connector redelivery, skip bulk notifications and formatting-only changes, and coalesce edit bursts. Track source evidence independently of model extraction versions.
4. **Resolve detail when needed.** The memory agent uses references to retrieve source content under current permissions in both synchronous query answering and asynchronous maintenance. Missing, changed, or inaccessible evidence is reported explicitly.
5. **Maintain lineage.** Corrections, deletion, revoked access, and retention changes invalidate affected derived memory. Model consolidation or pruning does not independently authorize deletion of timeline evidence.

If exact historical wording must remain reconstructable, retain the permitted versioned excerpt as separate evidence under an explicit policy. A source locator alone does not guarantee historical content remains available.

Maintain connector coverage records separately: subscribed scope, cursor/watermark, last successful synchronization, filter version, and known gaps. Track source availability changes as well. Absence from a filtered or incomplete timeline is not evidence that an interaction never occurred.

## Example: assistance becomes responsibility only when delegated

| Interaction | Timeline record |
| --- | --- |
| “Summarize this email.” | Source-linked assistance request, if relevant to continuity. No durable task required. |
| “Draft a reply saying I can deliver Friday.” | Drafting interaction and proposed commitment, linked to the email and draft. |
| Reply actually sent through Outlook. | Sent-message observation and supported communicated commitment. Do not infer sending from draft acceptance. |
| “Track this and remind me before Friday.” | Explicit ongoing responsibility, linked to the commitment and durable runtime record. |

The memory agent can connect these interactions without conflating conversation, proposed intent, communicated commitments, and authorized ongoing work.
