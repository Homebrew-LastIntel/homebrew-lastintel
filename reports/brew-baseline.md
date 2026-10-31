# Homebrew brew baseline: Intel behavior at 518895ab vs origin/main

Read-only. Clone kept at `/tmp/lastintel-brew` (about 168 MiB). No push, fork, PR, install script, or security-disabling patch. Disk: `/System/Volumes/Data` had 217 GiB available before and after the clone (threshold 50 GiB).

- Baseline: `518895ab7be7407523990f86eb6a1a53eb35a72f` (2026-08-31 19:37:39 +0000), `Merge pull request #23717 from Homebrew/issue_23714_brew`.
- Today's `origin/main`: `8a17c2078a1a2f91248915546216783978399e0c` (2026-10-31 18:44:09 +0000), `Merge pull request #24411 from Homebrew/receipt-path-names`.
- Commits in `518895ab..origin/main`: 1247.
- Newest tag reachable from `origin/main`: `7.0.11` (`c6069d2a943b71b0a90d91fc1ac93cb9c1634dcc`, 2026-10-26). `main` has commits after that tag.
- Homebrew 7.0.0 is `d79ef822ab8136e393ed5f86e2b56afc68d04874` (2026-09-13 08:06:15 +0000), after the Tier 3 merge. Do not rewind to 7.0.0.

## 1. Parent of the Tier 3 merge

Confirmed. `d7daec068877135f991a864cff094e7d25bca5b3` is a merge commit.

- Committer date: 2026-09-01 07:25:47 +0000.
- Subject: `Merge pull request #23679 from Homebrew/intel-tier-3`.
- Body: `Reduce Intel macOS to Tier 3`.
- First parent (`^1`): `518895ab7be7407523990f86eb6a1a53eb35a72f`.
- Second parent (`^2`): `c0c92cc7b791bd4e429a71a56d5f440e52393314` (2026-08-31 20:54:56 +0100), subject `Reduce Intel macOS to Tier 3`. That commit is the behavior change. The merge only joins it to `main`.
- Both `518895ab` and `d7daec06` are ancestors of `origin/main`.

Files the Tier 3 commit itself touched (9):

- `Library/Homebrew/extend/os/mac/diagnostic.rb`
- `Library/Homebrew/extend/os/mac/formula_installer.rb`
- `Library/Homebrew/os.rb`
- `Library/Homebrew/plans/relocatable-bottles.md`
- `Library/Homebrew/test/os/mac/diagnostic_spec.rb`
- `Library/Homebrew/test/os/mac/formula_installer_spec.rb`
- `docs/Installation.md`
- `docs/Support-Tiers.md`
- `package/Distribution.xml` (added an Intel Tier 3 *warning*; still `hostArchitectures="x86_64,arm64"`)

## 2. Tag 6.0.20

Confirmed. `refs/tags/6.0.20` exists. It is a lightweight tag (object type `commit`, no tagger date).

- Tagged commit: `3e3da86e4eaccd23f4ce43df9b0a31b16c707347`.
- Commit date: 2026-08-27 02:05:49 +0000.
- Subject: `Merge pull request #23669 from Homebrew/sorbet-files-update`.
- `6.0.20` is an ancestor of `518895ab` (73 commits earlier). It is before the Intel Tier 3 merge, so it still has the pre-Tier-3 Intel behavior.

## 3. What still runs on Intel, unchanged

`brew` on current `main` does not refuse to start on Intel. These paths are the same idea as at `518895ab` and must not be "fixed" by deleting them:

