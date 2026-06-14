# Publish Completed Work Skill

Publish Completed Work is a reusable workflow for AI agents and maintainers who want completed skills or projects to be preserved in GitHub only after the user has accepted the current version.

## What It Solves

This skill prevents two common release mistakes:

- publishing a skill or project before the user has confirmed that the current version is acceptable,
- publishing a local, platform-specific skill folder as if it were a general-purpose public package.

It also requires public skill repositories to include a useful README instead of an empty or platform-only placeholder.

## Package Contents

```text
publish-completed-work-skill/
  README.md
  SKILL.md
```

There are no required scripts, assets, or external dependencies.

## How To Use

Give `SKILL.md` to an AI agent or maintainer as the publication checklist for completed skill and project work.

Run the workflow only after:

1. the skill or project has been completed and validated, and
2. the user explicitly confirms that this version is acceptable or asks for GitHub publication.

For skills, publish a generic package that a person can understand after downloading the repository. Do not publish local platform metadata by default.

## Safety Notes

- Never publish secrets, tokens, credentials, cookies, private keys, `.env` files, or unrelated user files.
- Do not create duplicate repositories for later updates; sync the existing repository.
- Do not publish an empty README or a README that only describes one agent platform's installation path.
- Keep platform-specific adapters separate from the main package unless the user explicitly asks to include them.
