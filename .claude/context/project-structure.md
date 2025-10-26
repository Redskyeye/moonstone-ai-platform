---
created: 2025-10-12T11:19:12Z
last_updated: 2025-10-12T11:19:12Z
version: 1.0
author: Claude Code PM System
---

# Project Structure

## Directory Organization

```
/Volumes/MAC/AI及重点项目/Projects/web/
├── .claude/                    # Main system directory
│   ├── agents/                 # AI agent definitions
│   ├── commands/               # Command definitions
│   │   ├── context/           # Context management commands
│   │   ├── pm/                # Project management commands
│   │   └── testing/           # Testing commands
│   ├── context/               # Project context files
│   ├── epics/                 # Epic storage
│   ├── hooks/                 # Git hooks
│   ├── prds/                  # PRD storage
│   ├── rules/                 # Operational rules
│   └── scripts/               # Automation scripts
├── ccpm/                      # Distribution copy
├── doc/                       # Additional documentation
├── install/                   # Installation scripts
├── zh-docs/                   # Chinese documentation
├── README.md                  # Main documentation
├── CLAUDE.md                  # Claude instructions
└── [other project files]
```

## Key Patterns

### Command Structure
- All commands are in `.claude/commands/`
- Each command is a markdown file with frontmatter
- Commands organized by category (context, pm, testing)
- Command naming follows `category:action` pattern

### Agent Organization
- Specialized agents for different task types
- Each agent has specific capabilities and context
- Agents are invoked through commands

### Context Management
- `.claude/context/` stores project documentation
- Context files use consistent frontmatter format
- Context is versioned and timestamped

### File Naming Conventions
- Commands: `category-action.md`
- Epic tasks: `[issue-id].md` (after GitHub sync)
- PRDs: `feature-name.md`
- Context: `descriptive-name.md`

## Module Organization

### PM Commands Core
- `/pm:prd-*` - Product requirement document management
- `/pm:epic-*` - Epic planning and execution
- `/pm:issue-*` - Issue tracking and management
- `/pm:sync-*` - GitHub synchronization

### Context System
- `/context:create` - Initial context creation
- `/context:update` - Context refresh
- `/context:prime` - Context loading

### Automation Scripts
- GitHub operations via CLI
- Git workflow automation
- File system operations
- Status reporting