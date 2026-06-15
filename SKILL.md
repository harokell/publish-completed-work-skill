---
name: publish-completed-work
description: Publish or synchronize user-confirmed generic skill packages and completed project work to GitHub. Use only after an agent creates or updates a skill or project and the user explicitly confirms that this version is acceptable, meets requirements, needs no further changes for now, or should be published or synced. For skills, run prepare-generic-skill first, then publish the resulting agent-agnostic GitHub package with a useful README and generic SKILL.md, use a repository name ending in -skill, and keep platform-specific metadata local unless the user explicitly wants adapter files. On later updates, sync the same GitHub repository instead of creating a duplicate.
---

# Publish Completed Work

## Overview

Use this skill as a confirmation-gated publication workflow. Publish newly completed skills or projects to GitHub, or synchronize updates to the correct existing repository, only after the user accepts the current version.

For skills, publish a generic skill package for broad reuse. The local source may be platform-specific, but the GitHub repository should not read like an install artifact for only one agent product.

## Pre-Publish Generic Conversion Gate

For any skill publication or skill repository update, use `prepare-generic-skill` before this workflow writes to GitHub.

The expected input to this workflow is the prepared generic package, not the local platform-specific skill folder. If `prepare-generic-skill` has not been run yet, stop and run it first, then return here after the user confirms the prepared package is acceptable.

## Confirmation Gate

Run this workflow only when both gates are satisfied:

1. The work gate is satisfied: the main task is complete and validation has passed.
2. The user-confirmation gate is satisfied: the user explicitly confirms that the current version is acceptable.

Treat these user responses as confirmation:

- "可以", "确认", "确认执行", "就这样", "这一版可以", "达到要求了", "暂时不需要修改".
- "上传到 GitHub", "同步 GitHub", "发布", "更新仓库", or equivalent explicit publication instructions.
- A clear statement that the skill or project is finished enough to preserve remotely.

Do not infer confirmation from the agent's own validation, the absence of requested changes, or a normal final handoff. If the user has not confirmed, summarize the local result and wait for confirmation instead of touching GitHub.

Do not run this workflow for pure chat, analysis-only answers, throwaway scratch work, or local-only changes the user explicitly does not want published.

## Repository Naming

### Skills

For a skill named `<skill-name>`, publish it to a GitHub repository named:

```text
<skill-name>-skill
```

Keep exactly one suffix. If the skill name already ends with that suffix, do not append another one.

Keep the local install folder and `name:` field in `SKILL.md` as `<skill-name>`, not `<skill-name>-skill`, unless the skill itself is intentionally named with the suffix.

For a skill repository, make the repository root directly understandable and reusable:

```text
<skill-name>-skill/
  README.md
  SKILL.md
  scripts/        # optional
  references/     # optional
  assets/         # optional
```

Do not publish platform adapters, hidden runtime folders, plugin cache files, local install paths, or other agent-specific metadata as part of the default public skill package. If the user explicitly wants platform adapters, put those files under an adapter-specific path and keep the main README and SKILL.md platform-neutral.

### Projects

For projects, use the project slug or existing repository name. Do not add the skill suffix to non-skill projects.

If the project already has a Git remote, sync that repository unless the user requests a new target.

## Generic Skill Package

When publishing a skill that was built locally for one agent environment, prepare a clean generic package before writing to GitHub:

1. Treat the local skill as the source, not as the exact public package.
2. Keep `SKILL.md` as agent-facing instructions, but rewrite platform-specific wording to generic terms such as "agent", "AI agent", "assistant", or "workflow" unless a platform-specific detail is unavoidable.
3. Include all reusable resources required by the skill, such as `scripts/`, `references/`, and `assets/`.
4. Exclude agent-specific metadata and runtime state by default:
   - platform adapter files
   - hidden runtime folders
   - plugin cache files
   - local absolute install paths
   - desktop or session notes
5. Add or update `README.md` as the human-facing entry point.
6. Verify that a person can download the repository and understand how to use the skill without already knowing one specific agent platform.

The public README may mention a specific product only as an optional compatibility note when useful. It must not make that product the only supported use path.

## Repository Metadata Gate

For public skill repositories, set and verify the GitHub repository About or description metadata as part of publication.

Before publishing or updating a public skill repository:

