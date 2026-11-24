# GitHub Design Discussion

Turn a GitHub PR or issue into a shared design understanding and a clear decision record.

Use this skill before or during implementation when a proposal has unanswered questions, hidden assumptions, or consequential tradeoffs. It requires a concrete PR or issue; it is not a general brainstorming session or an automatic code-fixing workflow.

## How it works

1. **Read the context.** Examine the description, existing discussion, relevant code and tests, and the diff for a PR. Respect decisions already made.
2. **Find decisions and blind spots.** Identify assumptions, alternatives, and dependencies. Separate observed facts from inferences and recommendations.
3. **Discuss in rounds.** Ask manageable groups of questions, with recommendations and reasoning. Settle prerequisites before dependent choices. The user makes the decisions.
4. **Research when useful.** Recommend targeted investigation when evidence could change a choice, such as selecting a service or checking compatibility, pricing, or limits. Compare options against actual constraints and cite current sources. Substantial or paid research needs direction.
5. **Defer without forcing certainty.** Track unanswered questions as provisional or deferred. With authorization, create or reuse a subissue or linked follow-up containing context, options, research needs, decision criteria, and blockers.
6. **Record the outcome.** Confirm shared understanding, then prepare an authoritative comment distinguishing agreed decisions, provisional choices, and open questions. Post or update it when authorized and return verified links.

The conversation maintains stable question IDs and a compact decision ledger. A session can finish with explicitly deferred questions; this does not imply that blocked implementation is ready to proceed.

For issue parents, native subissue relationships are used when supported. PR follow-ups use linked issues. The skill does not invent stakeholder approval, owners, deadlines, or consensus, and does not implement code changes as a side effect of discussion.

## Requirements

- An interactive agent supporting `SKILL.md` skills, such as Codex or GitHub Copilot agent mode.
- A PR or issue URL, or an unambiguous repository and number.
- GitHub read access through connected tools or an authenticated `gh` CLI. Posting comments and creating issues require appropriate write access and user authorization.
- Web research tools when current external evidence is needed. Missing tools or permissions should be reported; a draft can still be produced when writes are unavailable.

The skill contains instructions, not credentials or an integration. It does not grant permissions or install research tools. Use an interactive chat for the interview rather than expecting an unattended code review to conduct it.

## Install

Run these commands from the root of this cloned repository. Copy the entire skill folder so its metadata stays with it.

### Codex on Windows (PowerShell)

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.codex\skills" | Out-Null
Copy-Item -Recurse ./skills/github-design-discussion "$env:USERPROFILE\.codex\skills/"
```

### Codex on Linux or WSL

```bash
mkdir -p ~/.codex/skills
cp -r skills/github-design-discussion ~/.codex/skills/
```

If your installation uses a custom `CODEX_HOME`, put the folder in its `skills` directory instead. This skill was created and verified in a Codex installation using `~/.codex/skills`.

### GitHub Copilot on Windows (PowerShell)

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.copilot\skills" | Out-Null
Copy-Item -Recurse ./skills/github-design-discussion "$env:USERPROFILE\.copilot\skills/"
```

### GitHub Copilot on Linux or WSL

```bash
mkdir -p ~/.copilot/skills
cp -r skills/github-design-discussion ~/.copilot/skills/
```

For a WSL session, install inside WSL. For a local Windows session, install on Windows. A remote environment needs the skill in its own filesystem.

### Repository-scoped Copilot installation

Copy the skill into the target repository as `.github/skills/github-design-discussion/`, then commit it to share with collaborators and make it available to repository-based agents. Copilot uses `SKILL.md`; `agents/openai.yaml` supplies Codex-specific interface metadata.

See the current [Copilot installation documentation](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/add-skills) and [Codex skills documentation](https://developers.openai.com/codex/skills/) for supported locations and client behavior.

## Use

In Codex:

```text
Use $github-design-discussion on https://github.com/OWNER/REPO/issues/123.
```

In GitHub Copilot agent chat, including VS Code:

```text
Use the github-design-discussion skill on https://github.com/OWNER/REPO/pull/456.
```

You can steer the conversation naturally:

- "Compare the hosting options against a small monthly budget."
- "I cannot decide this now; create a linked research issue for that question."
- "That choice is provisional until we benchmark it."
- "We agree on the summary; post the decision record."

Explicit instructions can authorize the corresponding external writes. Starting a discussion alone does not authorize posting or creating issues.

## Files

- [SKILL.md](SKILL.md): reusable agent workflow.
- [agents/openai.yaml](agents/openai.yaml): Codex display name and suggested invocation.

## Design influences

The workflow draws on the dependency-aware interview structure in [Matt Pocock's grilling skill](https://github.com/mattpocock/skills/blob/main/skills/productivity/grilling/SKILL.md) and the existing-discussion inspection approach in [OpenAI's gh-address-comments skill](https://github.com/openai/skills/blob/main/skills/.curated/gh-address-comments/SKILL.md). It adds a required PR or issue, targeted research, explicit deferral, and an agreed decision record.
