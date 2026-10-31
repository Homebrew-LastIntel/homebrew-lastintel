# Homebrew-LastIntel org check

Read-only. Confirmed with `gh` on 2026-10-31. No repos, forks, or pushes were created. `homebrew-core` and `homebrew-cask` were not cloned. No replacement installer was written.

## 1. Org `Homebrew-LastIntel`

Source: `gh api orgs/Homebrew-LastIntel` and `gh api orgs/Homebrew-LastIntel/memberships/gregorylejeune`.

| Field | Value |
| --- | --- |
| Login | `Homebrew-LastIntel` |
| URL | https://github.com/Homebrew-LastIntel |
| Description | Unofficial open-source Intel Mac continuation. Not affiliated with or endorsed by Homebrew. |
| Public repo count (`public_repos`) | 0 |
| Repo list (`gh repo list`, `GET /orgs/Homebrew-LastIntel/repos`) | empty (0) |
| `two_factor_requirement_enabled` | `false` |
| Plan | free |
| Created | 2026-10-31T01:16:14Z |
| Verified | false |

`gregorylejeune` is an admin.

- Membership: `role=admin`, `state=active`, `direct_membership=true`
- User id: 45644514
- Authenticated `gh` user at check time: `gregorylejeune`

## 2. Active Mac-relevant upstream repos

Source: GitHub GraphQL `repository` (owner `Homebrew`). `diskUsage` is the API value in kilobytes. None of these nine are archived. Default branch is `main` on all nine.

| Repository | SPDX | Archived | diskUsage (KB) | pushedAt | Default branch |
| --- | --- | --- | --- | --- | --- |
| Homebrew/brew | BSD-2-Clause | false | 129750 | 2026-10-31T18:57:24Z | main |
| Homebrew/homebrew-core | BSD-2-Clause | false | 1283279 | 2026-10-31T18:47:43Z | main |
| Homebrew/homebrew-cask | BSD-2-Clause | false | 532647 | 2026-10-31T19:04:18Z | main |
| Homebrew/BrewUI | AGPL-3.0 | false | 3028 | 2026-10-30T08:19:09Z | main |
| Homebrew/advisory-database | CC0-1.0 | false | 62653 | 2026-10-31T17:26:27Z | main |
| Homebrew/ruby-macho | MIT | false | 2538 | 2026-10-30T08:17:31Z | main |
| Homebrew/actions | BSD-2-Clause | false | 10026 | 2026-10-30T08:17:37Z | main |
| Homebrew/brew-pip-audit | BSD-2-Clause | false | 6395 | 2026-10-30T08:17:13Z | main |
| Homebrew/install | BSD-2-Clause | false | 1281 | 2026-10-30T08:22:07Z | main |

License names from the same query: BSD 2-Clause "Simplified" License, GNU Affero General Public License v3.0 (BrewUI), Creative Commons Zero v1.0 Universal (advisory-database), MIT License (ruby-macho).

## 3. `Homebrew/install` `install.sh` — macOS abort unless `arm64`

Fetched with `gh api repos/Homebrew/install/contents/install.sh?ref=main` (default branch `main`). Not modified.

- Blob SHA: `30615f54182a9d600455ad979783f3cebcdc58aa`
- Size: 35651 bytes
- HTML: https://github.com/Homebrew/install/blob/main/install.sh
- Tip of `main` at check: `8c82c081753d4263da0cfe615ef8d9433425f22b` (2026-10-30T08:22:05Z)

On Darwin, the script sets `UNAME_MACHINE` from `/usr/bin/uname -m` and aborts unless that value is `arm64`. Quoted from lines 167–173:

```sh
  UNAME_MACHINE="$(/usr/bin/uname -m)"

  # On macOS, support Apple Silicon only
  if [[ "${UNAME_MACHINE}" != "arm64" ]]
  then
    abort "Homebrew on macOS is only supported on Apple Silicon processors!"
  fi
```

That is the only abort that requires `uname -m` to be `arm64`. The later Linux check (around line 620) is a different condition: it aborts unless the machine is `x86_64` or `aarch64`. It is not quoted here. No replacement installer was written.

## 4. First repository to create (after reviewer sign-off)

Do not create it until a reviewer signs off. Do not create it in this pass.

Recommended single first repository:

- Owner: `Homebrew-LastIntel`
- Name: `homebrew-lastintel`
- Visibility: public
- Contents only: status reports, plus the BSD and AGPL notices that match the upstream licenses above (BSD-2-Clause for brew/core/cask/actions/brew-pip-audit/install; AGPL-3.0 for BrewUI).
- Must not contain a `curl | bash` installer.
- Must not be a fork of `homebrew-core` or `homebrew-cask`.

Name collision: no.

- `GET /repos/Homebrew-LastIntel/homebrew-lastintel` → 404
- `GET /repos/Homebrew/homebrew-lastintel` → 404
- `gh search repos homebrew-lastintel` → no matches
- `search/repositories?q=homebrew-lastintel+in:name` → no items
- The org currently has zero repositories, so the name is free on this owner.
