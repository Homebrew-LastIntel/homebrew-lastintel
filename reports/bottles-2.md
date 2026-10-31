# Intel bottle sample (second pass)

Fetch date: 2026-10-31. Local check of the saved responses finished 2026-10-31T19:27:53Z.
Host this report is about: x86_64, macOS 26.7.1 (Tahoe, build 25G241). Unprefixed bottle tags (`sonoma`, `sequoia`, `tahoe`, and the same pattern for other macOS codenames) are Intel. `arm64_*` does not run on this CPU. `x86_64_linux` is a Linux bottle, not a Mac Intel bottle.

Source: public JSON `https://formulae.brew.sh/api/formula/<name>.json` only (`bottle.stable.files` keys, plus `requirements`). `@` in a name was percent-encoded. Each HTTP 200 object's `generated_date` is 2026-10-31. `bottle.stable.root_url` is `https://ghcr.io/v2/homebrew/core` for all 82. Homebrew/core was not cloned. This is not the full catalogue.

Names were deduplicated before the fetch. Repeated mentions of `wget`, `curl`, `cmake`, `jq`, and `gh` were fetched once. 88 unique names. 82 returned HTTP 200. Six returned HTTP 404 and are not counted as pour or source-build.

A missing Intel macOS tag means Homebrew has no bottle for this CPU, so the install path in this table is a source build. It is not proof the upstream project dropped `x86_64-apple-darwin`.

Disposition rule, same names as [reports/bottles.md](bottles.md):

- `pour-old-bottle` — at least one unprefixed macOS tag is present, or the bottle tag is `all`. `all` is not an OS codename. It is Homebrew's tag for a bottle that is not tied to one OS or CPU, so it still pours on this Intel Mac. Both `all` bottles in this sample use cellar `:any_skip_relocation`. They are counted as pour, not as source-build. [reports/bottles.md](bottles.md) did not see an `all` tag; the rule there was only the unprefixed OS names.
- `source-build` — no unprefixed macOS tag and no `all` tag. Linux tags do not count. `arm64_*` does not count.
- `not classified` — the named URL returned HTTP 404. No second name was queried.

`versions.bottle` is true on all 82. That only means a bottle block exists. `pour_bottle_only_if` is null on all 82. None of the 82 is `deprecated` or `disabled`.

`arm64_golden_gate` is an Apple Silicon bottle tag. It is not an Intel tag. No bottle file in this sample uses an unprefixed `golden_gate` key. These notes do not say that macOS 27 runs on Intel.

## Counts

| | |
| --- | --- |
| Unique names requested | 88 |
| HTTP 200, classified | 82 |
| HTTP 404, not classified | 6 (`icu4c`, `pkg-config`, `choose`, `delta`, `terraform`, `kubectl`) |
| pour-old-bottle | 27 |
| source-build | 55 |
| explicit arch blocks | 1 (`podman`, also in the 55) |

Of the 27 pours, 25 have an unprefixed macOS tag and 2 are `all` only (`ca-certificates`, `meson`). 27 + 55 = 82. The arch block is not a third install path; it is the one JSON `requirements` object whose `name` is `arch`.

## Table

Intel tags are listed newest-first. Cellar is the JSON `cellar` for that tag. `arm64_linux` and `x86_64_linux` are omitted. For 80 formulae the Apple Silicon macOS tags include `arm64_golden_gate`, `arm64_tahoe`, and `arm64_sequoia`; those are not Intel and are omitted here. `ca-certificates` and `meson` have no `arm64_*` tag.

