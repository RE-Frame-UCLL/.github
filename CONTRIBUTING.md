# Contributing to RE-Frame

Thank you for helping. These rules hold for every repo in
[RE-Frame-UCLL](https://github.com/RE-Frame-UCLL) unless the repo has its own
`CONTRIBUTING.md`.

> [!IMPORTANT]
> **Every pull request needs a changeset, and you write it yourself** with
> `npx changeset`. CI does not write it for you; it only checks that it is there and
> fails your pull request when it is missing. CI does the rest of the release.
> See [Changesets: you write them, CI releases them](#changesets-you-write-them-ci-releases-them).

## The short version

1. Branch from `main`, keep the branch short-lived.
2. Make your change, with tests.
3. **Run `npx changeset` yourself** and commit the file it writes in `.changeset/`.
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

## Changesets: you write them, CI releases them

A changeset is a small file in `.changeset/` that says which package changes, what
kind of change it is, and one line for the changelog. A release has three changeset
steps. **Only the first one is yours**; CI does the other two.

| Step | Who | When |
| ---- | --- | ---- |
| `npx changeset` | **You** | Once per pull request, on your branch, before you push |
| `changeset version` | CI (the `release` workflow) | After your pull request is merged into `main`: raises the versions, writes `CHANGELOG.md` and opens the "Version packages" pull request |
| `changeset publish` | CI | After a core maintainer merges "Version packages": publishes the packages to GitHub Packages and tags the release |

### Your step: `npx changeset`

CI cannot write your changeset, because only you know what your change means: a fix
or something new, whether apps must change their code, and what users should read
in the changelog.

On your branch, after `npm ci`:

```bash
npx changeset
```

It asks three things:

1. Which packages changed.
2. The kind of change for each package:

   | Kind  | When                                    |
   | ----- | --------------------------------------- |
   | patch | A fix                                   |
   | minor | Something new that breaks nothing       |
   | major | An app must change code to keep working |

3. One line for the changelog, written for the people who use the package.

Commit the new file in `.changeset/` together with your change. Forgot it? Add it to
the same pull request; the check turns green again.

Handy variants:

- `npx changeset --empty` for a pull request that releases nothing (docs, CI or
  tests only).
- `npx changeset add --patch @re-frame-ucll/re-brain` (or `--minor`, `--major`) gives
  the kind on the command line, so it only asks for the changelog line.

Run it after `npm ci`, so `npx` uses the repo's own Changesets (`@changesets/cli` is a
dev dependency). If `npx` asks "Need to install the following packages: changeset",
answer **no**: that is an unrelated package of someone else.

### What CI does with it

- **On your pull request:** the `check` workflow fails (`check / changeset`) when a
  package changed and there is no changeset.
- **After the merge:** the `release` workflow collects every changeset on `main` into
  one "Version packages" pull request. More merges before the release update that
  same pull request, so one release can hold many changes.
- **At release time:** a core maintainer reviews and merges "Version packages"; that
  merge publishes the packages, tags them and creates a GitHub Release.
- You never run `changeset version` or `changeset publish` yourself. Publishing by
  hand is only for emergencies, by a core maintainer.

### Majors move in lockstep

Every RE-Frame package has the same major as core (re-frame 5 goes with every lib at
5.x). Do not make a `major` changeset for a lib on its own; talk to the core
maintainers first.

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