- `Library/Homebrew/utils/os.sh`: `x86_64-*` sets `HOMEBREW_PROCESSOR=x86_64`. On macOS, arm64 uses `/opt/homebrew`; every other macOS CPU, including Intel, uses `HOMEBREW_GENERIC_DEFAULT_PREFIX` (`/usr/local`). `setup-os-details` still renames the macOS user-agent processor to `Intel` when `HOMEBREW_PROCESSOR=x86_64`, and still detects Rosetta (`hw.optional.arm64=1`) into `HOMEBREW_PHYSICAL_PROCESSOR=arm64`.
- `Library/Homebrew/extend/os/mac/install.rb` is new, but it is a move of the prefix guard that already lived in `Library/Homebrew/install.rb` at `518895ab`: Intel or Rosetta in `/opt/homebrew` dies; Apple Silicon in `/usr/local` dies. Same behavior. Do not edit it to "restore" Intel.
- `Library/Homebrew/cmd/vendor-install.sh` `set_ruby_variables` still forces `ruby_PROCESSOR=x86_64` and `ruby_OS=darwin` when an arm64 process is running a `/usr/local` prefix, and otherwise uses the physical processor. It still sources `Library/Homebrew/vendor/portable-ruby-${ruby_PROCESSOR}-${ruby_OS}`.
- `Library/Homebrew/github_runner_matrix.rb` already limited the macOS `x86_64` runner to formulae whose names start with `portable-` at `518895ab`. The general macOS bottle matrix was already arm64-only. That file is not the Tier 3 switch.
- `Library/Homebrew/macos_version.rb` still contains `# odisabled: remove support for Big Sur and macOS x86_64 September (or later) 2027`. Removal of the ability to *run* on Intel is not implemented. `docs/Support-Tiers.md` says that removal is planned in or after September 2027.
- Linux x86_64 stays Tier 1 (SSSE3 required). Do not treat Linux `x86_64` as the macOS Intel demotion.

Version floors moved for every Mac, not only Intel. Leave them:

- `Library/Homebrew/brew.sh`: `HOMEBREW_MACOS_OLDEST_SUPPORTED` 14 → 15, `HOMEBREW_MACOS_OLDEST_ALLOWED` 10.15 → 11, `HOMEBREW_MACOS_NEWEST_SUPPORTED` 26 → 27, `HOMEBREW_MACOS_NEWEST_UNSUPPORTED` 27 → 28.
- `Library/Homebrew/utils/os.sh` now refuses to run before Big Sur (`HOMEBREW_MACOS_OLDEST_ALLOWED`) instead of before Catalina, and dropped the Catalina libressl/git special cases.
- `Library/Homebrew/macos_version.rb`: `SYMBOLS` no longer includes `:catalina` (and older names are display-only via `RELEASES`). `outdated_release?` is still `self < HOMEBREW_MACOS_OLDEST_SUPPORTED`, so Sonoma is Tier 3 on Apple Silicon too.
- `package/Distribution.xml` `os-version min` 14.0.0 → 15.0.0 matches that floor. Putting Intel back does not mean lowering it.

Post-baseline Intel bugfixes to keep:

- `10116fe6e6c4b8a08ad3c57c9a7a761b89c062be` (2026-09-01) `Library/Homebrew/shims/super/cc`: under Rosetta, Apple clang gets `-arch x86_64` so it does not combine an arm64 target with `-march=westmere`. Reverting this breaks Intel Homebrew under Rosetta on macOS 26.
- `883aaedc9cfc0249298f410c4744d94530ae294b` (2026-09-20) `Library/Homebrew/os/mac/xcode.rb`: `latest_version` for macOS 26 is `26.6` on Intel and `27.0` on arm64 (`arm64:` argument). This is the Intel Tahoe CLT check fix.

## 4. Intel behavior diff, 518895ab..origin/main

### `Library/Homebrew/os.rb`

Tier 1 issue URL. At `518895ab`, `ISSUES_URL` was set (so `OS.not_tier_one_configuration?` was false) when macOS was not prerelease, not outdated, `--cc=` was absent, and the prefix was `HOMEBREW_DEFAULT_PREFIX` or (ARM prefix and ARM CPU). On Intel that prefix is `/usr/local`.

On `origin/main` the condition is `Hardware::CPU.arm?` and `HOMEBREW_PREFIX == HOMEBREW_MACOS_ARM_DEFAULT_PREFIX` only. Every Intel Mac is not Tier 1 for issue reporting, including `/usr/local` on a current macOS. This is the code definition of the demotion. Introduced entirely by `c0c92cc7`.

### `Library/Homebrew/extend/os/mac/diagnostic.rb`

`check_for_unsupported_macos` at `518895ab`: prerelease → tier 2; `outdated_release?` → tier 3 plus a MacPorts sentence; a current supported Intel Mac produced no finding.

On `origin/main`, any `Hardware::CPU.intel?` that is not an outdated release is tier 3. The message says "You are using macOS on Intel x86_64" and that Homebrew will not support that platform (September 2026 / announced August 2025). Remediation is `macos_bottle_remediation(..., intel: true)`.

