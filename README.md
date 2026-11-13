# RE-Frame-UCLL/.github

Shared defaults for every repo in the RE-Frame-UCLL organisation.

| Path | What |
| ---- | ---- |
| `profile/README.md` | The organisation profile page |
| `CONTRIBUTING.md`, `SECURITY.md` | Default contributing guide and security policy for every repo without its own |
| `.github/ISSUE_TEMPLATE/`, `.github/pull_request_template.md` | Default issue forms and pull-request template |
| `.github/workflows/check.yml` | Reusable pull-request check |
| `.github/workflows/release.yml` | Reusable changesets release to GitHub Packages |
| `workflow-templates/` | The two caller workflows, offered under **Actions → New workflow** in every repo |

## Use the shared CI in a repo

Add two small files. Both are also offered as workflow templates ("RE-Frame check" and
"RE-Frame release") under **Actions → New workflow**.

`.github/workflows/check.yml`:

```yaml
name: check
on:
  pull_request:
  workflow_dispatch:
permissions:
  contents: read
  packages: read
concurrency:
  group: check-${{ github.ref }}
  cancel-in-progress: true
jobs:
  check:
    uses: RE-Frame-UCLL/.github/.github/workflows/check.yml@main
```

`.github/workflows/release.yml` (only in repos that publish packages):

```yaml
name: release
on:
  push:
    branches: [main]
permissions:
  contents: write
  pull-requests: write
  packages: write
  actions: write
concurrency:
  group: release
  cancel-in-progress: false
jobs:
  release:
    uses: RE-Frame-UCLL/.github/.github/workflows/release.yml@main
```

Keep the job id `check` and the file name `check.yml`: the organisation ruleset on `main`
requires the status check **`check / result`**, and the release workflow starts
`check.yml` on the "Version packages" pull request.

## What `check` runs

| Job | Does | Skipped when |
| --- | ---- | ------------ |
| `lint` | `npm run lint` (ESLint with the RE-Frame preset), then `npm run rf -- lint` if the repo has an `rf` script (a notice until the rf CLI exists) | no `package.json` |
| `types` | `npm run types`: generates `.d.ts` from the JSDoc, fails on JSDoc errors | no `package.json` |
| `test` | `npm test` | no `package.json` |
| `changeset` | `npx changeset status --since=origin/main`: fails when a changed package has no changeset | not a pull request, no `.changeset/config.json`, or the "Version packages" PR |
| `licence header` | every tracked `.js`/`.mjs`/`.cjs` file starts with `// Copyright <year> UCLL` and `// SPDX-License-Identifier: Apache-2.0` | never |
| `result` | green when every job above passed or was skipped; this is the required check | never |

Inputs (all optional): `node-version` (default `26`), `lint-command`, `test-command`,
`types-command` (an empty string skips that step), `rf-lint` (boolean), `header-exclude`
(regular expression of paths without a header, for example vendored files).

So a repo that uses `check` needs `npm run lint`, `npm test` and `npm run types` scripts
and a committed `package-lock.json`, or passes an empty command for what it does not have.

## What `release` does

On every push to `main`: `npm ci`, the tests, then
[changesets/action](https://github.com/changesets/action):

- with unreleased changesets it opens or updates the **Version packages** pull request
  (versions and `CHANGELOG.md`), and starts `check.yml` on it, because pull requests
  opened with `GITHUB_TOKEN` start no workflows by themselves;
- once a core maintainer merges that pull request, it publishes every package whose
  version is not yet on GitHub Packages (`npm.pkg.github.com`, scope `@re-frame-ucll`)
  and tags it (`re-frame@5.1.0`).

It uses the built-in `GITHUB_TOKEN` with `packages: write`; no token is stored.
Each package needs `"publishConfig": { "registry": "https://npm.pkg.github.com" }` and a
`repository` field that points to its repo in RE-Frame-UCLL.

## Licence

Apache License 2.0, see [LICENSE](LICENSE) and [NOTICE](NOTICE).
