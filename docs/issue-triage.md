# Issue triage agent

This repository demos running its own profiles in CI via
[`ai-outfitter/actions`](https://github.com/ai-outfitter/actions). Every newly
opened issue launches the `issue_triage` profile
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

## How it runs

- **Trigger** — `issues: [opened]`; not label-driven, the agent itself does the
  labeling (hard-limited to the three labels above).
- **Model** — the open-source `openai/gpt-oss-120b`, served by
  [GitHub Models](https://docs.github.com/en/github-models). The workflow
  grants `models: read` on the built-in `GITHUB_TOKEN` and installs
  `.github/models.json` as a custom pi provider (`github-models`) — no external
  API keys or paid inference accounts required.
- **Standards source** — the profile appends `CONTRIBUTING.md` to its system
  prompt (`repo_file:`), so contributor conventions and expectations are what
  the agent triages against; updating CONTRIBUTING.md updates the triage
  behavior with no profile change.
- **Safety** — the workflow passes only the issue number; the agent fetches
  content with `gh`, so untrusted issue text never enters workflow code.
