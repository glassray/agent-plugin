# Working on this repo

This repo is the canonical home of Glassray's agent skills. A skill here is consumed two ways,
and has to work in both:

1. **Installed as a directory** - `npx skills add glassray/skills`, or the Claude Code plugin
   marketplace (`.claude-plugin/marketplace.json`).
2. **Fetched as one file** - `https://glassray.ai/SKILL.md` serves `skills/glassray-otel/SKILL.md`
   verbatim to an agent that can only read a URL. (The Glassray monorepo vendors this repo as a
   git submodule to keep that copy identical.)

Because of (2), a skill's `SKILL.md` must be self-sufficient: no `references/` it depends on,
and depth lives in the public docs (`https://glassray.ai/docs/...`, `llms.txt`,
`llms-full.txt`) rather than in copies here that would drift.

## Layout

```
skills/<name>/SKILL.md                  standalone skills
plugins/<plugin>/skills/<name>/SKILL.md skills bundled with a plugin
.claude-plugin/marketplace.json         Claude Code marketplace
.agents/plugins/marketplace.json        Codex marketplace
.github/workflows/validate.yml          reference validator + link check, on every push
```

The skill folder name must match `name` in its frontmatter.

Edit `skills/glassray-otel/SKILL.md` when changing tracing setup. Copy that file to
`plugins/glassray/skills/glassray-otel/SKILL.md` for the bundled install. CI checks that the
two files match.

## Rules

- **One skill per job, never per language or framework.** Route by what's found in the repo
  inside the skill (see `glassray-otel` §3) and link the docs page for the specifics.
- **The description says _when_ to use the skill**, in the words a user would actually type.
  It is the only thing an agent sees before deciding to load the skill.
- **Cite, don't copy.** Endpoints, attribute names, options and env vars must match the
  shipped ingest code and the published docs; anything longer than a few lines links to its
  docs page.
- **Keep `SKILL.md` under 500 lines** (spec recommendation).
- **Never commit or push in the instructions, never hardcode a key** - every skill tells the
  agent to leave changes uncommitted and to read keys from the environment.
- **Regenerate, don't patch.** When a skill drifts, rewrite the section from the current docs
  rather than layering fixes.

## Before opening a PR

```bash
for d in skills/*/ plugins/*/skills/*/; do npx -y skills-ref validate "$d"; done
```

CI runs the same validator plus a link check over every `https://` URL in every `SKILL.md`.
