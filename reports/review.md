# Review for the first public push

Verdict: approve with cuts.

Push only `brew-baseline.md`, `brewui.md`, `bottles.md`, `org.md`, and the five-line README below. Do not push the other files in `/tmp/lastintel-reports` (`formula_installer.rb`, `bottles.rb`, `mac_bottles.rb`, and the formula JSON dumps). Those are working copies, not reports.

## Cuts

Leave this sentence out of `brew-baseline.md` section 6, item 7, before the file is committed:

> Tier 1 macOS must include Intel x86_64 at `/usr/local` again for the current supported releases (Sequoia, Tahoe, Golden Gate), not only Apple Silicon.

That sentence lists Golden Gate (macOS 27) as an Intel Tier 1 release. Publishing it would claim macOS 27 runs on Intel. Upstream `docs/Support-Tiers.md` at `8a17c2078a1a2f91248915546216783978399e0c` says macOS Tahoe 26 is the final macOS that runs on Intel hardware. The rest of item 7 can stay. Section 4 can stay: it correctly attributes Golden Gate 27 to Apple Silicon Tier 1, and it does not say Intel boots 27.

The section 6 opener that names Golden Gate as Homebrew's newest supported version may stay. `Library/Homebrew/brew.sh` at that same commit sets `HOMEBREW_MACOS_NEWEST_SUPPORTED="27"`. That is a global version ceiling, not a claim that this Intel Mac runs macOS 27.

No other paragraph has to come out.

## Hard-stop scan

After that cut, none of the reject conditions remain.

- No report contains a `curl | bash` installer or a patched `install.sh`. `org.md` quotes the upstream abort at `Homebrew/install` `install.sh` lines 167–173 and says not to ship an installer. That quote matches the blob. `brewui.md` only says `./scripts/bootstrap` was not run.
- No report tells anyone to set `HOMEBREW_NO_VERIFY_ATTESTATIONS`, `HOMEBREW_NO_SANDBOX`, or `HOMEBREW_NO_REQUIRE_TAP_TRUST`. `brew-baseline.md` names `no_require_tap_trust?` as code to leave alone.
- No report says to fork or clone `homebrew-core` or `homebrew-cask` onto this Mac. All four say those clones were not made. `org.md` says the new repo must not be a fork of either. `brewui.md` is about a future BrewUI fork, and it says to wait.
- No report says Intel support is impossible. `bottles.md` treats a missing Intel bottle as a source build. `brew-baseline.md` says current `brew` still starts on Intel.
- The macOS 27 problem is only the sentence cut above.
- Load-bearing claims carry SHAs or URLs. The checks below matched, except the same-day snapshot drift and the items marked not rechecked.

## Spot-checks

Checked with `gh` or the public formula API on 2026-10-31.

Matched:

