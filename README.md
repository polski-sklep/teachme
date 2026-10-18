# Skills For Real Engineers

[![skills.sh](https://skills.sh/b/mattpocock/skills)](https://skills.sh/mattpocock/skills)

These are the agent skills I use for day-to-day engineering. A skill is a directory with a `SKILL.md`: Markdown instructions and YAML frontmatter. Claude Code loads them as slash commands. User-invoked skills run when you type them. Model-invoked skills can also be reached by the agent when the task fits.

The npm package is `mattpocock-skills` at version 1.0.1. `package.json` records the repository as [mattpocock/skills](https://github.com/mattpocock/skills). The licence is MIT. See [LICENSE](./LICENSE). Copyright (c) 2026 Matt Pocock.

Updates also go out on the [newsletter](https://www.aihero.dev/s/skills-newsletter).

## Install

From a checkout of this repo, symlink the skills into the local Claude CLI:

```bash
./scripts/link-skills.sh
```

That links every skill directory under `skills/` that contains a `SKILL.md` into `~/.claude/skills`. It includes `personal/` and `in-progress/`. It skips `skills/deprecated/`. It stops if `~/.claude/skills` is already a symlink into this repo.

The skills.sh installer installs the repository named in `package.json`:

```bash
npx skills@latest add mattpocock/skills
```

Pick the skills you want and which coding agents to install them on. Include `/setup-matt-pocock-skills`.

## Set up a consumer repo

Run `/setup-matt-pocock-skills` once in the repo you are working on, before the other engineering skills. It is prompt-driven. It reads the repo, then walks three decisions one at a time.

- **Issue tracker.** GitHub (the `gh` CLI) when the remote is GitHub, GitLab (the `glab` CLI) when the remote is GitLab, local Markdown files under `.scratch/<feature>/`, or another tracker you describe in a paragraph. The skill names Jira and Linear as examples of that last case. For GitHub or GitLab it also asks whether external pull requests are a request surface for `/triage`. The default is no.
- **Triage labels.** Five roles: `needs-triage`, `needs-info`, `ready-for-agent`, `ready-for-human`, and `wontfix`. Each role maps to the label string your tracker already uses. The default string is the role name.
- **Domain docs.** Single-context (one `CONTEXT.md` and `docs/adr/` at the repo root) or multi-context (`CONTEXT-MAP.md` at the root, pointing at a `CONTEXT.md` per context).

It writes an `## Agent skills` section into the existing `CLAUDE.md`, or into `AGENTS.md` when that is the file the repo already has. If neither file exists, it asks which one to create. It also writes:

- `docs/agents/issue-tracker.md`
- `docs/agents/triage-labels.md`
- `docs/agents/domain.md`

Edit those files later if the mapping changes. Run the setup skill again when you want to switch tracker or start the config over.

`/to-prd`, `/to-issues`, and `/triage` read that config and tell you to run setup when it is missing. `/diagnosing-bugs`, `/tdd`, and `/improve-codebase-architecture` read `CONTEXT.md` and nearby ADRs when those files exist, and they still run without them. [docs/adr/0001](./docs/adr/0001-explicit-setup-pointer-only-for-hard-dependencies.md) records that split. It still uses the old skill name `diagnose` for what is now `diagnosing-bugs`.

## How a piece of work moves

[`/ask-matt`](./skills/engineering/ask-matt/SKILL.md) is the router. The path it describes:

1. **Sharpen the idea.** [`/grill-with-docs`](./skills/engineering/grill-with-docs/SKILL.md) when you have a codebase. That skill runs [`/grilling`](./skills/productivity/grilling/SKILL.md) and [`/domain-modeling`](./skills/engineering/domain-modeling/SKILL.md). [`/grill-me`](./skills/productivity/grill-me/SKILL.md) when you do not have a codebase.
2. **Prototype when conversation is not enough.** [`/handoff`](./skills/productivity/handoff/SKILL.md) out to a fresh session, run [`/prototype`](./skills/engineering/prototype/SKILL.md), then hand the answer back. A prototype is throwaway: a terminal app for state and business logic, or several UI variations on one route, switched with a URL search param.
3. **Build.** A single sitting goes straight to [`/implement`](./skills/engineering/implement/SKILL.md). Work that spans sessions goes through [`/to-prd`](./skills/engineering/to-prd/SKILL.md), then [`/to-issues`](./skills/engineering/to-issues/SKILL.md) (tracer-bullet vertical slices), then a fresh session per issue that starts `/implement` with the PRD and that one issue.

[`/triage`](./skills/engineering/triage/SKILL.md) is the on-ramp for issues you did not write yourself. When setup recorded external pull requests as a request surface, triage runs those PRs through the same roles. Issues that `/to-issues` already published are agent-ready and skip triage.

[`/improve-codebase-architecture`](./skills/engineering/improve-codebase-architecture/SKILL.md) sits beside that flow. Picking a candidate gives you an idea to take back into `/grill-with-docs`.

[`/handoff`](./skills/productivity/handoff/SKILL.md) writes a Markdown file in the operating system's temporary directory, with secrets redacted, so another session can continue. [`/teach`](./skills/productivity/teach/SKILL.md) and [`/writing-great-skills`](./skills/productivity/writing-great-skills/SKILL.md) sit off this flow.

## Reference

**User-invoked** skills set `disable-model-invocation: true` and are reachable only when you type them. **Model-invoked** skills omit that flag. A user-invoked skill may invoke model-invoked skills, and it never invokes another user-invoked skill. The full rule is in [docs/invocation.md](./docs/invocation.md).

The Claude Code plugin manifest at [`.claude-plugin/plugin.json`](./.claude-plugin/plugin.json) lists the engineering and productivity skills below except `implement` and `resolving-merge-conflicts`. The `misc/` skills are in this catalogue and are not in the manifest. `skills/personal/`, `skills/in-progress/`, and `skills/deprecated/` stay out of the manifest and out of this catalogue.

`skills/engineering/README.md` does not yet list `implement` or `resolving-merge-conflicts`. They are in the tree and in this list.

### Engineering

Skills I use daily for code work.

**User-invoked**

- **[ask-matt](./skills/engineering/ask-matt/SKILL.md)**: Ask which skill or flow fits your situation. A router over the user-invoked skills in this repo.
- **[grill-with-docs](./skills/engineering/grill-with-docs/SKILL.md)**: A grilling session that also builds the project's domain model, updating `CONTEXT.md` and ADRs as terms and decisions get resolved.
- **[triage](./skills/engineering/triage/SKILL.md)**: Move issues, and external PRs when setup says so, through triage roles. Categories are `bug` and `enhancement`. States are `needs-triage`, `needs-info`, `ready-for-agent`, `ready-for-human`, and `wontfix`.
- **[improve-codebase-architecture](./skills/engineering/improve-codebase-architecture/SKILL.md)**: Scan a codebase for deepening opportunities, write a self-contained HTML report in the OS temp directory (Tailwind and Mermaid loaded from CDNs), then grill through whichever candidate you pick.
- **[setup-matt-pocock-skills](./skills/engineering/setup-matt-pocock-skills/SKILL.md)**: Configure a repo for the engineering skills (issue tracker, triage labels, domain doc layout). Run once per repo before the other engineering skills.
- **[to-issues](./skills/engineering/to-issues/SKILL.md)**: Break a plan, spec, or PRD into independently-grabbable issues using tracer-bullet vertical slices. The breakdown is checked with you, then published with the tracker's agent-ready triage label.
- **[to-prd](./skills/engineering/to-prd/SKILL.md)**: Turn the current conversation into a PRD, check the test seams with you, and publish it to the issue tracker with the `ready-for-agent` label. It synthesises what you have already discussed.
- **[prototype](./skills/engineering/prototype/SKILL.md)**: Build a throwaway prototype: a runnable terminal app for state and business-logic questions, or several UI variations toggleable from one route.
- **[implement](./skills/engineering/implement/SKILL.md)**: Implement a PRD or a set of issues, using `/tdd` at the pre-agreed seams. It typechecks and runs tests along the way, reviews the result, and commits on the current branch.

**Model-invoked**

- **[diagnosing-bugs](./skills/engineering/diagnosing-bugs/SKILL.md)**: Diagnosis loop for hard bugs and performance regressions: build a feedback loop, reproduce and minimise, hypothesise, instrument, fix and regression-test, then clean up.
- **[tdd](./skills/engineering/tdd/SKILL.md)**: Test-driven development. One failing test, then the code that makes it pass, then refactor, one vertical slice at a time. Tests exercise behaviour through the public interface. `/codebase-design` supplies the vocabulary for that interface.
- **[domain-modeling](./skills/engineering/domain-modeling/SKILL.md)**: Build and sharpen a project's domain model. Challenge terms against the glossary, stress-test them with edge cases, and update `CONTEXT.md` and ADRs inline.
- **[codebase-design](./skills/engineering/codebase-design/SKILL.md)**: Shared vocabulary for deep modules: a lot of behaviour behind a small interface, placed at a clean seam, testable through that interface.
- **[resolving-merge-conflicts](./skills/engineering/resolving-merge-conflicts/SKILL.md)**: Resolve an in-progress git merge or rebase. Read both sides, keep both intents where they can coexist, run the project's checks, and finish the merge or rebase.

### Productivity

General workflow tools, not code-specific.

**User-invoked**

- **[grill-me](./skills/productivity/grill-me/SKILL.md)**: Relentless interview about a plan or design until every branch of the decision tree is resolved. Saves nothing locally.
- **[handoff](./skills/productivity/handoff/SKILL.md)**: Compact the current conversation into a handoff document so another agent can continue the work. The file is written to the OS temporary directory.
- **[teach](./skills/productivity/teach/SKILL.md)**: Teach a skill or concept over multiple sessions. The current directory is the teaching workspace: `MISSION.md`, `RESOURCES.md`, HTML lessons, and reusable pieces under `assets/`.
- **[writing-great-skills](./skills/productivity/writing-great-skills/SKILL.md)**: Reference for writing and editing skills: the vocabulary and principles that make a skill predictable.

**Model-invoked**

- **[grilling](./skills/productivity/grilling/SKILL.md)**: Interview the user relentlessly about a plan or design, one question at a time, until every branch of the decision tree is resolved. The loop behind `grill-me` and `grill-with-docs`.

### Misc

Tools I keep around but rarely use. All four are model-invoked.

**User-invoked**

None in this bucket.

**Model-invoked**

- **[git-guardrails-claude-code](./skills/misc/git-guardrails-claude-code/SKILL.md)**: Set up a Claude Code `PreToolUse` hook that blocks `git push`, `git reset --hard`, `git clean -f` / `-fd`, `git branch -D`, `git checkout .`, and `git restore .` before they run. Installs for one project (`.claude/settings.json`) or for all projects (`~/.claude/settings.json`).
- **[migrate-to-shoehorn](./skills/misc/migrate-to-shoehorn/SKILL.md)**: Migrate test files from `as` type assertions to `@total-typescript/shoehorn`. Test code only.
- **[scaffold-exercises](./skills/misc/scaffold-exercises/SKILL.md)**: Create an `exercises/` tree of sections, problems, solutions, and explainers that passes `pnpm ai-hero-cli internal lint`, then commit it.
- **[setup-pre-commit](./skills/misc/setup-pre-commit/SKILL.md)**: Set up Husky pre-commit hooks in the current repo, with lint-staged running Prettier, plus type checking and tests.

## Repository layout

```
skills/
  engineering/     daily code work
  productivity/    daily non-code workflow tools
  misc/            kept around, rarely used
  personal/        tied to my own setup (not listed above)
  in-progress/     drafts (not listed above)
  deprecated/      no longer used (not listed above)
docs/
  invocation.md    user-invoked vs model-invoked
  adr/             decisions for this repository
scripts/
  link-skills.sh   symlink skills into ~/.claude/skills
  list-skills.sh   print every SKILL.md path
.claude-plugin/
  plugin.json      Claude Code plugin manifest
.github/workflows/
  release.yml      version and tag on pushes to main
.changeset/        Changesets config and pending version notes
.out-of-scope/     requests this repo has declined
```

Each bucket has its own `README.md`. `CONTEXT.md` at this repo's root is the glossary for the skills themselves (issue tracker, issue, triage role). Consumer repos get their own `CONTEXT.md` from `/domain-modeling`.

## Tech stack

- **Skills:** Markdown plus YAML frontmatter. This repository has no application server and no UI.
- **Local scripts:** Bash (`scripts/link-skills.sh`, `scripts/list-skills.sh`).
- **Release tooling:** [Changesets](https://github.com/changesets/changesets) on Node.js. `package.json` sets `packageManager` to npm 10.9.4. The dev dependencies are `@changesets/cli` and `@changesets/changelog-github`.
- **CI:** GitHub Actions (`.github/workflows/release.yml`) on `ubuntu-latest`, using Node.js 22.

`git-guardrails-claude-code` installs a Claude Code hook. The other skills are instructions for the agent.

## Prerequisites

To use the skills from a checkout:

- Claude Code, or another agent that reads `~/.claude/skills`.
- Bash, for `scripts/link-skills.sh`.

To install via skills.sh:

- Node.js with `npx`.
- Git.

In a consumer repo, GitHub issue tracking uses the `gh` CLI. GitLab uses the `glab` CLI. Local Markdown issues need neither.

To work on versioning in this repository:

- Node.js 22, the version the release workflow installs.
- npm. `package.json` declares `packageManager` npm 10.9.4.

## Run it locally

Invoke a skill with its slash command, for example `/grill-me` or `/tdd`, after it is installed.

From this checkout:

```bash
npm ci
./scripts/list-skills.sh
./scripts/link-skills.sh
```

`list-skills.sh` prints every `SKILL.md` path, including deprecated skills.

`npm run changeset` writes a version note. `npm run version` applies pending changesets and updates `CHANGELOG.md`. The release workflow runs that version command on GitHub.

## Configuration

This repository has no `.env` file and no `.env.example`. The skills do not read environment variables.

Per-repo configuration for the engineering skills is the Markdown written by `/setup-matt-pocock-skills`: `docs/agents/issue-tracker.md`, `docs/agents/triage-labels.md`, `docs/agents/domain.md`, and an `## Agent skills` section in `CLAUDE.md` or `AGENTS.md`.

The release workflow passes one name into the Changesets step:

- `GITHUB_TOKEN`, set from `secrets.GITHUB_TOKEN` in [`.github/workflows/release.yml`](./.github/workflows/release.yml).

## Tests

This repository has no test suite and no `test` script. The release workflow's install step is `npm ci`.

## Versioning and releases

Pushes to `main` run `.github/workflows/release.yml`. The job checks out the repo, installs Node.js 22, runs `npm ci`, and calls `changesets/action@v1` with:

- `version`: `npx changeset version`
- `publish`: `npx changeset tag`
- `commit` and `title`: `chore: version skills`

When `.changeset/` has a pending changeset, that action opens a pull request with that title. Merging it runs `npx changeset tag`. Changesets config sets `privatePackages.version` and `privatePackages.tag` to true, so the private package still gets a version and a git tag. The workflow leaves `createGithubReleases` unset. On `changesets/action@v1` that input defaults to true, so a successful publish also creates a GitHub release. `package.json` sets `"private": true`. Changesets `access` is `restricted`, `baseBranch` is `main`, and the changelog generator is `@changesets/changelog-github` for `mattpocock/skills`.
