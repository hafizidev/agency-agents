# Hermes Integration

Installs the full Agency roster as Hermes Agent skills. Each agent becomes a
workspace under `~/.hermes/agency-agents/<slug>/` with `SOUL.md`, `AGENTS.md`,
`IDENTITY.md`, and a `SKILL.md` entry point (same split as OpenClaw, plus a
Hermes skill wrapper).

Before installing, generate the Hermes workspaces:

```bash
./scripts/convert.sh --tool hermes
```

## Install

```bash
./scripts/install.sh --tool hermes
```

This copies workspaces to `~/.hermes/agency-agents/` and registers the directory
in `~/.hermes/config.yaml` under `skills.external_dirs` when possible.

## Activate a Skill

In the Hermes CLI or TUI:

```bash
hermes chat --toolsets skills -q "Use the frontend-developer skill to review this component"
```

Or use a slash command (skill name matches the slug):

```
/frontend-developer
```

Available slugs follow `slugify(agent-name)`, e.g.:

- `frontend-developer`
- `backend-architect`
- `reality-checker`
- `growth-hacker`

## Workspace Layout

Each generated workspace contains:

| File | Role |
|------|------|
| `SKILL.md` | Hermes skill entry — When to Use, Procedure, links to bundled files via `${HERMES_SKILL_DIR}` |
| `SOUL.md` | Persona, identity, communication style, critical rules |
| `AGENTS.md` | Mission, deliverables, workflows, success metrics |
| `IDENTITY.md` | Display name, emoji, vibe |

## Profiles

Hermes profiles use separate `HERMES_HOME` directories. By default, install
targets `~/.hermes/` (default profile). For an isolated Agency profile:

```bash
hermes profile create agency --clone
# Re-run install with HERMES_HOME pointing at the profile, or copy agency-agents
# into ~/.hermes/profiles/agency/ and add external_dirs in that profile's config.yaml
```

## Manual external_dirs

If the installer could not update your config, add **under the existing `skills:` section** (not at the end of the file):

```yaml
skills:
  external_dirs:
    - ~/.hermes/agency-agents
```

## Fix broken config.yaml

If `hermes` reports a YAML parse error and you see a stray line at the **end** of
`config.yaml`:

```yaml
    - ~/.hermes/agency-agents
```

after comments or `platform_toolsets`, **delete that line**. It was appended outside
`skills.external_dirs` by an older installer and breaks the whole config.

**Invalid** (do not leave like this):

```yaml
skills:
  external_dirs: []
    - ~/.hermes/agency-agents
```

**Valid** block form:

```yaml
skills:
  external_dirs:
    - ~/.hermes/agency-agents
```

If you have `external_dirs: []`, **remove the `[]`** and use the block list above
(or delete the broken `- ~/.hermes/agency-agents` line and re-run
`./scripts/install.sh --tool hermes` with the latest installer).

Then run `hermes doctor` or `hermes skills list` to confirm the config parses.

## Regenerate

```bash
./scripts/convert.sh --tool hermes
```
