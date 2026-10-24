# Suwayomi Workspace Guide (Claude Code)

## Structure
This workspace at `D:\Project\Suwayomi-Server` contains 3 git repositories for the custom `download-metadata-cover` fork:
1. `Suwayomi-Server` (root repo)
2. `Suwayomi-WebUI` (directory `Suwayomi-WebUI/` in root)
3. `Suwayomi-Server-docker` (directory `Suwayomi-Server-docker/` in root)

The feature branch across all 3 repositories is `feat/download-metadata-cover`.

## Submodule / Gitlink Conventions
- `Suwayomi-Server` tracks `Suwayomi-WebUI` and `Suwayomi-Server-docker` as gitlink submodules (mode 160000).
- After any rebase or commit in `Suwayomi-WebUI` or `Suwayomi-Server-docker`, update the submodule pointers in `Suwayomi-Server`:
  ```bash
  git add Suwayomi-WebUI Suwayomi-Server-docker
  git commit --amend --no-edit  # or new commit
  ```

## Critical Project Conventions
- **Constants.kt**: `buildSrc/src/main/kotlin/Constants.kt` defines `webUIRevisionTag`.
  - Always prefix with `v` instead of upstream's `r` (e.g. `val webUIRevisionTag = "v3518"`).
  - Matches the tag published by `NgoMinhToan/Suwayomi-WebUI` release workflow.
- **Docker Repo**:
  - Keep `main` in `Suwayomi-Server-docker` cleanly reset to `upstream/main`.
  - Ensure the scheduled `save_docker_pull_count.yml` workflow on the fork remains disabled to prevent weekly divergence on `main`.

## Build & CI/CD Order
Always execute in sequential order:
1. **WebUI**: Run workflow `release.yml` with `release_version` (e.g. `3518`) -> Undraft release with `gh release edit v<ver> --draft=false --repo NgoMinhToan/Suwayomi-WebUI`.
2. **Server**: Run workflow `publish.yml` with `release_version` (e.g. `v2.4.2436`) -> Undraft release with `gh release edit v<ver> --draft=false --repo NgoMinhToan/Suwayomi-Server`.
3. **Docker**: Run workflow `build_container_images.yml` (`tachidesk_release_type=preview`) on `feat/download-metadata-cover`.
   - Produces `ghcr.io/ngominhtoan/suwayomi-server:preview`.
