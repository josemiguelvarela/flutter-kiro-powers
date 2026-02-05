# Tech Stack

## Main Technologies

- **Flutter**: Cross-platform mobile development framework
- **Dart**: Programming language
- **Kiro Powers**: Knowledge packaging system for AI agents

## Power Structure

### Required Files

| File | Purpose |
|------|---------|
| `POWER.md` | Main documentation with YAML frontmatter |

### Optional Files

| File | Purpose |
|------|---------|
| `mcp.json` | MCP server configuration |
| `steering/*.md` | Additional workflow guides |

## Frontmatter Format

```yaml
---
name: string          # Power name
description: string   # Brief description
keywords: string[]    # List of activation keywords
---
```

## Code Conventions

### Dart/Flutter

- Follow Dart style guides: https://dart.dev/guides/language/effective-dart/style
- Use `flutter analyze` to verify code
- Prefer const widgets when possible
- Document public APIs with dartdoc
- ALL code comments MUST be in English

### Markdown

- Use hierarchical headers (h1 > h2 > h3)
- Include code examples with syntax highlighting
- Keep lines under 100 characters when possible
- ALL documentation MUST be in English

## Testing

- Unit tests for business logic
- Widget tests for UI components
- Integration tests for complete flows
- Golden tests for visual verification

## Localization

- Use easy_localization for all user-facing strings
- Store translations in `assets/translations/` as JSON files
- Support at minimum: English (en) and Spanish (es)
