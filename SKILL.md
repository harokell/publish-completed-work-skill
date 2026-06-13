---
name: publish-completed-work
description: Publish or synchronize completed Codex skills and completed project work to GitHub before final handoff. Use at the end of any task where Codex creates or updates a skill, completes or updates a project, produces a repo-ready artifact, or makes changes that should be preserved in GitHub. For skills, use a GitHub repository name ending in -skill while keeping the local skill folder named after the actual skill. On later updates, sync the same GitHub repository instead of creating a duplicate.
---

# Publish Completed Work

## Overview

Use this skill as a completion gate. Before the final response, publish newly completed skills or projects to GitHub, or synchronize updates to the correct existing repository.

## Completion Gate

Run this workflow after the main task is complete and validation has passed, but before final handoff to the user.

Use it when any of these are true:

- A Codex skill was created, updated, installed, or validated.
- A project, prototype, app, repository, script package, or reusable artifact was completed or updated.
- The user asked for GitHub publication, sync, backup, sharing, or repository management.
- The work created files that should be preserved beyond the local task output folder.

Do not run this workflow for pure chat, analysis-only answers, throwaway scratch work, or local-only changes the user explicitly does not want published.

## Repository Naming

### Skills

For a skill named `<skill-name>`, publish it to a GitHub repository named:

```text
<skill-name>-skill
```

Keep exactly one `-skill` suffix. If the skill name already ends with `-skill`, do not append another suffix.

Keep the local install folder and `name:` field in `SKILL.md` as `<skill-name>`, not `<skill-name>-skill`, unless the skill itself is intentionally named with that suffix.

For a skill repository, make the repository root directly installable:

```text
<skill-name>-skill/
  SKILL.md
  agents/openai.yaml
  scripts/        # optional
  references/     # optional
  assets/         # optional
```

### Projects

For projects, use the project slug or existing repository name. Do not add `-skill` to non-skill projects.

If the project already has a Git remote, sync that repository unless the user requests a new target.

## Publish Workflow

1. Identify the completed artifact: skill folder, project folder, repository, or generated package.
2. Validate before publishing:
   - For skills, run the skill validator when available.
   - For projects, run the relevant tests, build, lint, or smoke checks when practical.
3. Determine the GitHub target:
   - Use an explicit user-provided owner/repo when present.
   - Otherwise infer from the current git remote, authenticated GitHub account, or established user preference.
   - If the owner or visibility is ambiguous, ask before creating a repository.
4. Check whether the target repository already exists.
5. If it exists, update it in place. Do not create duplicates.
6. If it does not exist, create it using the naming rules above.
7. Publish only the intended final files:
   - Include skill files and bundled resources for skills.
   - Include project source, docs, config, tests, and required assets for projects.
   - Exclude local caches, credentials, `.env` files, private tokens, generated temp folders, `.DS_Store`, `node_modules`, virtual environments, build caches, and unrelated task output folders.
8. Commit with a concise message describing the completed work or update.
9. Push or write the repository update.
10. Verify the remote files after publishing.
11. Report the repository URL and validation status in the final response.

## Update Workflow

When a previously published skill or project changes:

- Locate the existing GitHub repository first.
- Preserve the same repository name unless the user requests a rename.
- Update changed files rather than creating a new repository.
- Re-run validation after local changes and before syncing.
- Verify the remote content after syncing.

## Confirmation And Safety

Publishing to GitHub is an external side effect. Proceed when the user has clearly asked for ongoing publication or synchronization, as in a standing instruction or this skill's invocation.

Ask for confirmation when:

- Creating a repository under an ambiguous owner or visibility.
- Making a private repository public.
- Force-pushing, deleting, archiving, renaming, or transferring a repository.
- Publishing files that might contain secrets, private data, credentials, proprietary data, or unrelated user files.
- Installing dependencies or running downloaded code only to support publication.

Never publish secrets, tokens, credentials, browser cookies, private keys, `.env` files, or hidden configuration unless the user explicitly identifies the file as safe to publish.

## Interaction With Task Output Folders

Use `task-output-folder` for local staging when no explicit destination is provided. Do not create generic `work/`, `scratch/`, or `staging/` folders in the current directory.

Do not publish the whole task output folder by default. Publish only the final skill or project files that belong in the GitHub repository.

## Final Response Pattern

Include:

- The GitHub repository URL.
- Whether this was a new repository or an update.
- What was published or synchronized.
- Validation checks performed.
- Any files intentionally excluded for safety.
