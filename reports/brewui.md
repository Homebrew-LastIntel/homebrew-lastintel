# BrewUI fork-readiness (no archive)

Date: 2026-10-31. Clone left at `/tmp/lastintel-brewui`. No push, no fork, no PR, no changes under `/usr/local`, no `homebrew-core` / `homebrew-cask` clone, no `xcodebuild archive`.

## Host and disk

- `/System/Volumes/Data` free before clone: 217 GiB. After the checks below: 216 GiB. Floor is 50 GiB. Did not stop.
- `uname -m`: `x86_64`. `sw_vers`: macOS 26.7.1 (25G241).
- `xcode-select -p`: `/Applications/Xcode.app/Contents/Developer`. `xcodebuild -version`: Xcode 26.6 (17F113).
- Active SDK from that Xcode: `MacOSX26.5.sdk` (macosx26.5), path `/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX26.5.sdk`. SDK `SupportedTargets.macosx.Archs` includes `x86_64`, `x86_64h`, `arm64`, `arm64e`.

## Clone

- First attempt used GitHub Desktop's `git` (`/Applications/GitHub Desktop.app/Contents/Resources/app/git/bin/git`). It failed: `git: 'remote-https' is not a git command` and `fatal: remote helper 'https' aborted session`. No directory was left behind.
- Successful clone used `/usr/bin/git` (Apple Git 2.50.1): `git clone https://github.com/Homebrew/BrewUI.git /tmp/lastintel-brewui`.
- HEAD: `587fcb808c6e45b11d5d25487239741f0f24bb7e` (`main...origin/main`). Subject: `Merge pull request #299 from Homebrew/dependabot/github_actions/github-actions-a3cda27434` (2026-10-30 16:19:06 +0800). Working tree clean. Size about 7.4 MiB. Full clone, not shallow.

## License (AGPL-3.0, as expected)

`/tmp/lastintel-brewui/LICENSE` lines 1–2:

```
                    GNU AFFERO GENERAL PUBLIC LICENSE
                       Version 3, 19 November 2007
```

`/tmp/lastintel-brewui/README.md` (Licence section):

```
[AGPL-3.0](LICENSE). If you reuse or adapt the source the AGPL terms apply, including the network-use clause.
```

App target copyright string, `Homebrew.xcodeproj/project.pbxproj` (Debug and Release of the Homebrew target, lines 759 and 788):

```
INFOPLIST_KEY_NSHumanReadableCopyright = "© Homebrew contributors. AGPLv3.";
```

## macOS deployment target

Two different floors. The Xcode app is **26.2**. The Swift package that the app links is **26.0**. The lint tool package is **14**.

`Package.swift` (BrewKit, swift-tools-version 6.2), lines 7–9:

```
    platforms: [
        .macOS("26.0"),
    ],
```

`Tools/BrewUILint/Package.swift` (swift-tools-version 6.0), line 6:

```
    platforms: [.macOS(.v14)],
```

`.swift-version` is `6.2`. `Configurations/App.xcconfig`, `Signing.xcconfig`, and `Version.xcconfig` do not set `MACOSX_DEPLOYMENT_TARGET` or any arch key.

Every `MACOSX_DEPLOYMENT_TARGET` in `Homebrew.xcodeproj/project.pbxproj` is `26.2` (8 assignments). Quoted with the target they belong to:

HomebrewUpgradeHelper Debug (line 555) and Release (line 591):

```
				MACOSX_DEPLOYMENT_TARGET = 26.2;
```

HomebrewAskpassHelper Debug (line 573) and Release (line 609):

```
				MACOSX_DEPLOYMENT_TARGET = 26.2;
```

PBXProject "Homebrew" Debug (line 677) and Release (line 738):

```
				MACOSX_DEPLOYMENT_TARGET = 26.2;
```

BrewTests Debug (line 811) and Release (line 829):

```
				MACOSX_DEPLOYMENT_TARGET = 26.2;
```

The Homebrew app target and the BrewUITests target do not set `MACOSX_DEPLOYMENT_TARGET`. They inherit the project value, 26.2. Resolved check (`xcodebuild -showBuildSettings -scheme Brew -destination 'platform=macOS,arch=x86_64'`) printed `MACOSX_DEPLOYMENT_TARGET = 26.2` for both Debug and Release. 26.2 is below this Mac's 26.7.1, so the deployment floor does not block running here. No archive was built to confirm a binary.

## ARCHS / EXCLUDED_ARCHS / ONLY_ACTIVE_ARCH

Searched `project.pbxproj`, both `Package.swift` files, the three xcconfigs, and the shared schemes.

- `ARCHS`: no assignment anywhere in those files.
- `EXCLUDED_ARCHS`: no assignment. Expanded build settings also have no `EXCLUDED_ARCHS` key (only unrelated `EXCLUDED_*` path patterns).
- `VALID_ARCHS`: not set in the project. The SDK/Xcode default that showed up was `VALID_ARCHS = arm64 arm64e i386 x86_64`.
- `ONLY_ACTIVE_ARCH`: exactly one project line, project-level Debug only.

`project.pbxproj` lines 677–681 (PBXProject "Homebrew", Debug, `EEC39EDE2F5AB4B900269514`):

```
				MACOSX_DEPLOYMENT_TARGET = 26.2;
				MTL_ENABLE_DEBUG_INFO = INCLUDE_SOURCE;
				MTL_FAST_MATH = YES;
				ONLY_ACTIVE_ARCH = YES;
				SDKROOT = macosx;
```