| formula | stable (revision, rebuild) | Intel tags | disposition | arch block |
| --- | --- | --- | --- | --- |
| git | 2.56.0 (rev 2, rebuild 0) | none | source-build | no |
| node | 26.13.0 (rev 2, rebuild 0) | none | source-build | no |
| python@3.13 | 3.13.18 (rev 0, rebuild 0) | none | source-build | no |
| openssl@3 | 3.5.9 (rev 0, rebuild 0) | none | source-build | no |
| cmake | 4.4.5 (rev 0, rebuild 0) | none | source-build | no |
| go | 1.27.3 (rev 0, rebuild 0) | none | source-build | no |
| rust | 1.99.1 (rev 0, rebuild 0) | none | source-build | no |
| ffmpeg | 9.0.2 (rev 1, rebuild 0) | none | source-build | no |
| llvm | 23.1.4 (rev 0, rebuild 0) | none | source-build | no |
| jq | 1.8.2 (rev 0, rebuild 1) | sonoma (cellar `:any`) | pour-old-bottle | no |
| ripgrep | 15.2.0 (rev 0, rebuild 1) | sonoma (cellar `:any`) | pour-old-bottle | no |
| wget | 1.25.0 (rev 2, rebuild 0) | none | source-build | no |
| gh | 2.103.3 (rev 0, rebuild 0) | none | source-build | no |
| ruby | 4.0.9 (rev 0, rebuild 0) | none | source-build | no |
| htop | 3.5.3 (rev 0, rebuild 0) | sonoma (cellar `:any`) | pour-old-bottle | no |
| curl | 8.23.0 (rev 0, rebuild 0) | none | source-build | no |
| python@3.14 | 3.14.10 (rev 0, rebuild 0) | none | source-build | no |
| openssl@4 | 4.1.0 (rev 0, rebuild 0) | none | source-build | no |
| sqlite | 3.54.1 (rev 0, rebuild 0) | none | source-build | no |
| xz | 5.8.4 (rev 0, rebuild 0) | none | source-build | no |
| zlib | 1.3.2 (rev 0, rebuild 0) | sonoma (cellar `:any`) | pour-old-bottle | no |
| pkgconf | 3.0.10 (rev 0, rebuild 0) | none | source-build | no |
| ca-certificates | 2026-10-20 (rev 0, rebuild 0) | `all` (cellar `:any_skip_relocation`; not an OS codename) | pour-old-bottle | no |
| readline | 8.3.6 (rev 0, rebuild 0) | none | source-build | no |
| gettext | 1.0 (rev 0, rebuild 2) | none | source-build | no |
| icu4c | HTTP 404 | — | not classified | no |
| libpng | 1.6.59 (rev 0, rebuild 0) | none | source-build | no |
| freetype | 2.15.0 (rev 0, rebuild 0) | none | source-build | no |
| harfbuzz | 15.0.0 (rev 1, rebuild 0) | none | source-build | no |
| glib | 2.90.1 (rev 0, rebuild 1) | none | source-build | no |
| pcre2 | 10.49 (rev 0, rebuild 0) | none | source-build | no |
| ncurses | 6.6 (rev 0, rebuild 0) | sonoma (cellar `/usr/local/Cellar`) | pour-old-bottle | no |
| expat | 2.9.0 (rev 0, rebuild 0) | none | source-build | no |
| libunistring | 1.5 (rev 0, rebuild 0) | none | source-build | no |
| libidn2 | 2.3.8 (rev 0, rebuild 0) | sonoma (cellar `/usr/local/Cellar`), ventura (cellar `/usr/local/Cellar`) | pour-old-bottle | no |
| brotli | 1.2.1 (rev 0, rebuild 0) | none | source-build | no |
| zstd | 1.5.7 (rev 1, rebuild 0) | tahoe (cellar `:any`), sequoia (cellar `:any`), sonoma (cellar `:any`) | pour-old-bottle | no |
| lz4 | 1.10.0 (rev 0, rebuild 3) | none | source-build | no |
| libarchive | 3.8.11 (rev 0, rebuild 0) | none | source-build | no |
| mpdecimal | 4.0.1 (rev 0, rebuild 1) | none | source-build | no |
| autoconf | 2.73 (rev 0, rebuild 0) | tahoe (cellar `:any_skip_relocation`), sequoia (cellar `:any_skip_relocation`), sonoma (cellar `:any_skip_relocation`) | pour-old-bottle | no |
| automake | 1.19 (rev 0, rebuild 0) | none | source-build | no |
| libtool | 2.6.2 (rev 0, rebuild 0) | tahoe (cellar `:any`), sequoia (cellar `:any`), sonoma (cellar `:any`) | pour-old-bottle | no |
| m4 | 1.4.21 (rev 0, rebuild 0) | tahoe (cellar `:any_skip_relocation`), sequoia (cellar `:any_skip_relocation`), sonoma (cellar `:any_skip_relocation`) | pour-old-bottle | no |
| bison | 3.8.2 (rev 0, rebuild 0) | sonoma (cellar `:any_skip_relocation`), ventura (cellar `:any_skip_relocation`), monterey (cellar `:any_skip_relocation`), big_sur (cellar `:any_skip_relocation`) | pour-old-bottle | no |
| flex | 2.6.4 (rev 2, rebuild 0) | sonoma (cellar `/usr/local/Cellar`), ventura (cellar `/usr/local/Cellar`), monterey (cellar `/usr/local/Cellar`), big_sur (cellar `/usr/local/Cellar`) | pour-old-bottle | no |
| nasm | 3.02 (rev 0, rebuild 0) | sonoma (cellar `:any_skip_relocation`) | pour-old-bottle | no |
| yasm | 1.3.0 (rev 2, rebuild 1) | sonoma (cellar `:any_skip_relocation`), ventura (cellar `:any_skip_relocation`), monterey (cellar `:any_skip_relocation`), big_sur (cellar `:any_skip_relocation`) | pour-old-bottle | no |
| ninja | 1.13.2 (rev 0, rebuild 1) | none | source-build | no |
| meson | 1.12.1 (rev 0, rebuild 1) | `all` (cellar `:any_skip_relocation`; not an OS codename) | pour-old-bottle | no |
| pkg-config | HTTP 404 | — | not classified | no |
| openssh | 10.6p1 (rev 1, rebuild 0) | none | source-build | no |
| rsync | 3.5.1 (rev 0, rebuild 1) | none | source-build | no |
| tmux | 3.8 (rev 0, rebuild 0) | none | source-build | no |
| vim | 9.2.1250 (rev 0, rebuild 0) | none | source-build | no |
| neovim | 0.12.6 (rev 0, rebuild 0) | none | source-build | no |
| emacs | 31.1 (rev 1, rebuild 0) | none | source-build | no |
| git-lfs | 3.8.1 (rev 0, rebuild 0) | none | source-build | no |
| hub | 2.14.2 (rev 0, rebuild 0) | sonoma (cellar `:any_skip_relocation`), ventura (cellar `:any_skip_relocation`), monterey (cellar `:any_skip_relocation`), big_sur (cellar `:any_skip_relocation`) | pour-old-bottle | no |
| fd | 10.5.0 (rev 0, rebuild 0) | sonoma (cellar `:any_skip_relocation`) | pour-old-bottle | no |
| bat | 0.26.1 (rev 0, rebuild 0) | sonoma (cellar `:any`) | pour-old-bottle | no |
| eza | 0.23.5 (rev 0, rebuild 0) | sonoma (cellar `:any`) | pour-old-bottle | no |
| dust | 1.2.6 (rev 0, rebuild 0) | none | source-build | no |
| bottom | 0.14.9 (rev 0, rebuild 0) | sonoma (cellar `:any_skip_relocation`) | pour-old-bottle | no |
| hyperfine | 2.0.0 (rev 0, rebuild 0) | none | source-build | no |
| tokei | 15.0.0 (rev 0, rebuild 0) | none | source-build | no |
| sd | 1.1.0 (rev 0, rebuild 0) | sonoma (cellar `:any_skip_relocation`) | pour-old-bottle | no |
| choose | HTTP 404 | — | not classified | no |
| tealdeer | 1.9.1 (rev 0, rebuild 0) | none | source-build | no |
| doggo | 1.4.2 (rev 0, rebuild 0) | none | source-build | no |
| bandwhich | 0.23.1 (rev 0, rebuild 2) | sonoma (cellar `:any_skip_relocation`) | pour-old-bottle | no |
| grex | 1.4.6 (rev 0, rebuild 0) | sonoma (cellar `:any_skip_relocation`) | pour-old-bottle | no |
| zoxide | 0.10.0 (rev 0, rebuild 0) | sonoma (cellar `:any_skip_relocation`) | pour-old-bottle | no |
| starship | 1.27.0 (rev 0, rebuild 0) | none | source-build | no |
| fzf | 0.74.5 (rev 0, rebuild 0) | none | source-build | no |
| delta | HTTP 404 | — | not classified | no |
| lazygit | 0.66.2 (rev 0, rebuild 0) | none | source-build | no |
| glab | 1.126.0 (rev 0, rebuild 0) | none | source-build | no |
| terraform | HTTP 404 | — | not classified | no |
| kubectl | HTTP 404 | — | not classified | no |
| helm | 4.3.1 (rev 0, rebuild 0) | none | source-build | no |
| k9s | 0.51.0 (rev 0, rebuild 0) | sonoma (cellar `:any_skip_relocation`) | pour-old-bottle | no |
| minikube | 1.39.0 (rev 0, rebuild 0) | none | source-build | no |
| docker | 29.9.2 (rev 0, rebuild 0) | none | source-build | no |
| colima | 0.10.3 (rev 0, rebuild 0) | sonoma (cellar `:any_skip_relocation`) | pour-old-bottle | no |
| podman | 6.1.4 (rev 0, rebuild 0) | none | source-build | yes |
| lima | 2.2.1 (rev 0, rebuild 0) | none | source-build | no |
| qemu | 11.1.3 (rev 0, rebuild 0) | none | source-build | no |

