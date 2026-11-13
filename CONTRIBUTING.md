# Contributing to RE-Frame

Thank you for helping. These rules hold for every repo in
[RE-Frame-UCLL](https://github.com/RE-Frame-UCLL) unless the repo has its own
`CONTRIBUTING.md`.

## The short version

1. Branch from `main`, keep the branch short-lived.
2. Make your change, with tests.
3. Run `npx changeset` and commit the file it writes.
4. Open a pull request into `main`. CI must be green and one person must approve.

## Branches: trunk-based

- `main` is the only long-lived branch. There is no `development` branch.
- Work on a short feature branch (`fix/router-deny-reason`, `feat/rf-add`) and merge
  it back within days, not weeks.
- Releases are tags made by CI (`re-frame@5.1.0`), not branches.
- The organisation ruleset protects `main` in every repo: a pull request is required,
  with one approving review and the required check `check / result` green. Force
  pushes and deleting `main` are blocked. Where a repo has a `CODEOWNERS` file, the
  code owners must approve changes to their paths.

## Changesets: one per pull request

Every pull request that changes a package adds a changeset: a short note that says
which package changes, what kind of change it is, and one line for the changelog.

```bash
npx changeset
```

Pick the packages, pick the kind of change and write the line. Commit the new file
in `.changeset/` together with your change.

| Kind  | When                                         |
| ----- | -------------------------------------------- |
| patch | A fix                                        |
| minor | Something new that breaks nothing            |
| major | An app must change code to keep working      |

- Nothing to release (docs, CI, tests only)? Run `npx changeset --empty`.
- Majors move in lockstep: every RE-Frame package has the same major as core
  (re-frame 5 goes with every lib at 5.x). Do not bump a lib's major on its own;
  talk to the core maintainers first.
- The `check` workflow fails when a changed package has no changeset.
- Only core maintainers merge the "Version packages" pull request that CI opens;
  merging it publishes the packages.

## Run the tools through npm

Run the RE-Frame command-line tools through the project's npm scripts:

```bash
npm run rf -- lint
npm run rb -- doctor
```

Never use `npx rf`: the name `rf` on npmjs belongs to someone else's package, so `npx`
could download and run a stranger's code.

## Component glyphs

The glyph at the end of a custom element's tag tells you where it comes from.

- Core components end in `-Ƒ` (U+0191): `header-Ƒ`, `if-Ƒ`.
- Every package that ships components has one reserved glyph of its own, registered
  in core's glyph list. The linter knows them all. Never use another package's glyph.
- In app code each developer uses their own glyph (`home-χ`, `card-ɮ`). Pick one
  that is not reserved by a package or used by a teammate.
- A new package that ships components picks a glyph and registers it in core before
  its first release.

## Code

- Plain JavaScript ES modules with JSDoc comments. No TypeScript and no build step.
- Types (`.d.ts`) are generated from the JSDoc when a package is packed. They are never
  committed (`types/` is in `.gitignore`). The check job generates them on every pull
  request and fails on JSDoc errors, so keep your JSDoc correct.
- Node 26 (see `.nvmrc`). Install with `npm ci`.
- Write tests for what you change. A package without tests makes `npm update` risky
  for every app that uses it.

## Licence header

Every `.js`, `.mjs` and `.cjs` file starts with these two lines (after a `#!` line,
if there is one):

```js
// Copyright 2026 UCLL
// SPDX-License-Identifier: Apache-2.0
```

Use the year the file was created. The `check` workflow fails when a file misses the
header.

## Licence of your contribution

RE-Frame is licensed under the [Apache License 2.0](LICENSE); see also [NOTICE](NOTICE).
Under section 5 of that licence, anything you submit in a pull request is under the same
terms, unless you say otherwise. You keep the copyright of your own work; there is no
extra agreement to sign.

## Installing internal packages

While the packages are internal they live on GitHub Packages. Add this line to the
project's `.npmrc`:

```ini
@re-frame-ucll:registry=https://npm.pkg.github.com
```

and log in once with a GitHub token that has `read:packages`:

```bash
npm login --scope=@re-frame-ucll --registry=https://npm.pkg.github.com
```

## Issues and security

- Bugs and ideas: open an issue with one of the templates.
- Security problems: do not open an issue. See [SECURITY.md](SECURITY.md).