Project-level Release (`EEC39EDF2F5AB4B900269514`, lines 738–741) sets deployment target and `SDKROOT` and does **not** set `ONLY_ACTIVE_ARCH`:

```
				MACOSX_DEPLOYMENT_TARGET = 26.2;
				MTL_ENABLE_DEBUG_INFO = NO;
				MTL_FAST_MATH = YES;
				SDKROOT = macosx;
```

`Package.swift` and `Tools/BrewUILint/Package.swift` have no `ARCHS`, `EXCLUDED_ARCHS`, or `ONLY_ACTIVE_ARCH`.

Shared schemes `Brew.xcscheme`, `Brew-Unit.xcscheme`, and `Brew-UI.xcscheme` each say (line 8):

```
      buildArchitectures = "Automatic">
```

No scheme pins an architecture.

## Is an x86_64 build blocked, or does it follow the host?

**Not blocked.** Nothing excludes `x86_64`. On this Intel Mac a Debug build follows the host. A Release build is universal and still includes `x86_64`.

`xcodebuild -showdestinations -scheme Brew` (no compile, packages already resolved) listed:

```
{ platform:macOS, arch:x86_64, id:13476096-C194-51E3-B4A3-33847921B359, name:My Mac }
{ platform:macOS, name:Any Mac }
```

There is no arm64-only destination.

`xcodebuild -showBuildSettings -scheme Brew -destination 'platform=macOS,arch=x86_64'` (settings only, not a build):

| Configuration | ARCHS | ONLY_ACTIVE_ARCH | ARCHS_STANDARD | NATIVE_ARCH |
| --- | --- | --- | --- | --- |
| Debug | `x86_64` | `YES` (from the project) | `arm64 x86_64` | `x86_64` |
| Release | `arm64 x86_64` | `NO` (unset in the project; Xcode default) | `arm64 x86_64` | `x86_64` |

Debug's single `ONLY_ACTIVE_ARCH = YES` narrows `ARCHS` to the active run destination. That destination is this Mac, so Debug is `x86_64`, not a cross-compile to arm64 and not a refusal. Release does not set `ONLY_ACTIVE_ARCH`, so it keeps `ARCHS_STANDARD` (`arm64 x86_64`) and would try a universal binary, which still contains `x86_64`. CI (`.github/workflows/pr_build_test.yml`) uses `-destination "platform=macOS"` with no arch flag, on `macos-26`. That also does not exclude Intel. No archive was run, so a finished x86_64 binary was not produced.

## What was executed, and what was skipped

- Ran `xcodebuild -list` and `-showdestinations`, then `-showBuildSettings` for Debug and Release. No `archive`, no `test`, no `bootstrap`.
- `-list` still resolved the package graph even with `-disableAutomaticPackageResolution -skipPackageUpdates`. It fetched the pins already in `Package.resolved`: `swift-subprocess` 1.0.0, `swift-system` 1.8.0, and `swift-syntax` 602.0.0 (a prebuilt MacroSupport zip from `download.swift.org`, not a full Swift toolchain). Cache after that: `~/Library/Developer/Xcode/DerivedData/Homebrew-emshochonnaotgatnvwnapajrdxr/SourcePackages` about 204 MiB, DerivedData about 416 MiB, `~/Library/Caches/org.swift.swiftpm` about 70 MiB. Did not delete those caches.
- Did **not** run `./scripts/bootstrap`. That script installs Homebrew with the upstream `install.sh` if `brew` is missing, then `brew bundle` (Mint) and `mint bootstrap` (SwiftFormat / SwiftLint). That is a network toolchain install and was skipped on purpose. Mint was not run.

## BrewUI needs `brew`, and `brew` is not here

README line 15: data comes from the `brew` CLI and the Homebrew JSON API (`https://formulae.brew.sh/docs/api/`).

`Sources/BrewNetworking/BrewAPIClient.swift` defaults the base URL to `https://formulae.brew.sh` and requests `/api/formula.json`, `/api/cask.json`, plus install-analytics JSON. That catalogue can load without a local `brew`. It does not install, upgrade, uninstall, or doctor anything.

`Sources/BrewCLI/BrewExecutableLocator.swift` lines 31–39:

```
    /// Tries Apple Silicon default, then Intel default.
    public func findBrewExecutable() throws -> URL {
        ...
        let candidates = [
            URL(fileURLWithPath: "/opt/homebrew/bin/brew"),
            URL(fileURLWithPath: "/usr/local/bin/brew"),
        ]
```

Checked on this Mac: `brew` is not on `PATH`. `/opt/homebrew/bin/brew`, `/usr/local/bin/brew`, and `/usr/local/Homebrew` do not exist. The locator would throw `BrewLookupError.executableNotFound`. The Intel path this host needs is the second candidate, and it is absent.

## Should the first GitHub push of a BrewUI fork wait?

**Yes. Wait until `brew` is on this machine.**

A git push does not need `brew`, and an x86_64 Debug build is not blocked by the project. The app is still useless until the CLI exists: install, upgrade, uninstall, services, doctor, and config all go through `brew`, and this Mac has neither prefix the locator searches. `./scripts/bootstrap` is the documented setup and it will not finish without Homebrew (if run now it would try to install Homebrew itself). Push the fork only after `brew` is installed at `/usr/local/bin/brew` (Intel prefix for this x86_64 host) and a real launch can see it. Do not treat the formulae JSON API as a substitute.
