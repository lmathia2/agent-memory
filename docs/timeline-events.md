# Personal Timeline: Connector Event Primitives

*Capture contract for the timeline in the [architecture](architecture.md).*

The timeline is a compact ledger of **key interactions and work transitions** across Outlook email, Calendar, OneDrive, Word, and LLMSuite conversations and tasks. It records enough to identify what happened, connect related work, and retrieve supporting detail. Full emails, documents, and conversations remain in their source systems.

## What to capture

Capture events within the user's work scope, using authorized signals exposed by each connector.

| Connector | Key primitives | Compact timeline payload | Detail retrieved on demand |
| --- | --- | --- | --- |
| **Outlook email** | Relevant message sent/received, reply, request, commitment or approval | Message/thread IDs, sender and recipients, time, subject/topic, interaction type; brief attributed work statement when material | Email body, surrounding thread, attachments via Outlook MCP |
| **Calendar** | Meeting created, accepted, rescheduled or cancelled; participation when evidenced | Event ID, title/purpose, organizer and participants, interval, changed fields, related work references; participation basis if available | Invite, agenda, attachments and linked notes |
| **OneDrive** | Relevant file created, shared, updated or deleted | File ID, version reference, name/type, actor, action, time, related task/conversation | File content, permissions and available version history |
| **Word** | Document authored/revised, comment added/replied to, review or resolution recorded | Document/version ID, section or comment reference, actor, action; brief substantive change or review outcome | Relevant passages, comments and available revisions |
| **LLMSuite chat / conversations** | User request, clarification, correction, explicit decision or commitment, artifact produced | Conversation/turn IDs, actor, topic, brief request or attributed statement, artifact/task links | Relevant turns and tool-result references |
| **LLMSuite tasks** | Responsibility accepted, progress checkpoint, waiting/blocked, approval requested, completed, failed or cancelled | Task/operation IDs, transition, owner, scope, due date/dependency if applicable, result reference | Durable task state, execution trace and outputs |

OneDrive identifies the file lifecycle; Word identifies meaningful interactions within a document. Link overlapping events through the same artifact identity and version rather than duplicating the content.

A calendar acceptance is an RSVP, not proof of attendance. An email receipt is not proof of reading or agreement. Capture attendance, document consultation, or review only when a signal or explicit report supports it. Attribute extracted decisions and commitments to their source; keep proposals and inferences distinct.

## Common event fields

- **Identity and time:** event ID/type/version, source object/event ID, occurred time and ingestion time, deduplication key.
- **Work context:** actor, relevant participants, project/account/task links, thread or conversation ID.
- **Compact evidence:** interaction or transition, changed fields or bounded statement, source/version/section reference, observed/reported/extracted basis.
- **Governance and lineage:** access and retention policy references, supporting events, corrections and superseding events.

Store connector-resolvable identifiers, not just display URLs. References identify evidence; they do not grant access.

## Selection and retrieval

Admit interactions tied to participation, owned work, explicit subscriptions, or standing responsibilities. Retain changes that affect preparation, decisions, ownership, dependencies, or follow-up. Skip bulk mail, duplicate notifications, autosaves and formatting-only changes; coalesce edit bursts.

**Do not ingest the mailbox or document corpus into the timeline.** Metadata can identify candidate interactions; selectively fetch content when needed to extract a key statement. Persist only the compact event and references by default.

For “What did we agree with Sam?”, the memory agent locates relevant timeline entries, retrieves the necessary thread through Outlook MCP under current permissions, and returns query-relevant context to the task agent. The same references support asynchronous memory maintenance.

If exact historical wording must remain reconstructable, retain only the permitted versioned excerpt needed as separate evidence. Otherwise report when the original source is unavailable or has changed. Corrections, deletion and revoked access propagate to dependent memory.

Task transitions are evidence of execution; authoritative task state remains in the durable runtime. Capture filters and connector coverage are inspectable, so absence from the timeline is not mistaken for absence of activity.