Four source-build formulae still have an `arm64_sonoma` file and no unprefixed `sonoma`: `mpdecimal`, `emacs`, `tokei`, `minikube`. That tag is Apple Silicon. It does not move them to pour-old-bottle.

Unprefixed `tahoe` (macOS 26) is present on four formulae only: `autoconf`, `libtool`, `m4`, and `zstd`. Each of those also has unprefixed `sequoia` and `sonoma`. No formula in this sample has an unprefixed `sequoia` or `tahoe` tag without `sonoma`.

## Explicit arch requirement

One classified formula has a `requirements` object with `"name": "arch"`.

`podman` 6.1.4 (rev 0, rebuild 0). Bottle files are `arm64_golden_gate`, `arm64_tahoe`, `arm64_sequoia`, `arm64_linux`, and `x86_64_linux`. No unprefixed macOS tag, so the bottle disposition stays source-build. The JSON also says the formula requires arm64. Quoted from `requirements`:

```json
{"name": "arch", "cask": null, "download": null, "version": "arm64", "contexts": [], "specs": ["stable", "head"]}
```

The same array has a macOS minimum. That object is not an arch block:

```json
{"name": "macos", "cask": null, "download": null, "version": "13", "contexts": [], "specs": ["stable", "head"]}
```

This is a Homebrew formula requirement in the API JSON. It is not, by itself, a statement that the podman upstream tree deleted an `x86_64-apple-darwin` target.

