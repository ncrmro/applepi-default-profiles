# Outfitter Default Profiles

Default profile source for Outfitter users.

## Profiles

- `founder` - founder-operator setup for building, product thinking, research checks, dense prose, and careful delivery.
- `engineer` - engineering setup for repository navigation, maintainable code changes, tests, and reviews.
- `data-analyst` - data analysis setup for careful inspection, reproducible methods, assumptions, and summaries. See [docs/data-analyst.md](docs/data-analyst.md).
- `media-editor` - video post-production setup for transcript-driven editing with whisper.cpp and ffmpeg. See [docs/media-editor.md](docs/media-editor.md).
- `issue-triage` - CI triage agent that labels newly opened issues `fix`/`feature`/`idea` against CONTRIBUTING.md standards. See [docs/issue-triage.md](docs/issue-triage.md).

## Quick start

```bash
outfitter setup https://github.com/ai-outfitter/default-profiles
outfitter run --profile engineer
```

See [docs/usage.md](docs/usage.md) for manual settings configuration and syncing.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).