`check_for_opencore` was deleted in `c0c92cc7`. At baseline it ran only when not physical arm64, read OpenCore/OCLP nvram, and reported tier 2 if the CPU had `:pclmulqdq` and the OS was not outdated, otherwise tier 3. That was Intel-only.

Other edits in this file are not the Intel demotion and must stay: `check_for_emudhra_certificates` (compromised-CA doctor check), `check_broken_sdks` removal (Catalina CLT SDK bug), remediation formatting via `append_indented_list`, and `supported_configuration_checks` calling `super` (prefix and Nix checks). Do not revert those while editing this file.

### `Library/Homebrew/diagnostic.rb`

New `macos_bottle_remediation(version, intel:)` (`686e8a54d5`, 2026-09-05, "Base macOS migration advice on binary packages"):

- Returns nil unless Intel or an outdated release, and returns nil above Tahoe.
- Intel and `version >= :tahoe`: "Homebrew no longer builds bottles… Existing bottles may still work, but updated formulae may build from source."
- Otherwise: suggest MacPorts.

`check_for_unsupported_macos` always passes `intel: Hardware::CPU.intel?`, so every Intel Mac, including Tahoe, gets that text. The same file also grew tap-origin checks (`desired_git_remote?`, `examine_git_origin`) used for tap trust. Touch only `macos_bottle_remediation` (or stop the Intel caller from using it). Do not revert the tap-remote logic.

`check_homebrew_prefix` still exists. It now stays quiet when `BottleSpecification.compatible_locations?` (custom prefixes short enough for relocation). Intel `/usr/local` is still the default prefix, so this check does not fire there. The Support Tiers doc says a short custom prefix can be Tier 1 on Apple Silicon, and "Intel macOS remains Tier 3."

### `Library/Homebrew/extend/os/mac/formula_installer.rb`

`fresh_install?` controls the hard error in `FormulaInstaller#check_install_sanity` ("no bottle available!") for a tier-1-style fresh install.

At `518895ab`:

```ruby
!developer? && !OS::Mac.version.outdated_release? &&
  (installed_on_request? || !formula.any_version_installed?)
```

`c0c92cc7` added `Hardware::CPU.arm? &&` and left the rest. On Intel, a missing bottle is no longer that error, which matches Tier 3 source-build behavior.

Later, `aa796b8e60` (2026-09-11, "Report tiers and refresh stale bottle metadata") dropped `installed_on_request?` for every Mac and kept `!formula.any_version_installed?`. That later change is not Intel-only. Restoring Intel means removing the `arm?` guard. Do not also force the August predicate back onto Apple Silicon.

`Library/Homebrew/formula_installer.rb` `verify_bottle_attestation?` changed in the same range, from core-tap-only to any tap that is present (`95bfab038b`, 2026-10-17, "Tighten bottle attestation verification"). That is attestation. Leave it.

### `Library/Homebrew/macos_version.rb`

Not the Tier 3 switch. Diff is the release-symbol split (`RELEASES` vs active `SYMBOLS`), Big Sur `10.16` display alias, Catalina removed from active symbols, kernel-major math simplified now that pre-Big Sur is refused, and `NULL` constructed from `"11"` instead of `"10.0"`. `requires_nehalem_cpu?` still works for Intel. No file change here is required to treat current Intel macOS as Tier 1 again.

### `docs/Support-Tiers.md`

`last_review_date` 2026-04-03 → 2026-09-21. Behavioral policy on `origin/main`:

- Tier 1 macOS is official Apple Silicon hardware only, default prefix `/opt/homebrew` (or a shorter custom prefix). `/usr/local` on Intel is no longer a Tier 1 prefix.
- Tier 3 list adds "Intel x86_64 systems running macOS".
- As of September 2026, Intel is Tier 3 for Big Sur 11 through Tahoe 26, and Catalina and earlier are unsupported. Apple Silicon Tier 1 is Golden Gate 27, Tahoe 26, Sequoia 15.
- In or after September 2027, Intel is "Unsupported: all macOS versions."
- Text says Homebrew has stopped building new bottles for Intel and will remove the ability to run on Intel in or after September 2027.
- Custom-prefix paragraph: "Intel macOS remains Tier 3" even when the prefix is short enough.
- OpenCore Legacy Patcher lines were removed from Tier 2 and Tier 3 (Westmere split). Installation.md now says every Intel Mac that can run Homebrew, including OCLP, is Tier 3.

