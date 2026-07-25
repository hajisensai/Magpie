# Hibiki fork of Magpie

This repository is a fork of [Blinue/Magpie](https://github.com/Blinue/Magpie), licensed under
**GPL-3.0**. It exists so that [Hibiki](https://github.com/hajisensai/hibiki) can ship a
self-built Magpie for in-app window upscaling (galgame scenario), and so that we can patch
Magpie's source when integration requires it.

本仓库是 [Blinue/Magpie](https://github.com/Blinue/Magpie) 的 fork，遵循 **GPL-3.0**。
Hibiki 需要内置窗口超分并按需分发自编产物，且后续可能要改 Magpie 源码，故 fork 自持。

## Upstream baseline / 上游基线

- Forked from `Blinue/Magpie` branch `dev`, at upstream commit **`e6167ef`**
  (upstream release baseline `v0.12.1`, 2025-08-27).
- The upstream `LICENSE` and all copyright notices are kept **unmodified**.
- Corresponding source for every binary we distribute is this repository at the same commit
  recorded in the release body and in `hibiki-magpie.json` inside each zip.

## What we changed / 我们改了什么

As of the initial fork:

| Change | File | Why |
|---|---|---|
| Added a Hibiki-specific release workflow | `.github/workflows/hibiki-release.yml` | Publishes `Magpie-hibiki-<platform>.zip` + `.sha256` sidecar to the fixed tag `magpie-hibiki`, so the Hibiki client has a stable direct link. Upstream's `release.yml` is untouched. |
| Added this document | `HIBIKI-FORK.md` | GPL-3.0 attribution + change log |
| Pointer in README | `README.md` | Make the fork status discoverable |

**No source code under `src/` has been modified yet.** Any future source change will be listed
here with its rationale.

## How our release differs from upstream's

- Upstream ships `Magpie-<tag>-<platform>.zip` with an **MD5** recorded in `version.json`
  (consumed by Magpie's own updater). We additionally publish a **SHA-256 sidecar**, which is
  what the Hibiki client verifies against and uses as its "is there a newer build" predicate.
- Our asset filenames deliberately carry **no version number**, because the Hibiki client
  downloads from a fixed tag (`magpie-hibiki`) and must have a URL that never changes.
  Version information lives in the release body and in `hibiki-magpie.json` at the zip root.
- Our builds are **unsigned** (a fork has no access to the upstream signing certificate).
  Consequently Magpie's `TouchHelper` UIAccess registration will not work; Hibiki does not use
  touch support.
- We build with `--compiler=ClangCL` (same as upstream releases) but pass no `--version-*`
  flags, so the binary reports version `0.0.0` — Magpie's own marker for a development build.
  This is intentional: a self-built binary must not impersonate an official version number.

## Known integration caveats

- `src/Magpie/UpdateService.cpp` hardcodes update-check URLs pointing at
  `raw.githubusercontent.com/Blinue/Magpie/{dev,main}/version.json`. A user who enables
  Magpie's own auto-update inside our build would be pulled back to upstream official
  packages. Hibiki manages Magpie's version itself; disabling or re-pointing this is a
  candidate for a future source patch here.
