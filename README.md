<p>
  <a href="https://www.aihero.dev/s/skills-newsletter">
    <picture>
      <source media="(prefers-color-scheme: dark)" srcset="https://res.cloudinary.com/total-typescript/image/upload/v1777382277/skills-repo-dark_2x.png">
      <source media="(prefers-color-scheme: light)" srcset="https://res.cloudinary.com/total-typescript/image/upload/v1777382277/skill-repo-light_2x.png">
      <img alt="Skills" src="https://res.cloudinary.com/total-typescript/image/upload/v1777382277/skill-repo-light_2x.png" width="369">
    </picture>
  </a>
</p>

# Skills For Real Engineers

[![skills.sh](https://skills.sh/b/mattpocock/skills)](https://skills.sh/mattpocock/skills)

My agent skills that I use every day to do real engineering - not vibe coding.

Developing real applications is hard. Approaches like GSD, BMAD, and Spec-Kit try to help by owning the process. But while doing so, they take away your control and make bugs in the process hard to resolve.

These skills are small, easy to adapt, and composable. They are Markdown instructions (`SKILL.md`) with YAML frontmatter, loaded by a coding agent as slash commands and, where allowed, as automatic behaviour. They work with any model. They are based on decades of engineering experience. Hack around with them. Make them your own. Enjoy.

If you want to keep up with changes to these skills, and any new ones I create, you can join ~60,000 other devs on my newsletter:

[Sign Up To The Newsletter](https://www.aihero.dev/s/skills-newsletter)

The current package version is **1.0.1**. The licence is MIT. See [LICENSE](./LICENSE).

## Install

1. Run the skills.sh installer:

```bash
npx skills@latest add mattpocock/skills
```

2. Pick the skills you want, and which coding agents you want to install them on. **Make sure you select `/setup-matt-pocock-skills`**.

3. Run `/setup-matt-pocock-skills` once in the repo you are working on. It is prompt-driven: it reads the repo, then walks three decisions one at a time.

   - **Issue tracker.** GitHub (the `gh` CLI) when the remote is GitHub, GitLab (the `glab` CLI) when the remote is GitLab, local Markdown files under `.scratch/<feature>/`, or another tracker you describe in a paragraph (the skill names Jira and Linear as examples). For GitHub or GitLab it also asks whether external pull requests are a request surface for `/triage`. The default is no.
   - **Triage labels.** Five roles: `needs-triage`, `needs-info`, `ready-for-agent`, `ready-for-human`, and `wontfix`. Each role maps to the label string your tracker already uses. The default string is the role name.
   - **Domain docs.** Single-context (one `CONTEXT.md` and `docs/adr/` at the repo root) or multi-context (`CONTEXT-MAP.md` at the root, pointing at a `CONTEXT.md` per context).

   It then writes an `## Agent skills` section into the existing `CLAUDE.md`, or into `AGENTS.md` if that is the file the repo already has. If neither file exists, it asks which one to create. It also writes:

   - `docs/agents/issue-tracker.md`
   - `docs/agents/triage-labels.md`
   - `docs/agents/domain.md`

4. Bam, you're ready to go. Edit `docs/agents/*.md` later if the mapping changes. Run the setup skill again only when you want to switch tracker or start the config over.

`/to-prd`, `/to-issues`, and `/triage` read that config and tell you to run setup when it is missing. `/diagnosing-bugs`, `/tdd`, and `/improve-codebase-architecture` read `CONTEXT.md` and nearby ADRs when those files exist, and they still run without them.

## Why These Skills Exist

I built these skills as a way to fix common failure modes I see with Claude Code, Codex, and other coding agents.

### #1: The Agent Didn't Do What I Want

> "No-one knows exactly what they want"
>
> David Thomas & Andrew Hunt, [The Pragmatic Programmer](https://www.amazon.co.uk/Pragmatic-Programmer-Anniversary-Journey-Mastery/dp/B0833F1T3V)

**The Problem**. The most common failure mode in software development is misalignment. You think the dev knows what you want. Then you see what they've built, and you realise it didn't understand you at all.

This is just the same in the AI age. There is a communication gap between you and the agent. The fix for this is a **grilling session**: getting the agent to ask you detailed questions about what you're building.

**The Fix** is to use:

- [`/grill-me`](./skills/productivity/grill-me/SKILL.md) for non-code uses
- [`/grill-with-docs`](./skills/engineering/grill-with-docs/SKILL.md), the same interview as [`/grill-me`](./skills/productivity/grill-me/SKILL.md), plus the domain-model work below

These are my most popular skills. They help you align with the agent before you get started, and think deeply about the change you're making. Use them _every_ time you want to make a change.

### #2: The Agent Is Way Too Verbose

> With a ubiquitous language, conversations among developers and expressions of the code are all derived from the same domain model.
>
> Eric Evans, [Domain-Driven-Design](https://www.amazon.co.uk/Domain-Driven-Design-Tackling-Complexity-Software/dp/0321125215)

**The Problem**: At the start of a project, devs and the people they're building the software for (the domain experts) are usually speaking different languages.

I felt the same tension with my agents. Agents are usually dropped into a project and asked to figure out the jargon as they go. So they use 20 words where 1 will do.

**The Fix** for this is a shared language. It's a document that helps agents decode the jargon used in the project.

<details>
<summary>
Example
</summary>

Here's an example [`CONTEXT.md`](https://github.com/mattpocock/course-video-manager/blob/076a5a7a182db0fe1e62971dd7a68bcadf010f1c/CONTEXT.md), from my `course-video-manager` repo. Which one is easier to read?

- **BEFORE**: "There's a problem when a lesson inside a section of a course is made 'real' (i.e. given a spot in the file system)"
- **AFTER**: "There's a problem with the materialization cascade"

This concision pays off session after session.

</details>

This is built into [`/grill-with-docs`](./skills/engineering/grill-with-docs/SKILL.md). That skill runs [`/grilling`](./skills/productivity/grilling/SKILL.md) and [`/domain-modeling`](./skills/engineering/domain-modeling/SKILL.md). The glossary lands in `CONTEXT.md`. Hard-to-explain decisions land in ADRs under `docs/adr/`. Files are created when the first term or decision is actually resolved.

It's hard to explain how powerful this is. It might be the single coolest technique in this repo. Try it, and see.

> [!TIP]
> A shared language has many other benefits than reducing verbosity:
>
> - **Variables, functions and files are named consistently**, using the shared language
> - As a result, the **codebase is easier to navigate** for the agent
> - The agent also **spends fewer tokens on thinking**, because it has access to a more concise language

### #3: The Code Doesn't Work

> "Always take small, deliberate steps. The rate of feedback is your speed limit. Never take on a task that's too big."
>
> David Thomas & Andrew Hunt, [The Pragmatic Programmer](https://www.amazon.co.uk/Pragmatic-Programmer-Anniversary-Journey-Mastery/dp/B0833F1T3V)

**The Problem**: Let's say that you and the agent are aligned on what to build. What happens when the agent _still_ produces crap?

It's time to look at your feedback loops. Without feedback on how the code it produces actually runs, the agent will be flying blind.

**The Fix**: You need the usual tranche of feedback loops: static types, browser access, and automated tests.

For automated tests, a red-green-refactor loop is critical. The agent writes one failing test, makes that test pass, then refactors, one vertical slice at a time. Writing every test before any implementation is the anti-pattern the skill refuses.

I've built a **[`/tdd`](./skills/engineering/tdd/SKILL.md) skill** you can slot into any project. It encourages that loop and gives the agent plenty of guidance on what makes good and bad tests. Good tests exercise behaviour through the public interface. [`/codebase-design`](./skills/engineering/codebase-design/SKILL.md) supplies the vocabulary for that interface.

For debugging, I've also built a **[`/diagnosing-bugs`](./skills/engineering/diagnosing-bugs/SKILL.md)** skill. It starts by building a tight feedback loop that can go red on the bug. After that it reproduces and minimises, ranks hypotheses, instruments one variable at a time, then fixes and adds a regression test where a real seam exists.

### #4: We Built A Ball Of Mud

> "Invest in the design of the system _every day_."
>
> Kent Beck, [Extreme Programming Explained](https://www.amazon.co.uk/Extreme-Programming-Explained-Embrace-Change/dp/0321278658)

> "The best modules are deep. They allow a lot of functionality to be accessed through a simple interface."
>
> John Ousterhout, [A Philosophy Of Software Design](https://www.amazon.co.uk/Philosophy-Software-Design-2nd/dp/173210221X)

**The Problem**: Most apps built with agents are complex and hard to change. Because agents can radically speed up coding, they also accelerate software entropy. Codebases get more complex at an unprecedented rate.

**The Fix** for this is caring about the design of the code, on purpose, as part of the work.

- [`/to-prd`](./skills/engineering/to-prd/SKILL.md) turns the current conversation into a PRD and publishes it to the issue tracker with the `ready-for-agent` label. It records the modules and interfaces the work will touch, sketches the highest sensible test seams, and checks those seams with you. It synthesises what you have already discussed.
- [`/codebase-design`](./skills/engineering/codebase-design/SKILL.md) is the shared discipline: a lot of behaviour behind a small interface, placed at a clean seam, testable through that interface.
- [`/improve-codebase-architecture`](./skills/engineering/improve-codebase-architecture/SKILL.md) scans a codebase for deepening opportunities, writes a self-contained HTML report (Tailwind and Mermaid from CDNs), and grills through whichever candidate you pick. I recommend running it on your codebase once every few days.

### Summary

Software engineering fundamentals matter more than ever. These skills are my best effort at condensing these fundamentals into repeatable practices, to help you ship the best apps of your career. Enjoy.

## How a piece of work moves

[`/ask-matt`](./skills/engineering/ask-matt/SKILL.md) is the router. The path it describes:

1. **Sharpen the idea.** [`/grill-with-docs`](./skills/engineering/grill-with-docs/SKILL.md) when you have a codebase. [`/grill-me`](./skills/productivity/grill-me/SKILL.md) when you do not.
2. **Prototype when conversation is not enough.** [`/handoff`](./skills/productivity/handoff/SKILL.md) out to a fresh session, run [`/prototype`](./skills/engineering/prototype/SKILL.md), then hand the answer back. A prototype is throwaway: a terminal app for state and business logic, or several UI variations on one route.
3. **Build.** A single sitting goes straight to [`/implement`](./skills/engineering/implement/SKILL.md). Work that spans sessions goes through [`/to-prd`](./skills/engineering/to-prd/SKILL.md), then [`/to-issues`](./skills/engineering/to-issues/SKILL.md) (tracer-bullet vertical slices), then a fresh session per issue that starts `/implement` with the PRD and that one issue.

[`/triage`](./skills/engineering/triage/SKILL.md) is the on-ramp for issues you did not write yourself (bug reports and incoming requests). When setup recorded external pull requests as a request surface, triage runs those PRs through the same roles. Issues that `/to-issues` already created are agent-ready and skip triage.

[`/improve-codebase-architecture`](./skills/engineering/improve-codebase-architecture/SKILL.md) sits beside that flow. Picking a candidate gives you an idea to take back into `/grill-with-docs`.

[`/handoff`](./skills/productivity/handoff/SKILL.md) compacts a conversation into a Markdown file in the operating system's temporary directory, with secrets redacted, so another session can continue. [`/teach`](./skills/productivity/teach/SKILL.md) and [`/writing-great-skills`](./skills/productivity/writing-great-skills/SKILL.md) sit off this flow.

## Reference

These split on one axis: who can invoke them. **User-invoked** skills are reachable only when you type them (for example `/grill-me`). Their job is to orchestrate. **Model-invoked** skills can be invoked by you or reached for automatically by the agent when the task fits. They hold the reusable discipline. A user-invoked skill may invoke model-invoked skills, but never another user-invoked one. The full rule is in [docs/invocation.md](./docs/invocation.md).

The Claude Code plugin manifest at [`.claude-plugin/plugin.json`](./.claude-plugin/plugin.json) lists the engineering and productivity skills below, omitting `implement` and `resolving-merge-conflicts`. The `misc/` skills are part of this repository and of this catalogue. They are not in the manifest. `skills/personal/`, `skills/in-progress/`, and `skills/deprecated/` are kept out of the manifest and out of this catalogue.

### Engineering

Skills I use daily for code work.

**User-invoked**

- **[ask-matt](./skills/engineering/ask-matt/SKILL.md)**: Ask which skill or flow fits your situation. A router over the user-invoked skills in this repo.
- **[grill-with-docs](./skills/engineering/grill-with-docs/SKILL.md)**: Grilling session that also builds your project's domain model, sharpening terminology and updating `CONTEXT.md` and ADRs inline.
- **[triage](./skills/engineering/triage/SKILL.md)**: Move issues, and external PRs when setup says so, through triage roles. Categories are `bug` and `enhancement`. States are `needs-triage`, `needs-info`, `ready-for-agent`, `ready-for-human`, and `wontfix`.
- **[improve-codebase-architecture](./skills/engineering/improve-codebase-architecture/SKILL.md)**: Scan a codebase for deepening opportunities, present them as a visual HTML report, then grill through whichever one you pick.
- **[setup-matt-pocock-skills](./skills/engineering/setup-matt-pocock-skills/SKILL.md)**: Configure this repo for the engineering skills (issue tracker, triage labels, domain doc layout). Run once per repo before using the other engineering skills.
- **[to-issues](./skills/engineering/to-issues/SKILL.md)**: Break any plan, spec, or PRD into independently-grabbable issues using tracer-bullet vertical slices. The breakdown is checked with you, then published with the ready-for-agent label.
- **[to-prd](./skills/engineering/to-prd/SKILL.md)**: Turn the current conversation into a PRD, check the test seams with you, and publish it to the issue tracker with `ready-for-agent`.
- **[prototype](./skills/engineering/prototype/SKILL.md)**: Build a throwaway prototype: a runnable terminal app for state and business-logic questions, or several UI variations toggleable from one route.
- **[implement](./skills/engineering/implement/SKILL.md)**: Implement a PRD or a set of issues, using `/tdd` at the pre-agreed seams. It typechecks and runs tests along the way, reviews the result, and commits on the current branch.

**Model-invoked**

- **[diagnosing-bugs](./skills/engineering/diagnosing-bugs/SKILL.md)**: Disciplined diagnosis loop for hard bugs and performance regressions: feedback loop, reproduce and minimise, hypothesise, instrument, fix, regression-test.
- **[tdd](./skills/engineering/tdd/SKILL.md)**: Test-driven development with a red-green-refactor loop. Builds features or fixes bugs one vertical slice at a time.
- **[domain-modeling](./skills/engineering/domain-modeling/SKILL.md)**: Actively build and sharpen a project's domain model. Challenge terms against the glossary, stress-test with edge-case scenarios, and update `CONTEXT.md` and ADRs inline.
- **[codebase-design](./skills/engineering/codebase-design/SKILL.md)**: Shared discipline and vocabulary for designing deep modules: a lot of behaviour behind a small interface, placed at a clean seam, testable through that interface.
- **[resolving-merge-conflicts](./skills/engineering/resolving-merge-conflicts/SKILL.md)**: Resolve an in-progress git merge or rebase. Read both sides, preserve both intents where they can coexist, run the project's checks, and finish the merge or rebase.

### Productivity

General workflow tools, not code-specific.

**User-invoked**

- **[grill-me](./skills/productivity/grill-me/SKILL.md)**: Get relentlessly interviewed about a plan or design until every branch of the decision tree is resolved.
- **[handoff](./skills/productivity/handoff/SKILL.md)**: Compact the current conversation into a handoff document so another agent can continue the work. The file is written to the OS temporary directory.
- **[teach](./skills/productivity/teach/SKILL.md)**: Teach the user a new skill or concept over multiple sessions, using the current directory as a stateful teaching workspace. Lessons are HTML files, and reusable pieces live in `assets/`.
- **[writing-great-skills](./skills/productivity/writing-great-skills/SKILL.md)**: Reference for writing and editing skills well: the vocabulary and principles that make a skill predictable.

**Model-invoked**

- **[grilling](./skills/productivity/grilling/SKILL.md)**: Interview the user relentlessly about a plan or design until every branch of the decision tree is resolved. The reusable loop behind `grill-me` and `grill-with-docs`.

### Misc

Tools I keep around but rarely use. All four are model-invoked.

**User-invoked**

None in this bucket.

**Model-invoked**

- **[git-guardrails-claude-code](./skills/misc/git-guardrails-claude-code/SKILL.md)**: Set up a Claude Code PreToolUse hook that blocks `git push`, `git reset --hard`, `git clean -f` / `-fd`, `git branch -D`, `git checkout .`, and `git restore .` before they run. Installs for one project (`.claude/settings.json`) or for all projects (`~/.claude/settings.json`).
- **[migrate-to-shoehorn](./skills/misc/migrate-to-shoehorn/SKILL.md)**: Migrate test files from `as` type assertions to `@total-typescript/shoehorn`. Test code only.
- **[scaffold-exercises](./skills/misc/scaffold-exercises/SKILL.md)**: Create an `exercises/` tree of sections, problems, solutions, and explainers that passes `pnpm ai-hero-cli internal lint`, then commit it.
- **[setup-pre-commit](./skills/misc/setup-pre-commit/SKILL.md)**: Set up Husky pre-commit hooks in the current repo, with lint-staged running Prettier, plus type checking and tests.

## Repository layout

```
skills/
  engineering/     daily code work
  productivity/    daily non-code workflow tools
  misc/            kept around, rarely used
  personal/        tied to the author's own setup (not listed above)
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
.out-of-scope/     rejected requests against this repository
```

Each bucket has its own `README.md`. A skill is a directory with a `SKILL.md`. User-invoked skills set `disable-model-invocation: true`. Model-invoked skills omit that flag and keep a description the model can match against.

`CONTEXT.md` at this repo's root is the glossary for the skills themselves (issue tracker, issue, triage role). Consumer repos get their own `CONTEXT.md` from `/domain-modeling`.

## Tech stack

- **Skills:** Markdown plus YAML frontmatter. There is no application server and no UI in this repository.
- **Local scripts:** Bash (`scripts/link-skills.sh`, `scripts/list-skills.sh`).
- **Release tooling:** [Changesets](https://github.com/changesets/changesets), run with Node.js. `package.json` sets `packageManager` to npm 10.9.4. The only dependencies are the dev dependencies `@changesets/cli` and `@changesets/changelog-github`.
- **CI:** GitHub Actions (`.github/workflows/release.yml`).

One misc skill installs a Claude Code `PreToolUse` hook. That hook is for Claude Code. The rest of the skills are agent instructions.

## Prerequisites

To install and invoke the skills:

- A coding agent that loads Agent Skills from `SKILL.md` files.
- Node.js with `npx`, for `npx skills@latest add mattpocock/skills`.
- Git.

For a consumer repo whose issue tracker is GitHub, the engineering skills call the `gh` CLI. For GitLab, they call the `glab` CLI. Local Markdown issues need neither.

To work on this repository's versioning:

- Node.js 22 (the version the release workflow installs).
- npm 10.9.4 (`packageManager` in `package.json`).

## Run it locally

There is nothing to start. Using the skills means installing them into an agent (see [Install](#install)) and invoking a slash command such as `/grill-me` or `/tdd`.

To load every non-deprecated skill from a checkout into Claude Code on this machine:

```bash
npm ci
./scripts/list-skills.sh
./scripts/link-skills.sh
```

`link-skills.sh` symlinks each skill directory that contains a `SKILL.md` into `~/.claude/skills`. That includes `personal/` and `in-progress/`. It skips `skills/deprecated/`. It refuses to run if `~/.claude/skills` is already a symlink into this repo. `list-skills.sh` prints every `SKILL.md` path, including deprecated skills.

Version notes for a change:

```bash
npm run changeset
```

`npm run version` applies pending changesets and updates `CHANGELOG.md`. The release workflow runs the same command on GitHub. Run it locally when you want to preview the changelog.

## Configuration

This repository has no `.env` file. The skills do not read environment variables to configure themselves.

The release workflow passes one GitHub Actions secret into Changesets:

- `GITHUB_TOKEN`

Per-repo configuration for the engineering skills is Markdown written by `/setup-matt-pocock-skills`, not environment variables. The files are `docs/agents/issue-tracker.md`, `docs/agents/triage-labels.md`, `docs/agents/domain.md`, and an `## Agent skills` section in `CLAUDE.md` or `AGENTS.md`.

## Tests

This repository has no test suite and no `test` script. The skills are instructions. `npm ci` installs Changesets. That is the only automated check the release workflow runs.

## Versioning and releases

Pushes to `main` run `.github/workflows/release.yml` on `ubuntu-latest`. The job checks out the repo, installs Node.js 22, runs `npm ci`, and calls `changesets/action`.

- When `.changeset/` contains a pending changeset, the action opens a pull request titled `chore: version skills`.
- Merging that pull request runs `npx changeset tag` (the workflow's `publish` input). Changesets is configured with `privatePackages.version` and `privatePackages.tag` both true, so the private package still gets a version and a git tag. `changesets/action@v1` creates a GitHub release for each new tag (`createGithubReleases` defaults to true). `package.json` sets `"private": true` and Changesets `access` is `restricted`.

The changelog generator is `@changesets/changelog-github` for `mattpocock/skills`. `baseBranch` is `main`.
