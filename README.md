# Homebrew-LastIntel

Unofficial status notes for Intel Macs. This org is not affiliated with or endorsed by Homebrew.

These pages record public upstream status only. They are not an installer and they do not publish formulae.

Upstream licenses named here: BSD-2-Clause, AGPL-3.0 for BrewUI, CC0 for the advisory database, and MIT for ruby-macho.

macOS Tahoe 26 is the last release that runs on Intel. These notes do not say that macOS 27 runs on Intel.

## How the work was split

Four workers, then one reviewer. One subject each. Nothing here is a fork of `homebrew-core` or `homebrew-cask`.

| Worker | Subject | Report |
| --- | --- | --- |
| brew-baseline | Diff of Homebrew/brew from the last Intel-supported commit onto current main | [reports/brew-baseline.md](reports/brew-baseline.md) |
| brewui | Whether BrewUI's Xcode project blocks x86_64 | [reports/brewui.md](reports/brewui.md) |
| catalogue | Bottle tags for 15 formulae on the public API | [reports/bottles.md](reports/bottles.md) |
| org | Org state, licenses, and the installer abort | [reports/org.md](reports/org.md) |
| reviewer | Spot-check before this push | [reports/review.md](reports/review.md) |

Checked 2026-10-31. `Homebrew/brew` main at the time of the diff was `8a17c2078a1a2f91248915546216783978399e0c`. The Intel baseline commit is `518895ab7be7407523990f86eb6a1a53eb35a72f`.