- Org `Homebrew-LastIntel`: description, `public_repos` 0, empty repo list, `two_factor_requirement_enabled` false, plan free, created `2026-10-31T01:16:14Z`, verified false. `gregorylejeune` is an active direct admin, user id 45644514. `Homebrew-LastIntel/homebrew-lastintel` and `Homebrew/homebrew-lastintel` are 404. `gh search repos homebrew-lastintel` returned no matches.
- `Homebrew/install` `main` is `8c82c081753d4263da0cfe615ef8d9433425f22b` (2026-10-30T08:22:05Z). `install.sh` blob `30615f54182a9d600455ad979783f3cebcdc58aa`, 35651 bytes. Lines 167–173 match the quote. The Linux abort is line 622; the report said "around line 620".
- Brew commits: `518895ab7be7407523990f86eb6a1a53eb35a72f` (2026-08-31T19:37:39Z), `8a17c2078a1a2f91248915546216783978399e0c` (2026-10-31T18:44:09Z), merge `d7daec068877135f991a864cff094e7d25bca5b3` with parents `518895ab…` and `c0c92cc7b791bd4e429a71a56d5f440e52393314`. `c0c92cc7` committer time `2026-08-31T19:54:56Z` is the report's `20:54:56 +0100`. Compare `518895ab...8a17c207` is ahead by 1247. `d7daec06` is an ancestor of that `main` (ahead by 1245). Tag `6.0.20` is lightweight commit `3e3da86e4eaccd23f4ce43df9b0a31b16c707347`. Tag `7.0.0` is `d79ef822ab8136e393ed5f86e2b56afc68d04874`. Tag `7.0.11` is `c6069d2a943b71b0a90d91fc1ac93cb9c1634dcc` and is the newest tag returned.
- Portable Ruby on `8a17c207`: `Library/Homebrew/vendor/portable-ruby-version` is `4.0.9`; `portable-ruby-x86_64-darwin` is `ruby_TAG=big_sur` and sha256 `c90d3891dc3805de531cd262bc8f17382f499db0eb9185d76b8785ec93753fac`. At `518895ab` the same file is `ruby_TAG=catalina`, sha256 `ef0bf45e34c07a111674b976e47da3f5e9d5be52eae127b714898ca6949a3c18`, version `4.0.6`.
- `fresh_install?` on `8a17c207` is `Hardware::CPU.arm?` plus not outdated and `!formula.any_version_installed?`. `os.rb` sets `ISSUES_URL` for the ARM default prefix only. `brew.sh` has newest supported 27, newest unsupported 28, oldest supported 15, oldest allowed 11.
- All 15 formula rows match `https://formulae.brew.sh/api/formula/<name>.json`: versions, revisions, rebuilds, Intel tags (only `sonoma` on jq, ripgrep, and htop, cellar `:any`), arm64 tags, and `generated_date` 2026-10-31. `go`'s only requirement is macOS 12. `pour_bottle_only_if` is null. `ffmpeg` variations for those macOS names set `build_dependencies` to `pkgconf` and `nasm`, and its bottle files are arm64 macOS plus the two Linux tags.
- Licenses and default branch `main`, none archived: brew, homebrew-core, homebrew-cask, actions, brew-pip-audit, and install are BSD-2-Clause; BrewUI is AGPL-3.0; advisory-database is CC0-1.0; ruby-macho is MIT. diskUsage and pushedAt match the table for brew (129750, 2026-10-31T18:57:24Z), BrewUI, advisory-database, ruby-macho, actions, brew-pip-audit, and install.
- BrewUI `587fcb808c6e45b11d5d25487239741f0f24bb7e` is that merge of PR 299. Committer `2026-10-30T08:19:06Z` is the report's `16:19:06 +0800`. `Package.swift` platforms are macOS 26.0. `project.pbxproj` has eight `MACOSX_DEPLOYMENT_TARGET = 26.2` assignments, `ONLY_ACTIVE_ARCH = YES` at the project Debug config, and the AGPLv3 copyright strings at lines 759 and 788. No `EXCLUDED_ARCHS` line in that file.
- This host is `x86_64`, macOS 26.7.1 (25G241), Xcode 26.6 (17F113), Command Line Tools path `/Applications/Xcode.app/Contents/Developer`. `/opt/homebrew/bin/brew`, `/usr/local/bin/brew`, and `/usr/local/Homebrew` are absent, and `brew` is not on `PATH`. `/System/Volumes/Data` now has 216 GiB available, which matches the post-check figure in `brewui.md`.

Not reconfirmed, so do not treat these as freshly checked:

- `homebrew-core` diskUsage is still 1283279, but `pushedAt` is now `2026-10-31T19:13:10Z`, not the reported `2026-10-31T18:47:43Z`.
- `homebrew-cask` is now diskUsage 532697 and `pushedAt` `2026-10-31T19:16:04Z`, not 532647 and `2026-10-31T19:04:18Z`. Same-day drift. The license, archived flag, and `main` branch still match.
- `versions.bottle: true` was rechecked for git, jq, htop, ffmpeg, and go only. The other ten were checked for version and bottle tags, not that flag.
- These were not re-fetched one by one: the section 5 subject list beyond `c0c92cc7` and `d7daec06`, `Distribution.xml`, the CI workflow SHAs, `10116fe6`, `883aaedc`, `c5715c40`, and `96da617781`.
- Not re-run: `xcodebuild -showdestinations`, `-showBuildSettings`, the SDK `MacOSX26.5.sdk` arch list, DerivedData and SwiftPM cache sizes, the BrewUI lint package's macOS 14 floor, and the 217 GiB figure from before the clones.

## README

The parent may commit this as `README.md`. It is five lines and has no install command.

```
# Homebrew-LastIntel
Unofficial status notes for Intel Macs. This org is not affiliated with or endorsed by Homebrew.
These pages record public upstream status only. They are not an installer and they do not publish formulae.
Upstream licenses named here: BSD-2-Clause, AGPL-3.0 for BrewUI, CC0 for the advisory database, and MIT for ruby-macho.
macOS Tahoe 26 is the last release that runs on Intel. These notes do not say that macOS 27 runs on Intel.
```