At `518895ab` the same doc still listed Intel Tahoe 26, Sequoia 15, and Sonoma 14 as Tier 1, with `/usr/local` as the Intel default prefix.

### `package/Distribution.xml`

Two steps:

1. `c0c92cc7`: kept `hostArchitectures="x86_64,arm64"` and min OS 14, and added `installation_check` that warns (does not fatal) when `hw.machine == x86_64`, title "Intel Macs are a Tier 3 configuration".
2. `c5715c40661dc8ff15175050bdcccbe845e818d3` (2026-09-04, "Make macOS installer package Apple Silicon only"): `hostArchitectures="arm64"` only, so Installer refuses the pkg on Intel instead of warning. CLT script removed. Commit message: Intel is Tier 3 without new bottles, so stop shipping and testing the `.pkg` there; the `/usr/local` relocation in `postinstall` became dead code.

Current file also has `os-version min="15.0.0"` (the global floor, not an extra Intel-only gate).

### Install path and portable Ruby

`package/scripts/postinstall` at `518895ab` (around lines 55–80) moved the staged tree to `/usr/local/Homebrew` when `uname -m` was `x86_64`, and symlinked `/usr/local/bin/brew`. On `origin/main` there is no `x86_64` and no `/usr/local` in that script. The script now refuses symlinked prefix directories, refuses root as the install user, and runs git through `git_as_user`. Those checks are later installer hardening. Reintroducing an Intel destination must keep them. Do not restore the old `git_no_hooks` root checkout.

`package/resources/WELCOME.rtf` and `package/resources/CONCLUSION.rtf` lost their Intel text in `c5715c4066`.

`.github/workflows/release.yml`: `HOMEBREW_MACOS_OLDEST_SUPPORTED` 14.0 → 15.0; the test matrix dropped `macos-15-intel` / `macos-15-x86_64` and only runs `macos-15` and `macos-26`; cleanup no longer deletes `/usr/local`. `4e167d4e500c9d935ac7d74491e93374a58502ce` (2026-09-01, "Skip doctor on Tier 3 Intel macOS") had skipped `brew doctor` on that Intel pkg test before the runner was removed.

`Library/Homebrew/brew.sh` arch/prefix logic did not change aside from the four `HOMEBREW_MACOS_*` version constants above. `bin/brew` dropped `HOMEBREW_FORCE_BREW_WRAPPER` and refuses `EUID != UID`. That is wrapper/privilege policy (now documented as Tier 3 for wrappers), not the Intel CPU demotion. Leave it.

### Docs and CI that record the same decision

- `docs/Installation.md`: requires an Apple Silicon CPU; a 64-bit Intel CPU is Tier 3. `46f8aa4d85` removed the install-script support line for Intel. Footnote still says Intel bottle installs need developer tools, unlike Apple Silicon.
- `docs/FAQ.md`: `/usr/local` for Intel is "legacy, Tier 3". `f43f509752`.
- `Library/Homebrew/plans/relocatable-bottles.md`: Intel macOS is out of scope; "Homebrew/core no longer builds Intel bottles outside the portable Ruby toolchain." At baseline, Intel kept legacy `/usr/local` bottling until EOL.
- `Library/Homebrew/dev-cmd/generate-cask-ci-matrix.rb`: `07bad4a830` deleted `X86_MACOS_RUNNERS` (`macos-15-intel`). macOS cask CI is arm64 only (`macos-15`, `macos-26`, `xcode-27`). Linux runners still include Intel.
- `Library/Homebrew/dev-cmd/generate-bottle-ci-matrix.rb` is new (`52a621c710`). It still parses an optional `x86_64` runner suffix and defaults a bare macOS version to `x86_64`. It does not itself delete Intel runners. Not a demotion.
- `.github/workflows/tests.yml`: `96da617781` (2026-09-01, "Speed up test-bot smoke jobs", body "Drop duplicate Tier 3 Intel macOS coverage") removed the `test-bot (macOS x86_64)` job on `macos-15-intel`. Subject does not match the list in section 5. Linux x86_64 test-bot jobs remain.
- `Library/Homebrew/extend/os/mac/test_bot.rb`: deleted `skip_recursive_dependents?` that returned true on `Hardware::CPU.intel?`. Baseline skipped recursive dependents on Intel. Current code does not. This only matters if an Intel test-bot runner comes back.
- `Library/Homebrew/cask/audit.rb` `audit_rosetta` (`ca73422eb3`, subject does not match section 5): new casks must not require Rosetta 2. That is Apple Silicon cask policy, not the Intel Mac prefix/tier switch.

