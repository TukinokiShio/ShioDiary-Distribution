---
name: shiodiary-github-connectivity
description: >-
  Quickly create a private GitHub diary repository or connect ShioDiary to an
  existing GitHub repository, then safely diagnose or perform an explicitly
  requested diary push or pull. Use for ShioDiary GitHub setup, connection,
  backup, restore, authentication, or sync errors. Never expose diary contents
  or credentials, and never overwrite divergent data.
compatibility: Git for Windows and GitHub CLI are optional. Browser-based setup is supported.
version: 0.2.0
---

# ShioDiary GitHub Connectivity

## Goal

Finish with the exact ShioDiary data folder connected to one HTTPS
github.com repository whose fetch and push targets match. For a new repository,
make it private. Verify the final branch and working-tree state.

The diary data folder is separate from the ShioDiary source and distribution
repositories. ShioDiary stores entries under
YYYY/MM/YYYY-MM-DD/ (diary.md, meta.json, and supported attachments).
Supported attachment types are JPG, JPEG, PNG, GIF, WebP, MP3, M4A, WAV,
OGG, WebM, and AAC.

## First-principles fast path

Ask only for facts that cannot be read safely:

1. Use ShioDiary's configured diary folder when it is available; otherwise ask
   for the exact folder. Never guess from the current working directory.
2. Ask one target question if needed: create a private repository (ask for its
   name and use the authenticated account by default), or connect an existing
   repository (ask for its HTTPS URL).
3. Collect tool availability and repository state with read-only inspection
   when safe. Ask only for a fact that cannot be inspected or remains ambiguous.
4. Show one short preflight with the exact folder, account/repository,
   visibility, aggregate file count/size, and planned writes. Ask once before
   any local or remote mutation (including clone, git init, stage, commit,
   repository creation, or push) unless the user already authorized that exact
   target and write. Do not make a change before this approval.
5. Perform the shortest safe route, then verify the result. Do not turn routine
   execution details into questions.

## Privacy and write boundaries

- Treat diary text, titles, tags, dates, attachment names, and metadata as
  private. Do not open diary files, print filenames, quote status paths, or send
  diary data to web search or unrelated services. User-facing summaries contain
  aggregate counts only.
- Use only the exact configured or user-selected diary folder and the exact
  repository the user selected. Confirm that **git rev-parse --show-toplevel**
  resolves to that folder before operating on a repository.
- Keep GitHub passwords and tokens inside the normal sign-in UI and credential
  manager. Never request, display, copy, log, or place credentials in a URL,
  command, script, environment variable, or ShioDiary file.
- A private GitHub repository restricts access; it does not encrypt ordinary
  Markdown, metadata, images, or audio stored in Git history.
- Use one origin with a single HTTPS github.com fetch URL and the same single
  push repository. Do not accept credentials in the URL, SSH remotes, multiple
  push URLs, another host, or a source/distribution repository.
- Stage only validated ShioDiary date-folder files. If the folder contains
  unrelated or unsupported files, stop and summarize the count without listing
  their names.
- Never force-push, reset, rebase, merge unrelated histories, discard files,
  or resolve conflicts automatically. Stop on divergence, conflicts, an
  unexpected repository root, or an unverifiable remote.
- Do not install Git, GitHub CLI, extensions, or Skills unless the user asks
  for that installation. GitHub CLI is optional.
- Pass process arguments separately when the tool supports it. Quote the exact
  path with the shell's native mechanism; do not build shell commands from
  untrusted path text.

## Fast workflow

### 1. Read-only preflight

- Confirm the selected folder exists and is the ShioDiary data folder. Do not
  enumerate it to the user.
- Check **git --version**, **gh --version**, and **gh auth status** if
  available. Never ask for or print an authentication token.
- If Git exists, inspect the repository root, current branch, origin fetch and
  push targets, clean/dirty counts, and ahead/behind counts. Capture status
  output internally and report counts only; never show raw path output.
- Count supported files and total bytes without opening them. Stop if a file is
  100 MiB or larger; do not add Git LFS automatically.
- For an existing GitHub target, use gh repo view with the fields
  nameWithOwner, visibility, and isEmpty when available. Verify the exact
  selected repository, its privacy, and whether it has a branch. If GitHub CLI
  is unavailable, ask the user to confirm visibility in GitHub's web interface.

### 2. Create a new private repository

Use this route only when the user selected a new repository:

1. Resolve the owner from the authenticated GitHub account unless the user
   selected an organization. Ask only for the repository name if it is missing.
2. Present one preflight naming the exact private repository and all planned
   local and remote writes, including whether initial diary files will be
   pushed. Wait for confirmation unless those exact writes were already
   requested.
3. If gh is installed and signed in, create the empty private repository with
   **gh repo create OWNER/NAME --private**. Supplying the name and visibility
   makes the creation non-interactive and avoids accidental public/default-file
   setup of the web flow.
4. Confirm the local Git root is exactly the diary folder. If the folder is not
   already a repository, initialize it on main. If it is already a repository,
   preserve its current branch and history; stop if it is an unrelated project
   or has an unexpected remote.
5. Validate all pending paths against ShioDiary's dated-file layout without
   displaying the names. Stage only those validated paths. If there are no
   diary files yet, use an empty initialization commit rather than inventing
   unrelated files.
6. Add https://github.com/OWNER/NAME.git as origin only when no conflicting
   remote exists, then push the current branch and set its upstream.

If gh is unavailable, ask the user to create an empty private repository in the
browser and provide its HTTPS URL. Do not initialize it with a README, license,
or .gitignore; then continue with the same local preflight.

### 3. Connect an existing repository

- If the local folder is already the exact repository and its fetch/push URLs
  both point to the selected HTTPS GitHub repository, verify the connection.
- If the local folder is not a Git repository and the selected remote is empty,
  the new-private flow's local initialization and first push are safe after the
  one preflight.
- If the local folder is empty and the selected remote already has a branch,
  clone that exact repository into the selected folder, then verify the root.
- If local diary data and a populated remote have separate or unknown histories,
  stop without initializing, pulling, or copying over either side. Offer the
  user the safe choice of selecting a fresh clone in ShioDiary or reviewing a
  merge plan.
- If the folder already has a different remote, do not replace it. Show the
  sanitized host/repository identity and ask the user to select the intended
  target.

### 4. Push or pull an already connected diary

- For pull, require a clean working tree and use fast-forward-only semantics.
  Confirm the exact remote pull unless the user explicitly requested it.
  If local edits exist or the branches diverge, stop and preserve both sides.
- For push, stage and commit only validated ShioDiary date-folder changes.
  Show an aggregate preflight before the first remote write unless the user
  explicitly requested that exact push.
- Use the normal Git Credential Manager browser sign-in. If authentication
  fails, report the category and let the user sign in through its UI; do not
  inspect or reveal secrets.
- If Git or authentication tools are missing, do not install them. Give the
  official setup route or browser instructions and ask whether the user wants
  installation help.

## Verify and report

Before claiming success, verify:

- The Git root is exactly the selected diary folder.
- origin has exactly one HTTPS GitHub fetch URL and one matching push URL,
  with no embedded credentials.
- The selected repository's visibility matches the preflight.
- The intended branch tracks the matching remote branch and has no unresolved
  ahead/behind state.
- The working tree is clean, or report the aggregate remaining change count.

Report the folder, sanitized repository identity and visibility, operation,
aggregate file count, and result. Never include diary text, dates, attachment
names, raw Git output, or credentials. State clearly if either side needs
manual conflict review.

Official GitHub CLI reference for creating a repository:
[gh repo create](https://cli.github.com/manual/gh_repo_create).