- Set a concise, platform-neutral repository description.
- Do not use wording that makes the public package sound tied to one agent platform.
- Prefer descriptions such as "Generic workflow for ..." or "Reusable AI-agent workflow for ...".
- Verify the rendered GitHub About section or repository metadata after saving.

Repository metadata is separate from files such as `README.md` and `SKILL.md`; updating the files is not enough.

## README Quality Gate

Every published public skill repository must include a non-empty `README.md`.

Before publishing or updating a skill repository:

- If `README.md` is missing, create it.
- If `README.md` exists but is empty or placeholder-only, replace it.
- Include at minimum:
  - the skill name and purpose,
  - what problem it solves,
  - the files included in the package,
  - generic usage instructions for an AI agent or human maintainer,
  - any safety notes, exclusions, or setup requirements.
- Do not publish an empty README, a one-line placeholder, or a README that only explains one platform-specific installation path.

For project repositories, use the same rule when the repository is public or meant to be shared: do not leave an empty README in place.

## Publish Workflow

1. Identify the completed artifact: skill folder, project folder, repository, or generated package.
2. For skills, confirm the artifact is the output of `prepare-generic-skill` and is already a generic package with `README.md`, `SKILL.md`, and only reusable resources.
3. Validate before publishing:
   - For skills, run the skill validator when available.
   - For projects, run the relevant tests, build, lint, or smoke checks when practical.
4. Confirm the user accepted this version or explicitly asked to publish or sync it.
5. Determine the GitHub target:
   - Use an explicit user-provided owner and repository when present.
   - Otherwise infer from the current git remote, authenticated GitHub account, or established user preference.
   - If the owner or visibility is ambiguous, ask before creating a repository.
6. Check whether the target repository already exists.
7. If it exists, update it in place. Do not create duplicates.
8. If it does not exist, create it using the naming rules above.
9. Publish only the intended final files:
   - Include skill files and bundled resources for skills.
   - Include project source, docs, config, tests, and required assets for projects.
   - For skills, publish the generic package, not the local platform-specific folder verbatim.
   - Exclude local caches, credentials, environment files, private tokens, generated temp folders, system metadata files, dependency folders, virtual environments, build caches, platform-specific metadata, and unrelated task output folders.
10. Commit with a concise message describing the completed work or update.
11. Push or write the repository update.
12. Verify the remote files after publishing, including `README.md` and `SKILL.md` for skill repositories.
13. Verify the GitHub About or description metadata for public skill repositories.
14. Report the repository URL and validation status in the final response.

## Update Workflow

When a previously published skill or project changes:

- Locate the existing GitHub repository first.
- Preserve the same repository name unless the user requests a rename.
- Update changed files rather than creating a new repository.
- Re-run validation after local changes and before syncing.
- Wait for the user's explicit confirmation that the updated version is acceptable.
- Verify the remote content after syncing.

## Confirmation And Safety

Publishing to GitHub is an external side effect. Proceed only after the user confirms the current skill or project version is acceptable, or explicitly asks to publish or sync it.

Ask for confirmation when:

- The current version has not yet been accepted by the user.
- Creating a repository under an ambiguous owner or visibility.
- Making a private repository public.
- Force-pushing, deleting, archiving, renaming, or transferring a repository.
- Publishing files that might contain secrets, private data, credentials, proprietary data, or unrelated user files.
- Installing dependencies or running downloaded code only to support publication.

Never publish secrets, tokens, credentials, browser cookies, private keys, environment files, or hidden configuration unless the user explicitly identifies the file as safe to publish.

## Interaction With Task Output Folders

Use a task-local output folder for local staging when no explicit destination is provided. Do not create generic scratch or staging folders in the current directory.

Do not publish the whole task output folder by default. Publish only the final skill or project files that belong in the GitHub repository.

## Final Response Pattern

Include:

- The GitHub repository URL.
- Whether this was a new repository or an update.
- What was published or synchronized.
- Validation checks performed.
- For skills, whether the published package is generic and README-backed.
- For public skill repositories, whether the GitHub About or description is platform-neutral.
- Any files intentionally excluded for safety.

If the current version has not been confirmed yet, do not include a GitHub publication result. Instead, state that the local work is ready for review and that GitHub sync will happen after the user confirms this version is acceptable.