### Specs that assert the new Intel tier

Update these if the runtime files above change. They are not the behavior source:

- `Library/Homebrew/test/os/mac/diagnostic_spec.rb`
- `Library/Homebrew/test/os/mac/formula_installer_spec.rb`
- `Library/Homebrew/test/diagnostic/macos_bottle_remediation_spec.rb`
- `Library/Homebrew/test/dev-cmd/generate-cask-ci-matrix_spec.rb` if cask CI runners are restored

## 5. Commits since 518895ab whose subject mentions Intel, x86_64, Tier 3, or bottle CI

Case-insensitive subject match. Committer dates. Newest first is the reverse of this list. No subject contained the exact token `x86_64` (`0b949e8cc94e` says `x86-64`, so it is not listed). Merge subjects match because the branch name contains `intel`.

- `c0c92cc7b791bd4e429a71a56d5f440e52393314` 2026-08-31 Reduce Intel macOS to Tier 3
- `d7daec068877135f991a864cff094e7d25bca5b3` 2026-09-01 Merge pull request #23679 from Homebrew/intel-tier-3
- `10116fe6e6c4b8a08ad3c57c9a7a761b89c062be` 2026-09-01 Target Intel Clang under Rosetta
- `4e167d4e500c9d935ac7d74491e93374a58502ce` 2026-09-01 Skip doctor on Tier 3 Intel macOS
- `07bad4a8307653bd4264cbb58c919b863480a02b` 2026-09-03 generate-cask-ci-matrix: drop macOS Intel CI
- `6d41f0f13988ae1e6672947bc4e8cbce745f08b5` 2026-09-03 Merge pull request #23757 from Homebrew/cask-ci-matrix-drop-intel
- `52a621c7108ae10ba1a2e3e8d123d41a4ea3547d` 2026-09-06 Move bottle CI workflow helpers into brew
- `883aaedc9cfc0249298f410c4744d94530ae294b` 2026-09-20 Fix CLT version check on Intel Tahoe
- `cafd5c74e867cc5c6a491c3b375b68c7ccbbb5e8` 2026-09-20 Merge pull request #24043 from thiagogenez/clt-tahoe-intel-version
- `46f8aa4d855f6698f8134b5da49d7d750e416052` 2026-10-01 Update Installation.md - Remove reference to install script support for macOS Intel
- `f43f5097527682fd1be6831e1ccec7b54597021d` 2026-10-01 Update FAQ.md -- add support caveat to macOS Intel path note

`10116fe6e6` and `883aaedc9c` are fixes to keep. `52a621c710` adds a helper that still accepts `x86_64` runner names. The demotion commits are `c0c92cc7` / `d7daec06`, then `4e167d4e`, `07bad4a830` / `6d41f0f139`, `46f8aa4d`, and `f43f509752`. Related but not in this subject list: `c5715c4066` (pkg Apple Silicon only) and `96da617781` (drop `macos-15-intel` from `tests.yml`).

## 6. Files that must change to put 518895ab Intel behavior on today's main

"Today's main" means current version policy: oldest allowed Big Sur, oldest supported Sequoia, newest supported Golden Gate. It does not mean rolling Sonoma back to Tier 1 or turning Catalina back on. It means a non-prerelease, non-outdated Intel Mac in `/usr/local` is Tier 1 again, the `.pkg` can target `x86_64`, and `brew doctor` does not call every Intel Mac unsupported.

Must change (runtime and the installer that encodes it):

