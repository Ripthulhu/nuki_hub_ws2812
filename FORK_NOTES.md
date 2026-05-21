# Nuki Hub WS2812 Fork Notes

This fork carries a small WS2812/NeoPixel status LED feature set for ESP32 boards with an onboard WS2812 LED.

## Branch Strategy

Keep `master` as close to `technyon/nuki_hub:master` as possible. The `ws2812-status-led` branch stores the WS2812 changes as a small patch stack in `patches/ws2812-status-led/`, plus fork-specific CI/docs. This keeps upstream sync simple and avoids rewriting large source files in the fork until the patch is intentionally applied.

Recommended remotes:

```sh
git remote add upstream https://github.com/technyon/nuki_hub.git
git remote set-url origin https://github.com/Ripthulhu/nuki_hub_ws2812.git
```

Sync from upstream:

```sh
git fetch upstream
git switch master
git merge --ff-only upstream/master
git push origin master
git switch ws2812-status-led
git rebase master
git push --force-with-lease origin ws2812-status-led
```

If upstream changes the GPIO, web config, or Nuki wrapper files touched by this branch, resolve those conflicts first and then rebuild `esp32-s3-oct`.

Apply the patch stack locally from a clean upstream checkout:

```sh
git apply patches/ws2812-status-led/*.patch
```

## Build Target

The board used for this fork is:

```sh
make updater_esp32-s3-oct
make esp32-s3-oct
```

The OTA firmware artifact is generated at:

```text
release/esp32s3oct/nuki_hub_esp32s3oct.bin
```

Generated build output, local dependency folders, and Codex working directories should stay uncommitted.

## GitHub Actions

This fork adds a focused workflow for the ESP32-S3 octal PSRAM target. It runs on pushes to the WS2812 branch and on pull requests targeting `master`.

The workflow applies `patches/ws2812-status-led/*.patch`, builds the updater and firmware, checks that the OTA image fits the app slot, and uploads the `release/esp32s3oct` folder as an artifact.

GitHub only exposes the manual `workflow_dispatch` button for workflows present on the repository default branch. If you want a manual "Run workflow" button, copy or merge `.github/workflows/ws2812-s3-oct.yml` onto `master`; the patch stack can still stay on `ws2812-status-led`.
