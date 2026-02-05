# Flutter Powers Development Guide

This project is dedicated to creating Kiro Powers for Flutter development. Powers package tools, workflows, and best practices into a format that Kiro can activate on-demand.

## Power Structure

Each Power must follow this directory structure:

```text
power-name/
├── POWER.md           # Main file with frontmatter and documentation
├── mcp.json           # MCP server configuration (optional)
└── steering/          # Additional steering files (optional)
    ├── workflow-1.md
    └── workflow-2.md
```

## POWER.md Format

The POWER.md file has two parts: YAML frontmatter and markdown content.

### Frontmatter (Metadata)

```yaml
---
name: "Flutter Widget Builder"
description: "Build Flutter widgets with best practices and Material Design guidelines"
keywords:
  - flutter
  - widget
  - dart
  - material
  - cupertino
  - ui
  - mobile
---
```

Frontmatter fields:

- `name`: Descriptive name of the Power
- `description`: Brief description of what the Power does
- `keywords`: List of keywords that activate the Power when the user mentions them

### POWER.md Content

Recommended structure:

1. **Onboarding**: Initial setup steps (install CLI, hooks, dependencies)
2. **Steering Instructions**: Workflows and best practices
3. **Steering Files Map**: References to additional steering files for specific workflows

## MCP Configuration (mcp.json)

If the Power includes an MCP server:

```json
{
  "mcpServers": {
    "flutter-tools": {
      "command": "npx",
      "args": ["-y", "@your-org/flutter-mcp-server"],
      "env": {
        "FLUTTER_SDK": "${env:FLUTTER_SDK}"
      }
    }
  }
}
```

## Keywords for Flutter Powers

Use keywords that match how developers talk about Flutter:

- General: `flutter`, `dart`, `mobile`, `cross-platform`
- UI: `widget`, `material`, `cupertino`, `ui`, `layout`
- State: `state`, `bloc`, `cubit`
- Navigation: `navigation`, `routing`, `go_router`
- Localization: `i18n`, `l10n`, `localization`, `translation`
- Testing: `test`, `widget test`, `integration test`
- Build: `build`, `release`, `apk`, `ipa`, `app bundle`

## Best Practices

1. **Specific keywords**: Use terms that Flutter developers actually use
2. **Modular steering**: Separate complex workflows into individual steering files
3. **Clear documentation**: The POWER.md should be a complete guide for the agent
4. **MCP optional**: Not all Powers need an MCP server
5. **Useful hooks**: Consider adding hooks to automate common tasks
6. **English only**: All documentation and code comments must be in English

## Reference

- Official documentation: https://kiro.dev/docs/powers/create/
- Examples: https://github.com/kirodotdev/powers
