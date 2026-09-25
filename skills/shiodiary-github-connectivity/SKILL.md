---
name: shiodiary-github-connectivity
description: >
  Help users connect or synchronize a ShioDiary diary folder with a GitHub
  repository, including Git detection, authentication diagnosis, manual setup,
  and safe push/pull guidance. Use whenever someone asks to connect ShioDiary to
  GitHub, back up or restore ShioDiary diaries through GitHub, or troubleshoot a
  ShioDiary Git sync. Do not assume Git or GitHub CLI is installed; never expose
  diary contents or credentials, and never overwrite divergent data.
compatibility: Git for Windows and GitHub CLI are optional. Browser-based GitHub setup is supported.
version: 0.1.0
---

# ShioDiary GitHub Connectivity

Use this Skill to help a person connect the diary data folder selected in
ShioDiary to a GitHub repository. The diary repository is separate from the
ShioDiary source-code and distribution repositories.

## Safety rules

- Work only with the exact diary folder and remote repository the user selected.
  Do not infer a path from the current working directory or choose a similarly
  named repository.
- Treat diary text, attachment names, dates, and metadata as private. Do not open
  diary files, print their contents, or send them to web search or other services
  unless the user specifically asks for that content to be examined.
- Before a sync, summarize the destination account/repository, its visibility,
  and the type and approximate count of files that would move. Do not include
  diary text in the summary. Ask for confirmation before creating a repository or
  pushing when the user has not already explicitly requested that exact write.
- Recommend a private diary repository. Explain that GitHub stores ordinary
  Markdown and media as readable files; repository privacy is access control, not
  end-to-end encryption.
- Keep passwords and GitHub authentication secrets inside the operating system's
  sign-in UI and credential storage. Do not display, copy, or store secret values
  returned by Git or authentication tools. Use the normal Git Credential Manager
  / GitHub browser sign-in flow when available.
- Do not install Git, GitHub CLI, extensions, or Skills without explicit
  authorization for that installation. If a tool is missing, give the official
  download route or manual steps and wait before installing.
- Preserve local and remote history. Do not discard diary data or apply an
  automatic conflict-recovery operation.
- Do not use shell-built command strings for user paths. Pass arguments as
  separate process arguments and quote paths with the shell's native mechanism.

## Workflow

### 1. Establish the target

Ask for or confirm:

1. The exact ShioDiary data folder.
2. The GitHub account or organization and repository name.
3. Whether the repository already exists, and whether it is private.
4. Whether the user wants setup, pull, push, or diagnosis.

If the user already gave one of these facts, reuse it instead of asking again.
Do not inspect diary contents to identify the folder.

### 2. Detect available tools without exposing secrets

- Check for Git for Windows with a version-only command such as `git --version`.
- `gh --version` and `gh auth status` may be used to identify GitHub CLI and
  sign-in state. Do not request the token itself.
- If Git is absent, stop the automated route. Offer manual browser steps or ask
  whether the user wants to install Git for Windows. Do not perform the install
  without explicit authorization.
- If the app offers Git detection, treat detection as read-only. Run Git only
  after the user selects the Git sync action.

### 3. Inspect repository state minimally

When the user asks for setup or sync and authorizes local inspection:

- Confirm `git -C <diary-root> rev-parse --show-toplevel` resolves to the exact
  selected diary root. If it resolves to a parent folder, stop; do not operate on
  that repository.
- Check branch, remote host/name, and a summarized status. Sanitize remote URLs
  before displaying them. Never log raw Git arguments containing credentials.
- Do not open the diary body. A file count or status summary is enough for the
  pre-write review.
- If the folder is not already a repository, explain the proposed local `git
  init` and remote before doing it. Do not change the remote or initialize a
  repository based on a guess.

### 4. Authenticate through the user's normal sign-in

- Prefer Git Credential Manager's browser-based GitHub sign-in. `gh auth login`
  is an optional user-driven route when GitHub CLI is installed.
- Keep authentication in the system's credential UI. Do not copy tokens into
  ShioDiary files, scripts, environment variables, or chat.
- For an authentication failure, report the category and tell the user to sign
  in again through the official UI. Do not ask them to reveal the credential.
- If the user wants to do all GitHub actions manually, provide the minimal
  commands and explain what each writes. Do not run them on the user's behalf.

### 5. Pull without replacing local work

- Before pulling, summarize whether there are uncommitted local changes. If there
  are, stop and explain that they must be reviewed or committed first.
- Use fast-forward-only pull semantics (`git pull --ff-only`). If branches have
  diverged or files conflict, stop and preserve both sides for user review.
- Never switch to merge, rebase, reset, or force-push as an automatic recovery.
- After a successful pull, report only the operation result and a count of
  changed files, not diary contents.

### 6. Push only the intended diary files

- Before staging or pushing, show the target repository and a short, content-free
  summary of the files that will be sent. Exclude app settings, temporary drafts,
  credentials, exports outside the chosen diary root, and unrelated files.
- Stage only files under the confirmed diary root. Review the staged path list for
  secrets or unrelated data; if uncertain, stop and ask.
- Commit and push only when the user's request clearly authorizes both. Otherwise
  show the proposed commit summary and ask before the remote write.
- Use an ordinary push. If GitHub rejects it because the remote advanced, stop;
  fetch and compare safely, then ask the user how to resolve the divergence.
- Check for individual files near or above GitHub's 100 MiB limit before
  uploading. Do not silently introduce Git LFS.

### 7. Manual route when Git is unavailable

- Explain that ShioDiary still stores ordinary dated folders and Markdown files.
- Offer a browser-based repository setup and a user-run upload/download plan.
  Warn that uploading a folder with many attachments is more cumbersome in the
  browser and that a private repository does not encrypt its contents.
- If the user chooses an agent-assisted route, return to the safety preflight and
  request explicit confirmation for the exact repository write.
- Link only to official GitHub / Git for Windows instructions when current URLs
  are needed. Do not download installers or run them automatically.

## Completion report

Report:

- The confirmed diary folder and GitHub repository (visibility included).
- Whether Git or GitHub CLI was available, without exposing credentials.
- The local or remote operation that succeeded or failed.
- Whether any changes remain uncommitted or need conflict resolution.
- Any security limitation, including plaintext diary files in the repository.

Never report success based only on a command being started. Confirm its exit
status and resulting Git state first.
