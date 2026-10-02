# From Personal Timeline to a Durable Personal Agent

*Lambert Mathias · October 1, 2026*

## The product thesis

Evolve an enterprise assistant such as LLM Suite into a personal agent that carries delegated responsibilities across days, applications, and model changes.

> **“Own my preparation for this account and keep me current.”**

The agent maintains the account's working picture, follows relevant developments, tracks unresolved commitments, and prepares the employee before upcoming meetings. It continues authorized work while the employee is offline. When they return, they can see what changed, what is complete, what is blocked, and what needs their attention.

This requires three capabilities beneath the experience: evidence that remains available across sessions, work that can resume after interruption, and memory and context that adapt to the task and the model. Permissions remain independently enforced.

> **The timeline is evidence. Memory is computed from it. Work is durable. Context is compiled. Authority is external.**

The earlier Personal Timeline idea asked how an agent remembers across time. The product proposed here asks how it remains responsible across time. **Always-on means persistent responsibility.**

## What the employee experiences

An employee delegates an account, project, or recurring responsibility with a scope: which sources matter, what the agent should maintain, when to bring something to their attention, and which actions are permitted.

For an account-preparation responsibility, the agent might begin by assembling the current picture and outstanding questions. A revised document arrives the next day; it updates the relevant analysis and identifies a discrepancy. Before a meeting, it prepares a brief grounded in the new evidence and prior decisions. Afterwards, it records agreed follow-ups and carries them forward. The employee can correct its understanding, redirect it, or pause the responsibility at any point.

The product keeps this work inspectable. The employee can ask why an item matters, recover the evidence behind a conclusion, and see what the agent has done. A new conversation continues the responsibility rather than requiring the employee to reconstruct its history.

Personal continuity also matters across responsibilities. A working preference or prior decision may be useful to a new task, but the employee's entire history should not accompany every request. The agent selects relevant context under the user's sharing instructions and enterprise permissions. The employee can specify what may be reused, what stays within a project, and what should not be shared with a particular task.

Background work should usually update the working picture quietly. The agent interrupts when a material development needs attention or input. The employee controls the responsibility's scope, ongoing activity, and notification behavior.

## The technical bet

The state accumulated through work should persist independently of any particular model or conversation: evidence, decisions, ongoing tasks, commitments, and authorization records. That gives future models a basis for continuing work and revisiting earlier interpretations.

Memory and context strategies should remain adaptable. The model can choose how to organize permitted evidence, maintain useful artifacts, and construct its working set through tool interfaces. Files, databases, and vector stores offer capabilities it can use and combine; a fixed taxonomy of preferences, episodes, and facts need not define how it remembers.

“Context is compiled” means that the next model input is constructed from available state and evidence under current permissions. It leaves room for the model to choose and edit its working view. The product depends on continuity and appropriate sharing; it does not depend on one storage backend, retrieval method, or agent topology.

[Pi Durable](https://earendil.com/posts/pi-durable/) provides a precedent for separating durable execution and application state from model context. [Context Language Models](https://arxiv.org/html/2609.37725v1) provide evidence for letting models manage their live working context. Combining these ideas motivates a system with durable responsibilities and evolving memory strategies. Their extension to long-term enterprise personal agents remains a design and evaluation question.

A [separate architecture note](docs/architecture.md) describes one possible implementation, including agentic memory tools, asynchronous maintenance, scoped context delivery, storage and query interfaces, recovery, and enterprise controls.

## What success looks like

The employee can delegate a responsibility once, then return to work that has continued coherently. Relevant developments are incorporated, commitments remain visible, and useful preparation arrives without repeated prompting. Corrections and changes in scope take effect across subsequent work.

The agent reduces preparation effort and missed commitments without creating a stream of unnecessary interruptions. Its conclusions are traceable to evidence, its activity is inspectable, and accumulated memory never increases its authority.

## North star

A personal enterprise agent carries work forward without making the employee reconstruct it each morning. The enterprise preserves continuity and control while improving models keep changing how the agent reasons, organizes memory, and uses context.
