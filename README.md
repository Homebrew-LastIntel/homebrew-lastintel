# Homebrew-LastIntel

Unofficial work to keep Homebrew usable on Intel Macs. Not affiliated with or endorsed by Homebrew. Do not send this project money that you mean for [upstream Homebrew](https://github.com/sponsors/Homebrew).

This repository is the index. It is not a fork of one upstream repository. The forks are separate.

macOS Tahoe 26 is the last macOS that runs on Intel. Nothing here says that macOS 27 runs on Intel.

## Goals

1. Measure from the last Intel-supported `brew` and carry every later upstream change forward.
2. Convert changes that drop Intel, so an Intel Mac on macOS 15 or 26 can still install software.
3. Keep upstream's security work: the sandbox, bottle attestations, tap trust, and `brew vulns`.
4. Build BrewUI for the Intel Mac, under the AGPL-3.0 license it already has.
5. Leave `homebrew-core` and `homebrew-cask` on Homebrew's servers. Install a new formula by its upstream name. Copy a formula into a tap here only when the upstream formula blocks Intel and the source still builds.

The baseline client commit is [`518895ab7be7407523990f86eb6a1a53eb35a72f`](https://github.com/Homebrew/brew/commit/518895ab7be7407523990f86eb6a1a53eb35a72f) (2026-09-01, parent of the Tier 3 change). Release **6.0.20** (2026-08-27) is the last release from before the Intel bottle builders were turned off on 2026-08-28. We do not run 6.0.20 as the daily client.

Upstream's reason, in their words, is on [discussion 7044](https://github.com/orgs/Homebrew/discussions/7044): Apple ended Intel at macOS 27, GitHub is removing Intel Mac runners in 2027, the project is volunteers, and their Intel CI virtual machines became unusably slow. That CI failure was their fleet. It is not a defect that stops this Mac from compiling software.

## Repositories

| Repository | Forked from | License | Role |
| --- | --- | --- | --- |
| [brew](https://github.com/Homebrew-LastIntel/brew) | [Homebrew/brew](https://github.com/Homebrew/brew) at `8a17c207` | BSD-2-Clause | The package manager. Instructions are in that README. |
| [BrewUI](https://github.com/Homebrew-LastIntel/BrewUI) | [Homebrew/BrewUI](https://github.com/Homebrew/BrewUI) at `587fcb80` | AGPL-3.0 | The Mac app. Build steps are in that README. |
| [homebrew-lastintel](https://github.com/Homebrew-LastIntel/homebrew-lastintel) | This repo. Not a fork. | The notes are ours. Formulae we add later keep the upstream formula license. | Checks, and any formula we had to adjust. |

These stay upstream. We do not fork them and we do not clone them onto the Mac:

| Upstream | Why it stays there |
| --- | --- |
| [Homebrew/homebrew-core](https://github.com/Homebrew/homebrew-core) | About 1.22 GiB and hundreds of merges a day. Bottles and formulae come from `https://formulae.brew.sh` and `ghcr.io`. |
| [Homebrew/homebrew-cask](https://github.com/Homebrew/homebrew-cask) | Casks are the vendor's binaries. |
| [Homebrew/advisory-database](https://github.com/Homebrew/advisory-database) | CC0. `brew vulns` reads it from upstream. |
| [Homebrew/ruby-macho](https://github.com/Homebrew/ruby-macho) | MIT library already vendored by `brew`. |
| [Homebrew/actions](https://github.com/Homebrew/actions) | Used when we publish a bottle. BSD-2-Clause. |
| [Homebrew/brew-pip-audit](https://github.com/Homebrew/brew-pip-audit) | Used if `brew vulns` misses a Python package we installed. |
| [Homebrew/install](https://github.com/Homebrew/install) | The official script aborts on Intel. We do not publish a rewritten copy. |

## Install the client

Follow [Homebrew-LastIntel/brew](https://github.com/Homebrew-LastIntel/brew#install-on-an-intel-mac). Short version: the official installer refuses Intel, so you clone that repository into `/usr/local/Homebrew` and symlink `brew`. You need sudo for the new directories. The commands do not delete software already in `/usr/local`.

Then:

```bash
brew update
brew config
brew doctor
brew install <formula>
```

`brew config` should show prefix `/usr/local` and an Intel CPU. A formula with no Intel bottle compiles from source. Do not set `HOMEBREW_NO_VERIFY_ATTESTATIONS`, `HOMEBREW_NO_SANDBOX`, or `HOMEBREW_NO_REQUIRE_TAP_TRUST`.

## Build the app

Follow [Homebrew-LastIntel/BrewUI](https://github.com/Homebrew-LastIntel/BrewUI#build-on-this-mac) after `brew` is on your `PATH`. The build is `xcodebuild` for `arch=x86_64`. The license stays AGPL-3.0.

## How updates move

One repository at a time.

- `brew`: fetch `https://github.com/Homebrew/brew.git` and merge `main`. Keep Intel behavior. Take upstream features.
- `BrewUI`: fetch `https://github.com/Homebrew/BrewUI.git` and merge `main`. Keep `x86_64` in the build. Keep the AGPL.
- Formulae: `brew update` picks up upstream. If one formula we maintain is behind, start from their new file and put back only the Intel build behavior from 2026-08-27. Their new URL and checksum stay verbatim.
- A change that needs the macOS 27 SDK is not converted. Record the commit and stay on the last merge that still builds here.

## Checks already in this repo

| Report | What it checked |
| --- | --- |
| [reports/brew-baseline.md](reports/brew-baseline.md) | Diff from `518895ab` to upstream `8a17c207`. |
| [reports/brewui.md](reports/brewui.md) | BrewUI's Xcode project does not block `x86_64`. |
| [reports/bottles.md](reports/bottles.md) | Fifteen formulae. Twelve had no Intel bottle tag on 2026-10-31. |
| [reports/org.md](reports/org.md) | Licenses and the installer abort. |
| [reports/review.md](reports/review.md) | Review before the first push. |

## License names

BSD-2-Clause for `brew`, `homebrew-core`, `homebrew-cask`, `actions`, `brew-pip-audit`, and `install`. AGPL-3.0 for BrewUI. CC0-1.0 for the advisory database. MIT for ruby-macho.
