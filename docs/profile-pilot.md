# First Implementation: LLMSuite Work Profile

*Execution plan for a chat-only pilot of [agentic memory](architecture.md), using the [LLMSuite timeline primitives](timeline-events.md#llmsuite-conversations-and-assistance).*

## Objective and deliverable

Build a **/profile command** that turns an employee's authorized LLMSuite chat history into an editable work-context card. The user can inspect supporting conversations, compare two presentations, correct individual claims, and choose which confirmed information may be reused.

The pilot delivers an end-to-end loop:

**Chat history → compact evidence → candidate work profile → user review → versioned, approved memory.**

This tests whether useful personal memory can be grounded in ordinary chat and improved through feedback. It does not require every conversation to be work-related or an agentic task. It does not implement always-on execution or the full memory-agent architecture.

## How this advances the personal agent

The larger goal is an agent that remembers relevant work and continues authorized responsibilities across time. **/profile starts with the first part: establishing what the assistant understands about the employee, making that understanding inspectable, and letting the employee control its reuse.**

The card is an interface to derived memory, not the final memory architecture. It exposes a small working model of the user for correction. Its sections should not become mandatory categories for all future memory.

| Pilot capability | Contribution to the larger system | What remains untested |
| --- | --- | --- |
| Source-linked chat evidence | First connector and timeline ingestion path, including ordinary assistance. | Coverage of actual work across email, calendar and documents. |
| Derived profile claims | First memory projection grounded in evidence rather than an opaque summary. | Maintaining project state, commitments and relationships across sources. |
| User corrections and exclusions | Feedback becomes new evidence; reuse settings are an initial, limited part of the user constitution. | Full task boundaries, information barriers and delegated authority. |
| Versioned refresh and invalidation | Establishes how memory changes without discarding user corrections. | Long-running maintenance at enterprise scale. |
| Task-scoped reuse experiment | Tests whether selected approved memory improves a later response. | A general memory agent choosing context across many tasks. |

A convincing card alone does not establish a useful personal agent. The progression is:

1. **Understand me:** generate, inspect and correct the work card.
2. **Help me without repeated setup:** retrieve relevant approved claims for a new conversation and measure the benefit.
3. **Keep my work context current:** propose updates from new chats, then selected enterprise connector events.
4. **Carry a responsibility forward:** add explicit delegation, durable task state and event-driven resumption around a proven workflow.

Each step answers a different question. Do not use profile approval as evidence that the user wants autonomous action. If role/context claims add little value but assistance preferences help, ship that narrower memory first. If chat evidence misses important work, the next investment may be connector coverage rather than richer profile inference.

## 1. History window and activity cohorts

Start with **four weeks of history**. Extend to eight weeks for sparse users; return a partial card when evidence remains thin. The user can review the window, exclude conversations, or proceed with less history. Four weeks is a pilot default to test, not an established minimum.

Measure activity over all authorized conversations before content selection, then measure work-relevant evidence separately:

| Measure | Definition |
| --- | --- |
| Active days in 7 / 28 days | Distinct local calendar days with at least one user message. Count a day once, regardless of turn volume. |
| Active weeks in 28 days | Number of seven-day intervals containing user activity. |
| Conversation breadth | Distinct conversations containing retained work evidence. |
| Evidence density | Supported profile claims and independent conversations supporting them. |
| Recency | Age of the latest supporting evidence for each claim. |

DAU and MAU describe population adoption. For individual profiling, use active-day counts and distribution across weeks. Segment the pilot using initial thresholds:

| Cohort | Initial definition | Profile behavior |
| --- | --- | --- |
| Frequent | At least 12 active days in 28 days. | Use four weeks; sample across weeks and conversations. |
| Regular | 4–11 active days in 28 days. | Start with four weeks; extend to eight if evidence is insufficient. |
| Sparse / new | 0–3 active days in 28 days, or less than four weeks available. | Use up to eight weeks of available history; show a partial card and ask for missing context. |

For the pilot, call a profile broadly supported only when relevant evidence spans **at least five conversations, three active days, and two weeks**. This is a coverage label, not a requirement to run /profile or permission to fill every field.

Compare two-, four-, and eight-week windows offline for factual accuracy, user corrections, useful coverage, stale claims, latency and cost. Choose the shortest window that preserves useful coverage. High message volume alone is not evidence of profile quality.

## 2. What the card contains

Replace the wearable example with fields chat history can support:

| Section | Candidate content | Evidence rule |
| --- | --- | --- |
| Work context | Role, team, domain and responsibilities. | State a job title or responsibility only when the user explicitly identifies it or later confirms it. Questions about a role do not establish ownership. |
| Recurring work | Writing, analysis, coding, research, presentation preparation and similar workflows. | Describe how the user uses LLMSuite; do not equate chat frequency with their overall job mix. |
| Current focus | Repeatedly referenced initiatives, questions or deliverables. | Mark as current and dated; distinguish participation from ownership. Avoid copying confidential project detail unnecessarily. |
| Expertise and interests | Domains the user states expertise in, and topics they repeatedly explore. | Keep expertise separate from interest. Asking a technical question does not establish expertise. |
| Assistance preferences | Tone, response length, formats, depth, iteration and use of evidence. | Prefer explicit instructions and repeated corrections; retain task-specific scope. |
| Tools and collaboration context | Tools, artifacts or audiences the user explicitly associates with their work. | Do not infer tool usage, communication-channel percentages or meeting patterns from mentions alone. |

Leave unsupported fields absent or ask the user. Do not infer age, health, stress, working hours, productivity, completion rates, environmental preferences, or actual workplace activity from chat timestamps or generated text. Do not publish a single numerical confidence score.

A card might read:

> **Work context:** You describe your role as a software developer.  
> **Recurring LLMSuite use:** You use the assistant for code review, technical explanation and drafting project updates.  
> **Assistance preference:** You have asked for concise explanations with concrete examples.  
> **Needs confirmation:** Are these recurring uses representative of your responsibilities, or mainly occasional help?

These are illustrative statements, not facts about a real user.

## 3. /profile experience

1. **Choose history.** Show the date range and available coverage; allow exclusions. Recheck authorization before reading source content.
2. **Generate candidates.** Extract compact, attributed evidence per conversation, then synthesize claims across conversations. Keep source conversation/turn references.
3. **Show two cards.** Card A emphasizes role, responsibilities and recurring work. Card B emphasizes workflows and how the assistant can help. Use the same supported claims, with different emphasis and organization; randomize display order in the pilot.
4. **Collect feedback.** Offer “A,” “B,” “combine,” “neither,” plus per-claim confirm, edit, remove and “do not remember.” Allow new user-provided facts.
5. **Review reuse.** Show the final card and which fields may be reused for future work assistance. Save only on an explicit user action. Default to preview-only until then.
6. **Inspect and refresh.** A later /profile shows the saved card, provenance and proposed changes. New evidence must not silently overwrite user corrections or expand sharing scope.

Choosing a card records a **presentation preference**, not confirmation of every factual claim. If two interpretations are genuinely plausible, label the uncertainty and ask a targeted question rather than constructing two fictional identities.

## 4. Minimal implementation

| Component | Responsibility |
| --- | --- |
| History adapter | Fetch authorized user-scoped conversations with stable turn IDs, timestamps, deletion status and pagination/cursor support. |
| Evidence extractor | Identify relevant requests, corrections, explicit self-descriptions and preferences. Produce bounded natural-language records with source references. |
| Profile synthesizer | Deduplicate related evidence, separate stated facts from observed usage, flag contradictions, and produce candidate claims. |
| Card renderer | Produce the two presentations from the same claim set and expose per-claim evidence and controls. |
| Feedback handler | Save selection, edits, exclusions, confirmation and reuse settings independently. |
| Profile store | Persist immutable profile revisions, evidence dependencies and feedback events; support deletion and invalidation. |

Use one extraction pass per selected conversation and one synthesis pass over the compact records. Bound input size, distribute sampling across time, and report any omitted coverage. Cache extraction by source version plus extractor version; changed or excluded content invalidates dependent results. Storage can start with database records and JSON payloads behind these interfaces.

Use the user's own instructions and feedback as evidence. Assistant outputs are context, not proof about the user. Pasted emails, examples, fictional personas, role-play and third-party descriptions must not become autobiographical claims. Treat historical content as data, not as instructions to the extractor.

### Stored objects

| Object | Required fields |
| --- | --- |
| Evidence record | ID, owner, natural-language statement, interaction kind, conversation/turn references, occurrence time, evidence basis, extraction version and policy references. |
| Candidate claim | ID, section, statement, scope, supporting evidence IDs, status, first/last supported time and conflicting claim references. Status distinguishes explicitly stated, repeated usage, tentative interpretation, user-confirmed and user-edited. |
| Profile revision | ID, owner, schema/model/prompt versions, history window, coverage, claim IDs, draft/saved status and creation time. |
| Feedback event | ID, profile revision, actor/time, card presentation choice or targeted claim action, old/new value where applicable. |
| Reuse setting | Profile/claim scope, allowed purpose, exclusions, settings revision and authorizing user action. |

Keep evidence status, user confirmation and permission to reuse as separate fields. A user edit is a new attributed source. Excluding a source removes its dependent influence; “do not remember” records an exclusion so regeneration does not immediately recreate the claim.

The saved card is derived memory. Feedback and corrections become timeline evidence. Any future task-side use remains subject to the mediated memory interface and external authority; saving a card does not grant direct access to personal history.

## 5. Execution sequence

| Step | Work | Concrete deliverable |
| --- | --- | --- |
| 1. Data contract | Confirm authorized history access, source identity, exclusions and cohort measurement. | Chat adapter contract; synthetic fixtures; coverage report; reviewed work-card schema. |
| 2. Offline baseline | Implement extraction, synthesis and the two renderings. Compare history windows on permitted evaluation data. | Batch runner; source-linked candidate cards; error analysis by activity cohort. |
| 3. Interactive pilot | Connect /profile, evidence inspection, field editing and explicit save/reuse controls. | Working command; versioned profile store; feedback event log; exclusion and deletion behavior. |
| 4. Evaluate and refine | Review quality and feedback; run a bounded approved-memory reuse experiment; revise thresholds and prompts. | Pilot report; recommended history policy; measured downstream benefit; documented failure cases and next implementation decisions. |

Assign ownership for the chat adapter, extraction/evaluation, command UI and profile persistence before step 1. Access to authorized chat history is the dependency; start with synthetic fixtures while that integration is prepared. The command cannot be called complete on offline card generation alone.

## 6. Product questions and evaluation

Evaluate **profile quality** and **the value of using that profile** separately.

| Product question | Evidence to collect | Decision it informs |
| --- | --- | --- |
| Does the card capture what matters about my work? | Per-claim correctness and relevance; important omissions; user-added context; differences by claim type and activity cohort. | Which fields to keep, ask about, or omit. |
| Is LLMSuite history representative enough? | Users distinguish “what I ask the assistant” from “what my job involves”; compare history windows and work-domain coverage. | Whether chat-only memory is sufficient or another connector is needed. |
| Does the user want this remembered? | Confirmed claims allowed for reuse versus excluded claims; rejection reasons such as incorrect, irrelevant, too personal, or too specific to a project. | Memory scope and defaults. |
| Is review understandable and worth the effort? | Review completion, time and edits; whether users can locate evidence, remove a claim and understand its future use. | Review UX and whether to shorten the card. |
| Which presentation helps? | A/B preference, review time, missing-information reports and per-claim corrections, with display order randomized. | Card organization; not which facts are true. |
| Does memory improve subsequent assistance? | Output usefulness, repeated context entry, follow-up corrections and time to an acceptable result, compared with no memory. | Whether to enable ongoing reuse and which claim types help. |
| Does personalization become intrusive or overfit? | Irrelevant references to the user, wrong-context assumptions, stale project details and user requests to disable reuse. | Task relevance filtering, expiry and reuse controls. |
| Can the profile stay current? | Retention of user corrections across refresh; stale-claim rate; success correcting a changed role or preference. | Refresh policy and the next maintenance capability. |

### Evaluation design

**A. Offline grounding.** Use a reviewed, authorized sample stratified by frequent, regular and sparse/new users. Compare two-, four-, and eight-week windows. Review source attribution, third-party misattribution, contradictions, omissions and stale claims. Independently assess sampled claims, including ones users leave untouched; lack of correction does not establish accuracy.

**B. Interactive card review.** Measure field-level feedback, review effort, approved reuse and preference between presentations. Separate presentation choice from factual confirmation. Include interviews or short targeted questions about what is missing and what should not be remembered. A high save rate alone is insufficient.

**C. Bounded downstream reuse.** After explicit approval, test a small set of ordinary work-assistance scenarios: drafting, explanation and analysis. Compare three conditions:

- **No profile:** the current experience.
- **Approved preferences only:** relevant tone, depth and formatting instructions.
- **Relevant approved work context plus preferences:** selected claims appropriate to the request.

Use the same model and source access across conditions. In offline paired comparisons, use held-out later requests not used to construct the profile, blind response order, and obtain user ratings where personal relevance matters. A live test can randomize users to conditions to reduce carryover from earlier personalized responses. Do not insert the whole card into every prompt.

The primary product outcome is **less repeated setup with equal or better task usefulness**. Track explicit background/preference restatements, user corrections, time to an acceptable result, and rated usefulness. Track factual quality, irrelevant personalization, latency and cost alongside the benefit. Preference-only gains must not be attributed to deeper work understanding.

### Acceptance and next-step decisions

Required functional checks: generated claims have resolvable evidence; source/owner isolation is enforced; drafts and third-party text are not misattributed; unsupported fields remain empty; user edits survive refresh; card selection does not confirm all claims; exclusions, deletion and changed permissions remove affected influence before subsequent use. Test sparse histories, conflicting statements and a changed role.

Set numerical quality and benefit targets after the offline baseline and before the interactive/reuse experiments. Report results by cohort and claim type. Logging must distinguish source evidence, profile revisions, feedback actions and the specific claims supplied to an evaluated response.

- **Proceed to ongoing personal memory** when approved reuse improves assistance and users can inspect, correct and constrain it.
- **Narrow to assistance preferences** if work-context claims add no measurable benefit or introduce more errors.
- **Add a selected connector** if users consistently identify material work context absent from chat.
- **Test ongoing responsibility next** only as a separate delegation experiment with durable execution and event-driven follow-up. Neither persona accuracy nor increased usage proves readiness for it.

The final deliverable is **a working /profile command, an editable evidence-backed card, a versioned feedback and reuse record, a bounded downstream reuse experiment, and a report recommending the next increment toward a personal agent**.
