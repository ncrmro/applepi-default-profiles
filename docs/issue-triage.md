# Issue triage agent

This repository demos running its own profiles in CI via
[`ai-outfitter/actions`](https://github.com/ai-outfitter/actions). Every newly
opened issue launches the `issue-triage` profile
(`.github/workflows/issue-triage.yml`).

## What it does

The agent classifies the issue against the standards in
[CONTRIBUTING.md](../CONTRIBUTING.md) and applies exactly one of three labels —
`fix`, `feature`, or `idea` — then leaves one comment restating the work in the
repository's terms and noting that a follow-up agent can later be assigned to
implement it. If the issue is too unclear to classify, the agent applies no
label and asks a team member to take a look instead.

This is deliberately the first step of an issue-driven pipeline: triage now,
implementation agents later.

## Setup

The three labels must already exist in the repository — `gh issue edit
--add-label` fails on labels that don't exist, and the profile forbids the
agent from creating any. When porting this workflow to another repo, create
them once:

```bash
gh label create fix --description "Something existing is broken or behaving wrongly" --color d73a4a
gh label create feature --description "A concrete, buildable change" --color a2eeef
gh label create idea --description "A rough direction that needs shaping before it is buildable" --color d4c5f9
```

## How it runs

- **Trigger** — `issues: [opened]`; not label-driven, the agent itself does the
  labeling (hard-limited to the three labels above).
- **Templates** — three issue forms (fix request, feature request, idea) that
  structure what the filer provides but apply no labels; classification is the
  agent's call, so a mis-filed template is corrected at triage rather than
  propagated.
- **Model** — the open-source `deepseek/deepseek-v3-0324`, served by
  [GitHub Models](https://docs.github.com/en/github-models). The workflow
  grants `models: read` on the built-in `GITHUB_TOKEN` and installs
  `.github/models.json` as a custom pi provider (`github-models`) — no external
  API keys or paid inference accounts required. Two wiring details matter:
  the copy into `~/.pi/agent/models.json` must run *before* the action step,
  and the config's `"apiKey": "$GITHUB_TOKEN"` resolves at runtime because
  the action exports `GITHUB_TOKEN` on the step that launches the agent.
- **Limits** — GitHub Models' included tier has low per-day request caps and
  tight per-request token limits, and organizations can disable GitHub Models
  entirely. One short run per opened issue fits comfortably; heavier agents
  should use a paid provider key instead.
- **Standards source** — the profile appends `CONTRIBUTING.md` to its system
  prompt (`repo_file:`), so contributor conventions and expectations are what
  the agent triages against; updating CONTRIBUTING.md updates the triage
  behavior with no profile change.
- **Safety** — the workflow passes only the issue number; the agent fetches
  content with `gh`, so untrusted issue text never enters workflow code.
