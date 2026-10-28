# TechVal Coworker Playground Marketplace

A Claude plugin marketplace for TechVal Coworker Playground skills.
Public GitHub repository:
[tvcoworkerplay/techval-coworker-playground-mktplc](https://github.com/tvcoworkerplay/techval-coworker-playground-mktplc).

## Available plugins

| Plugin | Skill | Purpose |
| --- | --- | --- |
| `fsi-live-playground` | `fsi-live-playground-cja` | Canonical FSI Live mortgage stages, segments, rounding, and attribution rules for Adobe Customer Journey Analytics (CJA). |

## Prerequisites

- Claude Code with plugin marketplace support.
- An existing `cja` skill and the authenticated CJA tools it uses.
- Authorized access to the FSI Live data view. Workspace project and saved
  segment access are recommended; the skill defines fallbacks if unavailable.

This marketplace supplies analysis instructions, not a CJA connector,
credentials, access grants, or demo data. The skill's
`metadata.dependencies.skills` declaration documents its `cja` prerequisite;
it does not automatically install or enforce that dependency.

## Install locally

From the root of this folder in PowerShell:

```powershell
claude plugin marketplace add .
claude plugin install fsi-live-playground@techval-coworker-playground-mktplc
```

Start a new Claude Code session after installation. Invoke the skill with:

```text
/fsi-live-playground:fsi-live-playground-cja
```

For example, ask it to analyze mortgage application drop-off for a specific
date range. The skill requires a date range before running any analysis.

## Install from GitHub

```text
/plugin marketplace add tvcoworkerplay/techval-coworker-playground-mktplc
/plugin install fsi-live-playground@techval-coworker-playground-mktplc
```

Complete installation in the plugin panel, then start a new session.

## Repository layout

```text
.claude-plugin/
  marketplace.json
plugins/
  fsi-live-playground/
    .claude-plugin/
      plugin.json
    skills/
      fsi-live-playground-cja/
        SKILL.md
```

- [Marketplace catalog](.claude-plugin/marketplace.json)
- [Plugin manifest](plugins/fsi-live-playground/.claude-plugin/plugin.json)
- [FSI Live CJA skill](plugins/fsi-live-playground/skills/fsi-live-playground-cja/SKILL.md)

The initial skill is preserved as supplied.

## Add another skill or plugin

To extend the existing plugin, add a `skills/<skill-name>/SKILL.md` folder
inside it with matching `name` and a meaningful `description` in YAML
frontmatter.

For an independently installable plugin, create
`plugins/<plugin-name>/.claude-plugin/plugin.json`, place its skills under
`plugins/<plugin-name>/skills/`, and register it in the marketplace catalog.
The catalog entry and manifest must use the same plugin name; the catalog's
`source` is relative to the repository root.

Validate changes from the repository root:

```powershell
claude plugin validate .
claude plugin validate .\plugins\fsi-live-playground
```

Before publishing additional content, confirm permission to distribute each
skill and its referenced identifiers. Never include credentials, customer
data, or confidential supporting files.

## License

This repository is licensed under the [MIT License](LICENSE).
The license does not grant access to Adobe services, projects, or data.