1. `Library/Homebrew/os.rb` — set `ISSUES_URL` again for Intel when the prefix is `HOMEBREW_DEFAULT_PREFIX` (`/usr/local`), macOS is not prerelease and not outdated, and `--cc=` is absent. Keep the ARM `/opt/homebrew` clause. Keep the Nix branch.
2. `Library/Homebrew/extend/os/mac/diagnostic.rb` — `check_for_unsupported_macos` must not take the tier-3 branch only because `Hardware::CPU.intel?`. Restore the baseline split (prerelease tier 2, outdated tier 3, current supported Mac silent), including current Intel. Restore `check_for_opencore` if OCLP should again be tier 2/3 instead of "all Intel is tier 3". Do not remove `check_for_emudhra_certificates`.
3. `Library/Homebrew/diagnostic.rb` — only `macos_bottle_remediation`. It must not tell a supported Intel Mac that bottles are gone and to use MacPorts. Outdated-macOS MacPorts text can stay for old Apple Silicon. Do not edit `desired_git_remote?`, `examine_git_origin`, or `no_require_tap_trust?` in this file.
4. `Library/Homebrew/extend/os/mac/formula_installer.rb` — remove `Hardware::CPU.arm? &&` from `fresh_install?` so Intel on a supported macOS gets the same missing-bottle error as Apple Silicon. Do not revert `verify_bottle_attestation?` in `Library/Homebrew/formula_installer.rb`. Do not put `installed_on_request?` back for ARM; that was `aa796b8e60`, not the Tier 3 commit.
5. `package/Distribution.xml` — `hostArchitectures` must include `x86_64` again (`x86_64,arm64`). Leave `os-version min="15.0.0"`. Do not bring back a check that skips bottle attestation or tap trust.
6. `package/scripts/postinstall` — add back an `x86_64` install into `/usr/local` (baseline did this). Keep symlink refusal, non-root install user, and `git_as_user`. Do not restore the pre-hardening script.
7. `docs/Support-Tiers.md` — Tier 1 macOS for Intel x86_64 at `/usr/local` covers the releases that actually run on Intel (Sequoia 15 and Tahoe 26). Golden Gate 27 does not boot on Intel, so it is not an Intel Tier 1 target. Remove "Intel x86_64 systems running macOS" from the Tier 3 list for those Intel-capable releases. The September 2027 removal sentence is future policy; the baseline had not done that demotion yet.
8. `docs/Installation.md` and `docs/FAQ.md` — they currently tell users Intel is Tier 3 and that the install script does not support it. They have to match the code or the tier change is docs-only.

Needed only if CI and the pkg test should match baseline again (they do not change `brew` on a machine that already has a checkout):

- `.github/workflows/release.yml` — test the pkg on `macos-15-intel` again, and clean `/usr/local` in that job. Do not skip `brew doctor` the way `4e167d4e` did once Intel is Tier 1.
- `.github/workflows/tests.yml` — `test-bot (macOS x86_64)` on `macos-15-intel`, removed by `96da617781`.
- `Library/Homebrew/dev-cmd/generate-cask-ci-matrix.rb` — put `macos-15-intel` back if cask CI should build Intel again.
- `package/resources/WELCOME.rtf`, `package/resources/CONCLUSION.rtf`.
- `Library/Homebrew/plans/relocatable-bottles.md` — one sentence; not executed.
- `Library/Homebrew/extend/os/mac/test_bot.rb` — baseline skipped recursive dependents on Intel. Restore only if that CI policy is wanted. It is not required for Tier 1 doctor/issue behavior.
- The specs named in section 4.

Do not change for this restoration:

- `Library/Homebrew/macos_version.rb`, `Library/Homebrew/brew.sh` version constants, `Library/Homebrew/utils/os.sh` prefix selection, `Library/Homebrew/extend/os/mac/install.rb`, `Library/Homebrew/shims/super/cc`, `Library/Homebrew/os/mac/xcode.rb`, `Library/Homebrew/utils/portable_ruby.rb`, `Library/Homebrew/vendor/portable-ruby-x86_64-darwin`, `Library/Homebrew/github_runner_matrix.rb` (already portable-only for macOS x86_64 at the baseline), `Library/Homebrew/dev-cmd/generate-bottle-ci-matrix.rb` (still accepts `x86_64`).
- `bin/brew` UID check and wrapper removal.

## 7. Security files checked and left alone

These differ between `518895ab` and `origin/main`. None of them are how Intel is marked Tier 3. Do not revert them. Homebrew 7 sandbox work landed before and after tag `7.0.0` (`d79ef822ab`, 2026-09-13), including `ad26a60f5c` / `566c23545e` ("Refine macOS sandbox compatibility").

Sandbox:

