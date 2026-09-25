# ShioDiary

ShioDiary is a local-first Windows diary app. Its complete application source is maintained privately; this repository contains public Windows releases and the companion GitHub connectivity Skill.

## Download

Download the Windows x64 installer from [Releases](https://github.com/TukinokiShio/ShioDiary-Distribution/releases/latest):

`ShioDiary-Setup-0.1.0-x64.exe`

SHA-256: `18DBDF13F1918572C02C2E0DFE3030B64CC79C3B5E5D3B4DB16B5259FFAF80A9`

The installer is currently unsigned. Windows may show an unknown publisher or SmartScreen warning. Verify the SHA-256 before installing.

## Features

- Diary entries stored locally by year, month, and day, with images and audio in each day's folder.
- Markdown editing and preview; import/export Markdown, TXT, and DOCX. DOCX conversion preserves semantic text and common structure, not complex page layout.
- Paste images, attach or record audio, and play audio in an entry.
- Automatic saving, recovery drafts, writing-time tracking, search, tags, and favorites.
- Three themes, plus a local background image and simple appearance controls.
- Optional, user-triggered GitHub Push/Pull. Configure Git and credentials yourself or follow the [`shiodiary-github-connectivity` Skill](skills/shiodiary-github-connectivity/SKILL.md).

The Windows account prompt verifies the current account before opening the app. It does not encrypt diary files on disk. Diary content remains local unless you configure synchronization.

## GitHub connection Skill

Skill version 0.2.0 focuses on the shortest safe setup: select a diary folder
and either create a private repository or provide an existing repository URL.
It checks local and remote state before asking for one approval covering the
planned writes, then verifies the connection. The Skill never displays diary
contents or credentials and stops when repository histories conflict.

## Licenses

- The ShioDiary application and its original documentation are provided for free personal, non-commercial use only. Modification, commercial use, and redistribution are not permitted. See [LICENSE](LICENSE).
- The companion Skill is separately licensed under MIT. See [its license](skills/shiodiary-github-connectivity/LICENSE).
- Bundled third-party components remain subject to their own licenses and notices.

The complete application source is not included in this public repository.
