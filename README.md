# Publish Completed Work Skill

Publish Completed Work is a reusable workflow for publishing or synchronizing user-approved skills and completed projects to GitHub.

## What It Solves

It prevents premature publishing and keeps repositories organized:

- publish only after the user confirms the current version is acceptable,
- update existing repositories instead of creating duplicates,
- use `-skill` repository names for skill packages,
- publish only generic, README-backed skill packages,
- verify remote files and repository descriptions after syncing.

For skill repositories, run `prepare-generic-skill` first so the published package is platform-neutral and safe to share.

## Package Contents

```text
publish-completed-work-skill/
  README.md
  SKILL.md
```

There are no required scripts, assets, or external dependencies.

## How To Use

Give `SKILL.md` to an AI agent after a skill or project has been completed and the user has explicitly confirmed that version should be published or synchronized.

The agent should:

1. verify that user confirmation is present,
2. for skills, confirm a generic package has already been prepared,
3. determine whether the target GitHub repository exists,
4. create or update the correct repository,
5. exclude local-only files, caches, credentials, and adapter metadata,
6. verify remote files and repository description after publication.

## Safety Notes

- Never publish secrets, credentials, tokens, environment files, cookies, private keys, or private runtime state.
- Never infer publication approval from silence or successful local validation.
- For public skill repositories, keep the README, skill instructions, and repository About text platform-neutral.
- For updates, preserve the existing repository unless the user requests a rename or transfer.