- `Library/Homebrew/sandbox.rb`
- `Library/Homebrew/sandbox_operation.rb`
- `Library/Homebrew/extend/os/mac/sandbox.rb`
- `Library/Homebrew/extend/os/sandbox.rb` (present on main)
- `Library/Homebrew/extend/os/linux/sandbox.rb`
- `Library/Homebrew/extend/os/linux/sandbox/backend.rb`
- `Library/Homebrew/extend/os/linux/sandbox/landlock.rb`
- `Library/Homebrew/cmd/sandbox-exec.rb`

Attestation:

- `Library/Homebrew/attestation.rb`
- `Library/Homebrew/utils/attestation.rb`
- `Library/Homebrew/formula_installer.rb` method `verify_bottle_attestation?` (now any present tap, not only core; still skips formula `gh`). Widened by `95bfab038b` (2026-10-17).

Tap trust:

- `Library/Homebrew/trust.rb` (existed at the baseline; diff is +53/−15, not a rewrite to undo)
- `Library/Homebrew/cmd/trust.rb`
- `Library/Homebrew/cmd/untrust.rb`
- `Library/Homebrew/cmd/tap.rb`
- `Library/Homebrew/cmd/tap-info.rb`
- Tap-origin checks in `Library/Homebrew/diagnostic.rb` (`desired_git_remote?`, `examine_git_origin`, `Homebrew::EnvConfig.no_require_tap_trust?` / `require_tap_trust?`)

`brew vulns`:

- `Library/Homebrew/cmd/vulns.rb`
- `Library/Homebrew/vulns.rb`
- `Library/Homebrew/vulns/` (`advisory_database.rb`, `advisory_overrides.rb`, `cached_feed.rb`, `cpan_sec.rb`, `cvss.rb`, `history.rb`, `identify.rb`, `match.rb`, `osv.rb`, `osv_export.rb`, `output.rb`, `platform_independence.rb`, `purl.rb`, `repology.rb`, `scanner.rb`, `semver.rb`, `vulnerability.rb`)
- `Library/Homebrew/dev-cmd/generate-vulns-advisories.rb`
- `Library/Homebrew/dev-cmd/advisory-match.rb`
- `Library/Homebrew/utils/analytics/vulnerabilities.rb`

Also left alone inside `Library/Homebrew/extend/os/mac/diagnostic.rb`: `check_for_emudhra_certificates` (Apple advisory on compromised roots). Not an Intel tier check.

## 8. Portable Ruby x86_64 darwin artifact

Yes. Current `origin/main` still references an x86_64 Darwin portable-ruby artifact.

`Library/Homebrew/vendor/portable-ruby-version` is `4.0.9`.

`Library/Homebrew/vendor/portable-ruby-x86_64-darwin`:

```
ruby_TAG=big_sur
ruby_SHA=c90d3891dc3805de531cd262bc8f17382f499db0eb9185d76b8785ec93753fac
```

At `518895ab` the same file was `ruby_TAG=catalina` and `ruby_SHA=ef0bf45e34c07a111674b976e47da3f5e9d5be52eae127b714898ca6949a3c18`, version `4.0.6`. The artifact was updated (Catalina bottle tag → Big Sur, matching the new oldest allowed macOS). It was not removed.

`Library/Homebrew/cmd/vendor-install.sh` builds `portable-ruby-${HOMEBREW_PORTABLE_RUBY_VERSION}.${ruby_TAG}.bottle.tar.gz` and fetches `https://ghcr.io/v2/homebrew/core/portable-ruby/blobs/sha256:${ruby_SHA}` (also `HOMEBREW_ARTIFACT_DOMAIN` and `HOMEBREW_BOTTLE_DOMAIN` when set). For this file that is `portable-ruby-4.0.9.big_sur.bottle.tar.gz` at sha256 `c90d3891…`. `Library/Homebrew/utils/portable_ruby.rb` has no diff in this range. `github_runner_matrix.rb` still adds runner `macos-15-intel` ("macOS 11-cross x86_64") when the formula name starts with `portable-`.

The arm64 Darwin sibling is still separate: `Library/Homebrew/vendor/portable-ruby-arm64-darwin` has `ruby_TAG=arm64_big_sur`. Linux x86_64 is `Library/Homebrew/vendor/portable-ruby-x86_64-linux`. This report did not download the blob and did not clone homebrew-core. The reference is in the brew tree on `origin/main`.
