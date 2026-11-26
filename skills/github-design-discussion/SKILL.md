---
name: github-design-discussion
description: Examine a required GitHub PR or issue for design decisions and blind spots, discuss tradeoffs interactively, recommend useful research, and record agreed outcomes or deferred questions in comments and linked subissues. Use for design clarification rather than routine code fixes or review-comment implementation.
version: 2.0
---

# GitHub Design Discussion

Turn a concrete PR or issue into a shared, evidence-backed design record. A session can finish with questions resolved, provisional, or explicitly deferred; do not force certainty.

## Establish the source

A PR or issue is required. Resolve an unambiguous reference from the request or current context; otherwise ask for its URL or repository and number before starting the design interview. Do not substitute a free-floating plan. Record the canonical URL and repository. Preserve the user's scope, constraints, and existing decisions.

Use available GitHub connector tools, or `gh` when appropriate. Check read access early and write access when publishing becomes relevant. Read the description, comments, linked context, and relevant review threads. For a PR, inspect the current diff and enough surrounding code and tests to understand its behavior. For an issue, inspect relevant repository context when accessible. Distinguish unavailable evidence from evidence you checked; do not invent repository facts.

## Discover decisions and blind spots

Identify consequential decisions, their prerequisites, and assumptions. Ground findings in the source discussion or code, with links where useful. Look for gaps in intended behavior, user needs, interfaces, data ownership, compatibility, failures, operational constraints, and acceptance criteria when relevant. Scale the investigation to the change; avoid a generic exhaustive audit.

Maintain a compact decision ledger in the conversation: stable ID, question, dependency, evidence, options and recommendation, status, rationale, and follow-up link when available. Statuses are open, agreed, provisional, or deferred. Clearly distinguish existing agreements, facts, inferences, and your recommendations. Reopen an agreed choice only when new evidence or a conflict warrants it, and explain why.

## Discuss interactively

Favor an ongoing back-and-forth with the user over a one-way assessment or a long questionnaire. Work through the decision tree in manageable rounds. Ask questions whose prerequisites are settled; hold dependent questions for later. Number questions with stable IDs. Explain the material tradeoffs and give a recommended answer with reasoning when evidence supports one. Allow alternatives and uncertainty rather than framing agreement as the only acceptable response. When interactive prompt tools are available, use them for focused preference and decision questions; otherwise ask directly in the conversation.

Wait for the user's answers before treating choices as agreed. Recompute dependent questions after each round and continue the exchange as needed. Investigate discoverable facts yourself; ask the user about preferences, priorities, constraints, and judgments. Continue independent investigation while an answer is pending when useful. Do not implement changes merely because the discussion identifies them.

## Recommend and conduct research

Suggest targeted research when additional evidence could materially change a choice, particularly for software or service selection, unfamiliar capabilities, changing pricing or limits, compatibility, security claims, or performance assumptions. Explain the decision it informs, the uncertainty to resolve, and a proportionate research scope or experiment. Ask for missing constraints such as budget or hosting requirements when they affect the comparison.

Use current primary sources for changing claims, cite findings, and distinguish documented facts from inference and untested claims. Compare options against the user's actual criteria rather than generic popularity. Undertake bounded research within the authorized discussion scope; get direction before substantial, paid, or scope-expanding research. The user may decide now, request research, or defer. Research recommendations are not settled decisions.

## Defer questions into subissues

Offer a linked subissue when the user does not know, wants time, or needs research or another person's input. An open question need not prevent wrapping up. State what it blocks, what can proceed, and any explicitly agreed temporary assumption. Do not invent an owner, deadline, or default choice.

Before creating anything, check for an existing issue that already tracks the question. Prepare a concrete title and body with the parent context, precise question, known evidence, options and tradeoffs, proposed research, decision criteria, dependencies, and completion condition. Create or link it only with user authorization; a request to create the proposed subissues supplies that authorization without another confirmation.

Use native parent/subissue relationships when supported and appropriate. If the parent is a PR or native relationships are unavailable, use an ordinary issue with explicit links to the PR or issue and describe it accurately as a linked follow-up. Never claim a native relationship that was not created. If writing is unavailable, provide the ready-to-post draft and explain the limitation.

## Wrap up and record

Wrap up when consequential questions are agreed, explicitly provisional, or deferred with a next step, and the user confirms the shared understanding. Summarize unresolved blockers candidly; deferral does not imply implementation readiness.

Prepare a concise authoritative decision comment for the original PR or issue. Include only relevant sections:

- Scope and source revision when relevant.
- Agreed decisions and rationale, including meaningful rejected alternatives.
- Provisional choices and their revisit conditions.
- Deferred questions, linked issues, blockers, and next steps.
- Acceptance criteria and supporting evidence or research links.

Authority comes from accurately recording the user's agreements, not presenting the agent's suggestions as consensus. Do not claim approval from absent stakeholders or label the design approved merely because the interview ended.

Show the concrete comment before publishing unless the user already authorized posting the resulting record. Obtain authorization for external writes when it has not already been given; preserve existing authorization and do not request it twice. Before posting, refresh the relevant discussion and PR revision to detect material changes. Reconcile changes that invalidate decisions rather than silently publishing stale conclusions.

Prefer updating this workflow's existing decision comment when authorized and identifiable. Do not overwrite someone else's comment or unrelated text. If a write times out or has an uncertain result, read back before retrying to avoid duplicate comments or issues. Verify successful writes and relationships, and return their URLs. A completed draft must never be reported as published.
