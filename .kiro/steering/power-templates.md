# Templates for Flutter Powers

## Template: Basic Power (No MCP)

```markdown
---
name: "Flutter [Feature Name]"
description: "[Brief description of the Power]"
keywords:
  - flutter
  - [keyword1]
  - [keyword2]
---

# Flutter [Feature Name] Power

## Overview

[Description of what this Power does and when to use it]

## Onboarding

### Prerequisites

- Flutter SDK installed
- [Other dependencies]

### Setup

1. [Setup step 1]
2. [Setup step 2]

## Workflows

### [Workflow 1]

[Detailed instructions for the agent]

### [Workflow 2]

[Detailed instructions for the agent]

## Best Practices

- [Practice 1]
- [Practice 2]

## Common Patterns

### [Pattern Name]

\`\`\`dart
// Example code
\`\`\`
```

## Template: Power with MCP Server

### POWER.md

```markdown
---
name: "Flutter [Feature Name]"
description: "[Brief description]"
keywords:
  - flutter
  - [keywords]
---

# Flutter [Feature Name] Power

## Overview

This Power provides tools for [description].

## MCP Tools Available

- `tool_name`: [Tool description]
- `another_tool`: [Description]

## Onboarding

### Prerequisites

- Flutter SDK
- [MCP server dependencies]

## Workflows

### Using [Tool Name]

When you need [use case], use the `tool_name` tool with:
- Parameter 1: [description]
- Parameter 2: [description]

## Steering Files

For specific workflows, see:
- `steering/widgets.md` - Widget creation guide
- `steering/testing.md` - Testing guide
```

### mcp.json

```json
{
  "mcpServers": {
    "flutter-power-server": {
      "command": "npx",
      "args": ["-y", "@your-org/flutter-mcp-server@latest"],
      "env": {
        "FLUTTER_SDK": "${env:FLUTTER_SDK}"
      },
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

## Template: Additional Steering File

```markdown
---
inclusion: manual
---

# [Workflow Name] Guide

## When to Use

[Description of when to apply this steering]

## Instructions

### Step 1: [Step name]

[Detailed instructions]

### Step 2: [Step name]

[Detailed instructions]

## Code Examples

\`\`\`dart
// Relevant example
\`\`\`

## Common Mistakes to Avoid

- [Common mistake 1]
- [Common mistake 2]
```

## Power Ideas for Flutter

### 1. Flutter Widget Builder

- Keywords: widget, ui, layout, material, cupertino
- Functionality: Guide for creating widgets following Material Design

### 2. Flutter State Management

- Keywords: state, bloc, cubit
- Functionality: BLoC/Cubit state management patterns

### 3. Flutter Testing

- Keywords: test, widget test, integration, golden
- Functionality: Testing guides and test generation

### 4. Flutter Navigation

- Keywords: navigation, routing, go_router, navigator
- Functionality: Navigation patterns

### 5. Flutter Firebase

- Keywords: firebase, firestore, auth, cloud
- Functionality: Firebase integration

### 6. Flutter Animations

- Keywords: animation, transition, hero, implicit
- Functionality: Animation guides

### 7. Flutter Localization

- Keywords: i18n, l10n, localization, translation
- Functionality: Internationalization with easy_localization

### 8. Flutter Performance

- Keywords: performance, optimization, profiling
- Functionality: Performance optimization
