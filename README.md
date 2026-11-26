# Skills

Personal agent skills for Codex and GitHub Copilot.

## Available skills

| Skill | Purpose |
| --- | --- |
| [github-design-discussion](skills/github-design-discussion/README.md) | Explore design decisions and blind spots in a PR or issue, research useful options, and record agreed outcomes. |
| [screenshot-upload](skills/screenshot-upload/README.md) | Attach screenshots to pull request descriptions with GitHub CLI, using Google Cloud Storage only when CLI attachment is unavailable. |

Each skill lives in `skills/<skill-name>/`. Its `SKILL.md` contains agent instructions; its README explains usage and installation.

## Installation

Clone this repository, then follow the installation instructions in the skill's README. Skills provide instructions; GitHub access, research tools, and permissions come from your agent environment.