Two other formulae have a non-empty `requirements` array. Neither name is `arch`.

`go`:

```json
{"name": "macos", "cask": null, "download": null, "version": "12", "contexts": [], "specs": ["stable", "head"]}
```

`flex` (head spec only, no version):

```json
{"name": "macos", "cask": null, "download": null, "version": null, "contexts": [], "specs": ["head"]}
```

The other 79 classified formulae have `"requirements": []`.

## Variations are not bottle tags

Five formulae have unprefixed macOS keys under `variations`. Those keys are dependency lists. They were not counted as Intel bottle tags, and none of them is an arch requirement.

- `ffmpeg` variations `golden_gate`, `tahoe`, `sequoia`, `sonoma`, `ventura`, `monterey`, `big_sur` each set `build_dependencies` to `["pkgconf", "nasm"]`. Bottle files for `ffmpeg` have no unprefixed tag. The `golden_gate` key is that dependency list only. It is not a claim that macOS 27 runs on Intel.
- `llvm` variations `ventura`, `monterey`, `big_sur` (and the matching `arm64_*` keys) each set `dependencies` to `["zstd"]`. No unprefixed bottle tag.
- `curl` variations `monterey` and `big_sur` (and the matching `arm64_*` keys) replace the dependency list, including `openssl@4`. No unprefixed bottle tag.
- `libidn2` variations `monterey` and `big_sur` set `head_dependencies` only. Intel bottle tags remain `sonoma` and `ventura`.
- `hub` variations `monterey` and `big_sur` set `build_dependencies` to `["go"]`. Intel bottle tags remain `sonoma`, `ventura`, `monterey`, and `big_sur`.

## HTTP 404

These URLs returned a GitHub Pages 404. They are not source-build rows and not arch blocks. No alternate formula name was requested.

| name | URL | note |
| --- | --- | --- |
| icu4c | `https://formulae.brew.sh/api/formula/icu4c.json` | 404 |
| pkg-config | `https://formulae.brew.sh/api/formula/pkg-config.json` | 404, skipped as instructed. The `pkgconf` object lists aliases `pkg-config` and `pkgconfig`. `pkgconf` is already a row above. It was not counted twice. |
| choose | `https://formulae.brew.sh/api/formula/choose.json` | 404 |
| delta | `https://formulae.brew.sh/api/formula/delta.json` | 404 |
| terraform | `https://formulae.brew.sh/api/formula/terraform.json` | 404 |
| kubectl | `https://formulae.brew.sh/api/formula/kubectl.json` | 404 |

`openssl@4` returned HTTP 200 (`4.1.0`, no Intel macOS tag, source-build). The skip rule for that name did not apply.
