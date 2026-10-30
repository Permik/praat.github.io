# Cutting a new release

The **Cut release** workflow builds and packages all 14 currently supported
Praat editions, then creates one GitHub release containing the resulting
archives. It is intentionally a manual workflow because a release requires
version changes, platform testing, and access to the project’s special-purpose
build machines.

## Before starting

1. Update the version and release date in `main/main_Praat.h`:
   `PRAAT_VERSION_STR`, `PRAAT_VERSION_NUM`, `PRAAT_YEAR`, `PRAAT_MONTH`, and
   `PRAAT_DAY`. Use the same version in the release tag, without a leading `v`.
2. Commit and merge those changes into `master`.
3. Run the existing **Build and Test** workflow on the commit to be released.
4. Verify the GUI and audio behavior of the candidate binaries on each
   platform that you can test before publishing.

## Required runners

The workflow uses GitHub-hosted Ubuntu runners for Linux x64-v3 and Linux
ARM64. The remaining targets require self-hosted runners with the labels below.
Each runner must have the compiler, SDK, and system libraries needed by
`HOW_TO_BUILD_ONE.md` already installed, and must provide a Bash shell.

| Editions | Required labels |
| --- | --- |
| macOS universal | `self-hosted`, `macOS`, `xcode` |
| Windows x64-v3, x64-v1 | `self-hosted`, `Windows`, `msys2`, `clang64` |
| Windows ARM64 | `self-hosted`, `Windows`, `msys2`, `clangarm64` |
| Windows i686 | `self-hosted`, `Windows`, `msys2`, `mingw32` |
| Linux s390x | `self-hosted`, `Linux`, `s390x` |
| ChromeOS x64-v1 | `self-hosted`, `Linux`, `ChromeOS`, `x64` |
| ChromeOS ARM64 | `self-hosted`, `Linux`, `ChromeOS`, `arm64` |
| Raspberry Pi ARMv7 | `self-hosted`, `Linux`, `RaspberryPi`, `armv7` |

The macOS runner must keep the Xcode project at
`/opt/praat/praat.xcodeproj`, as described in `HOW_TO_BUILD_ONE.md`; this
project is not part of the ordinary source checkout. The workflow copies it
into the checkout before building. The Windows runners must have `make`, `7z`,
and the matching MSYS2 toolchain available on `PATH`. Do not register a runner
with more than one platform label unless it is genuinely able to build both
targets.

## Run the workflow

1. Open **Actions → Cut release**.
2. Select **Run workflow**.
3. Enter the version, for example `7.0.04`.
4. Leave `source_ref` as `master`, unless the release is being cut from a
   specific commit or release branch.
5. Keep **draft** enabled for the first run. Enable **prerelease** only for a
   prerelease version.
6. Start the workflow and wait for every platform job to finish successfully.
   The release job is blocked if even one edition fails.
7. Review the generated notes and all 14 assets in the draft release. Rename or
   replace an asset only if the corresponding build output was independently
   verified.
8. Publish the draft release.

The workflow creates the tag `v<version>` and uses GitHub’s generated release
notes. It refuses to overwrite an existing tag or release. If a build fails,
fix the runner or source problem and run the workflow again with a new version
or after removing the failed draft release and tag.

## Assets produced

The release contains the current Windows, macOS, Linux, ChromeOS, and
Raspberry Pi archives, including GUI and barren Linux variants. The filenames
follow the conventions documented in `README.md`; the workflow checks that
exactly 14 assets were produced before creating the release.
