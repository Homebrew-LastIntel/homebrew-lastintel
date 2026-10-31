# Intel bottle sample (15 formulae)

Fetch date: 2026-10-31T19:06:03Z
Host this report is about: x86_64, macOS 26.7.1 (Tahoe). Unprefixed bottle tags (`sonoma`, `sequoia`, `tahoe`, and the same pattern for other macOS codenames) are Intel. `arm64_*` does not run on this CPU. `x86_64_linux` is a Linux bottle, not a Mac Intel bottle.

Source: public JSON `https://formulae.brew.sh/api/formula/<name>.json` (`bottle.stable.files` keys). Each object’s `generated_date` is 2026-10-31. HTTP 200 for all 15. This is a 15-formula sample, not the full catalogue. Homebrew/core was not cloned.

A missing Intel macOS tag means Homebrew has no bottle for this CPU, so the install path is a source build. It is not proof the upstream project dropped x86_64.

Disposition rule used here:

- `pour-old-bottle` — at least one unprefixed macOS tag is present. None of these 15 have `tahoe` (or `sequoia` / `golden_gate`) Intel tags. Current `brew` main (`8a17c2078a1a2f91248915546216783978399e0c`, 2026-10-31) will still select an older same-arch tag: `Utils::Bottles::Collector#find_older_compatible_tag` in `Library/Homebrew/extend/os/mac/utils/bottles.rb` accepts a candidate whose macOS version is `<=` the host tag and whose `standardized_arch` matches. `:sonoma` parses as x86_64 (`Tag.from_symbol` defaults a missing arch prefix to `:x86_64`).
- `source-build` — no unprefixed macOS tag. Linux tags do not count.
- `unknown` — not used. Every response included a stable bottle map.

`versions.bottle: true` on all 15 only means a bottle block exists. It does not mean an Intel macOS bottle exists.

## Table

| formula | stable (revision, rebuild) | Intel macOS tags | arm64 macOS tags | disposition |
| --- | --- | --- | --- | --- |
| git | 2.56.0 (rev 2, rebuild 0) | absent | arm64_golden_gate, arm64_tahoe, arm64_sequoia | source-build |
| node | 26.13.0 (rev 2, rebuild 0) | absent | arm64_golden_gate, arm64_tahoe, arm64_sequoia | source-build |
| python@3.13 | 3.13.18 (rev 0, rebuild 0) | absent | arm64_golden_gate, arm64_tahoe, arm64_sequoia | source-build |
| openssl@3 | 3.5.9 (rev 0, rebuild 0) | absent | arm64_golden_gate, arm64_tahoe, arm64_sequoia | source-build |
| cmake | 4.4.5 (rev 0, rebuild 0) | absent | arm64_golden_gate, arm64_tahoe, arm64_sequoia | source-build |
| go | 1.27.3 (rev 0, rebuild 0) | absent | arm64_golden_gate, arm64_tahoe, arm64_sequoia | source-build |
| rust | 1.99.1 (rev 0, rebuild 0) | absent | arm64_golden_gate, arm64_tahoe, arm64_sequoia | source-build |
| ffmpeg | 9.0.2 (rev 1, rebuild 0) | absent | arm64_golden_gate, arm64_tahoe, arm64_sequoia | source-build |
| llvm | 23.1.4 (rev 0, rebuild 0) | absent | arm64_golden_gate, arm64_tahoe, arm64_sequoia | source-build |
| jq | 1.8.2 (rev 0, rebuild 1) | present: sonoma (`cellar: :any`) | arm64_golden_gate, arm64_tahoe, arm64_sequoia, arm64_sonoma | pour-old-bottle |
| ripgrep | 15.2.0 (rev 0, rebuild 1) | present: sonoma (`cellar: :any`) | arm64_golden_gate, arm64_tahoe, arm64_sequoia, arm64_sonoma | pour-old-bottle |
| wget | 1.25.0 (rev 2, rebuild 0) | absent | arm64_golden_gate, arm64_tahoe, arm64_sequoia | source-build |
| gh | 2.103.3 (rev 0, rebuild 0) | absent | arm64_golden_gate, arm64_tahoe, arm64_sequoia | source-build |
| ruby | 4.0.9 (rev 0, rebuild 0) | absent | arm64_golden_gate, arm64_tahoe, arm64_sequoia | source-build |
| htop | 3.5.3 (rev 0, rebuild 0) | present: sonoma (`cellar: :any`) | arm64_golden_gate, arm64_tahoe, arm64_sequoia, arm64_sonoma | pour-old-bottle |

Every formula in this sample also has `arm64_linux` and `x86_64_linux`. Those are not Mac bottles and are omitted from the two tag columns.

Counts: 3 pour-old-bottle (jq, ripgrep, htop, Intel tag `sonoma` only), 12 source-build, 0 unknown. No `tahoe` Intel tag in the sample.

## depends_on / arch requirements in the API

No formula in this sample has a CPU-arch requirement in the JSON (`requirements` is empty except `go`; nothing quotes `:arch`, `x86_64`-only, or `arm64`-only). `pour_bottle_only_if` is null for all 15.

`go` is the only non-empty `requirements` entry. It is a macOS minimum, not an arch constraint:

```json
{"name": "macos", "cask": null, "download": null, "version": "12", "contexts": [], "specs": ["stable", "head"]}
```

`variations` are not bottle tags. Two formulae have unprefixed macOS keys there, and they are dependency lists only:

- `ffmpeg` variations `golden_gate`, `tahoe`, `sequoia`, `sonoma`, `ventura`, `monterey`, `big_sur`: each sets `build_dependencies` to `["pkgconf", "nasm"]`. Bottle files for `ffmpeg` are still only the arm64 macOS tags plus the two Linux tags.
- `llvm` variations `ventura`, `monterey`, `big_sur` and the matching `arm64_*` keys each add dependency `zstd`. Bottle files for `llvm` do not include those tags.

Other `variations` keys in the sample are `x86_64_linux` / `arm64_linux` only.

## Intel missing-bottle behavior (confirmed in current brew, not only the PR text)

[Homebrew/brew PR 23679](https://github.com/Homebrew/brew/pull/23679) (“Reduce Intel macOS to Tier 3”, merged 2026-09-01) said Intel should fall back to source builds instead of a hard `no bottle available!` error. That is still what `main` does, checked via `gh` at `8a17c2078a1a2f91248915546216783978399e0c` (2026-10-31T18:44:09Z). Homebrew/core was not cloned.

- `Library/Homebrew/extend/os/mac/formula_installer.rb`: `fresh_install?` is true only when `::Hardware::CPU.arm?` and the install is not a developer or outdated-release case and `!formula.any_version_installed?`.
- `Library/Homebrew/formula_installer.rb` `check_install_sanity`: the `#{formula}: no bottle available!` `CannotInstallFormulaError` is built only in the `elsif fresh_install?(formula)` branch (default prefix, core tap, not `--build-from-source` / bottle build / HEAD, and `pour_bottle?` is false). The base `fresh_install?` is `false`. On Intel the macOS override is also false, so that error is not raised and the install continues into a source build.
- `Library/Homebrew/test/os/mac/formula_installer_spec.rb` still has the example “is false on Intel”.
- Drift from the merged PR diff: current `fresh_install?` no longer includes `installed_on_request?`; it only checks `!formula.any_version_installed?`. The `Hardware::CPU.arm?` gate is unchanged.
- Still a hard error on every CPU: `` `--force-bottle` passed but #{formula.full_name} has no bottle! `` when `force_bottle? && !pour_bottle?`. That path was not the PR 23679 change.
