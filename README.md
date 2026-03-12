# CLAUDE.md Generator — GitHub Action

Automatically generate a [CLAUDE.md](https://docs.anthropic.com/en/docs/claude-code/memory#claudemd-files) governance file for your repository. Detects your tech stack, frameworks, tools, and project structure — then produces a ready-to-use CLAUDE.md.

## Why?

Claude Code reads `CLAUDE.md` to understand your project's conventions, commands, and architecture. A well-written CLAUDE.md dramatically improves Claude's code quality. But writing one from scratch is tedious.

This action does it automatically.

## Supported Stacks

| Language | Frameworks | Tools |
|----------|-----------|-------|
| TypeScript/JavaScript | Next.js, React, Vue, Svelte, Angular, Astro, Nuxt, Remix, Express, Fastify, Hono | npm, yarn, pnpm, bun |
| Python | FastAPI, Django, Flask | pip, poetry, pdm |
| Go | Gin, Echo, Fiber | go modules |
| Rust | Actix Web, Axum | cargo |
| Ruby | Rails | bundler |

Also detects: Docker, GitHub Actions, Terraform, Vercel, Netlify, Fly.io, Prisma, Drizzle, SQLAlchemy, Tailwind CSS, ESLint, Prettier, Biome, Ruff, Black, mypy, monorepo setups (Turborepo, Nx, Lerna).

## Quick Start

```yaml
# .github/workflows/generate-claude-md.yml
name: Generate CLAUDE.md
on:
  push:
    branches: [main]
  workflow_dispatch:

jobs:
  generate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: goblinmode2700/claude-md-generator@v1
        with:
          style: standard  # minimal | standard | comprehensive
```

## Usage

### On Push to Main (auto-commit)

```yaml
name: Generate CLAUDE.md
on:
  push:
    branches: [main]

jobs:
  generate:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: actions/checkout@v4

      - name: Generate CLAUDE.md
        uses: goblinmode2700/claude-md-generator@v1
        with:
          style: comprehensive

      - name: Commit CLAUDE.md
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"
          git add CLAUDE.md
          git diff --staged --quiet || git commit -m "chore: auto-generate CLAUDE.md"
          git push
```

### Manual Trigger (workflow_dispatch)

```yaml
name: Generate CLAUDE.md
on:
  workflow_dispatch:
    inputs:
      style:
        description: 'Governance style'
        type: choice
        options: [minimal, standard, comprehensive]
        default: standard

jobs:
  generate:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: actions/checkout@v4
      - uses: goblinmode2700/claude-md-generator@v1
        with:
          style: ${{ inputs.style }}
      - run: |
          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"
          git add CLAUDE.md
          git diff --staged --quiet || git commit -m "chore: auto-generate CLAUDE.md"
          git push
```

### Dry Run (preview only)

```yaml
- uses: goblinmode2700/claude-md-generator@v1
  id: preview
  with:
    dry-run: 'true'
    style: comprehensive

- name: Show preview
  run: echo "${{ steps.preview.outputs.claude-md }}"
```

### Use Outputs

```yaml
- uses: goblinmode2700/claude-md-generator@v1
  id: gen

- name: Show detected stack
  run: echo "${{ steps.gen.outputs.detected-stack }}"
```

## Inputs

| Input | Description | Default |
|-------|-------------|---------|
| `style` | Governance detail level: `minimal`, `standard`, `comprehensive` | `standard` |
| `output-path` | Where to write the file | `CLAUDE.md` |
| `dry-run` | Preview without writing to disk | `false` |

## Outputs

| Output | Description |
|--------|-------------|
| `claude-md` | The generated CLAUDE.md content |
| `detected-stack` | Detected tech stack as JSON |
| `file-path` | Path where CLAUDE.md was written |

## Governance Styles

- **`minimal`** — Stack + commands only. For small projects or when you want to customize manually.
- **`standard`** — Stack + commands + code style + architecture overview. Good default for most projects.
- **`comprehensive`** — Everything above + git conventions, Docker notes, error handling guidelines. For team projects.

## Need More?

This action generates a solid starting point. For production-grade CLAUDE.md files with custom agent modes, HITL protocols, and multi-agent orchestration patterns, check out the [full toolkit at NecessitasLabs](https://necessitaslabs.vercel.app).

## License

MIT

## Also Check Out

- [Claude Code Plugins](https://github.com/goblinmode2700/claude-code-plugins) — installable skills for Claude Code (CLAUDE.md Architect, Sprint Manager)
- [Free CLAUDE.md Generator](https://necessitaslabs.vercel.app) — browser-based generator, no install needed
- [Resume Job Fit Scorer](https://necessitaslabs.vercel.app/resume-scorer) — free ATS compatibility checker
