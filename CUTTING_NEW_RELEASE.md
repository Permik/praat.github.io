# Cutting a new release

The **Cut release** workflow builds and packages the GitHub-hosted editions,
then creates one GitHub release containing the resulting archives. It is
intentionally a manual workflow because a release requires version changes and
platform testing.

## Before starting

1. Update the version and release date in `main/main_Praat.h`:
   `PRAAT_VERSION_STR`, `PRAAT_VERSION_NUM`, `PRAAT_YEAR`, `PRAAT_MONTH`, and
   `PRAAT_DAY`. Use the same version in the release tag, without a leading `v`.
2. Commit and merge those changes into `master`.
3. Run the existing **Build and Test** workflow on the commit to be released.
4. Verify the GUI and audio behavior of the candidate binaries on each
   platform that you can test before publishing.

## GitHub-hosted runners

The workflow uses GitHub-hosted runners for every edition that GitHub supports.
Linux s390x, ChromeOS, and Raspberry Pi ARMv7 are not available as
GitHub-hosted runner platforms and are therefore not built by this workflow.

| Editions | Runner |
| --- | --- |
| macOS universal | `macos-14` |
| Windows x64-v3, x64-v1 | `windows-2022` |
| Windows ARM64 | `windows-11-arm` |
| Windows i686 | `windows-2022` |
| Linux x64-v3 and barren | `ubuntu-24.04` |
| Linux ARM64 and barren | `ubuntu-24.04-arm` |

The workflow installs the required MSYS2 toolchain on each Windows runner.
For macOS, it downloads the `praat.xcodeproj` archive from the latest upstream
`praat/praat.github.io` release before building.

## Run the workflow

1. Open **Actions → Cut release**.
2. Select **Run workflow**.
3. Enter the version, for example `7.0.04`.
4. Leave `source_ref` as `master`, unless the release is being cut from a
   specific commit or release branch.
5. Keep **draft** enabled for the first run. Enable **prerelease** only for a
   prerelease version.
6. Start the workflow and wait for every supported platform job to finish successfully.
   The release job is blocked if even one edition fails.
7. Review the generated notes and all 9 assets in the draft release. Rename or
   replace an asset only if the corresponding build output was independently
   verified.
8. Publish the draft release.

The workflow creates the tag `v<version>` and uses GitHub’s generated release
notes. It refuses to overwrite an existing tag or release. If a build fails,
fix the runner or source problem and run the workflow again with a new version
or after removing the failed draft release and tag.

## Assets produced

The release contains the current Windows, macOS, and Linux archives, including
GUI and barren Linux variants. The filenames follow the conventions documented
in `README.md`; the workflow checks that exactly 9 assets were produced before
creating the release.
