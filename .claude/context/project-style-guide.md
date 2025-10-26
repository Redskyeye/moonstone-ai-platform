---
created: 2025-10-12T11:19:12Z
last_updated: 2025-10-12T11:19:12Z
version: 1.0
author: Claude Code PM System
---

# Project Style Guide

## File Naming Conventions

### General Rules
- Use kebab-case for all file names (lowercase with hyphens)
- Avoid spaces, underscores, or camelCase in file names
- Keep names descriptive but concise
- Use consistent naming patterns across similar file types

### Specific File Types
- **Commands**: `category-action.md` (e.g., `prd-new.md`, `epic-sync.md`)
- **Context Files**: `descriptive-name.md` (e.g., `project-overview.md`)
- **PRDs**: `feature-name.md` (e.g., `user-authentication.md`)
- **Task Files**: `[issue-id].md` after GitHub sync (e.g., `1234.md`)
- **Scripts**: `action.sh` or `function-action.sh` (e.g., `init.sh`, `pm-sync.sh`)

### Directory Naming
- Use singular nouns for directories (e.g., `command`, not `commands`)
- Keep directory names short and clear
- Group related files in appropriate subdirectories

## Directory Structure Standards

### Organization Principles
```
.claude/
├── agents/              # AI agent definitions
├── commands/            # Command definitions
│   ├── context/        # Context management commands
│   ├── pm/             # Project management commands
│   └── testing/        # Testing commands
├── context/            # Project context files
├── epics/              # Epic storage
│   └── [epic-name]/   # Individual epic directories
├── hooks/              # Git hooks
├── prds/               # PRD files
├── rules/              # Operational rules
└── scripts/            # Automation scripts
    └── pm/             # PM-specific scripts
```

### File Placement Rules
- Commands go in appropriate category subdirectory
- Context files always in `.claude/context/`
- Scripts organized by function/purpose
- Rules grouped by domain or application

## Markdown Formatting Standards

### Frontmatter Requirements
All markdown files must include YAML frontmatter:
```yaml
---
created: YYYY-MM-DDTHH:MM:SSZ
last_updated: YYYY-MM-DDTHH:MM:SSZ
version: x.x
author: Claude Code PM System
---
```

### Content Structure
1. **Title**: H1 heading at the top
2. **Sections**: Use H2-H6 for hierarchical organization
3. **Lists**: Use bullet points for items, numbered lists for steps
4. **Code Blocks**: Use appropriate language identifiers
5. **Links**: Use descriptive link text
6. **Tables**: Use markdown table format for structured data

### Formatting Rules
- **Line Length**: Keep lines under 100 characters when possible
- **Spacing**: Use single blank lines between sections
- **Emphasis**: Use bold for important terms, italics for emphasis
- **Code**: Use inline code for short references, code blocks for longer examples

## Command Documentation Standards

### Command File Structure
```yaml
---
allowed-tools: Tool1, Tool2, Tool3
---
# Command Title

Brief description of what the command does.

## Usage
`/command-name [arguments]`

## Description
Detailed explanation of command functionality.

## Parameters
- `parameter`: Description of parameter

## Examples
```bash
/command-name example
```

## Output
Expected output format or behavior.
```

### Command Naming Patterns
- Use `category:action` format (e.g., `pm:prd-new`)
- Keep names intuitive and discoverable
- Use consistent verbs for similar operations
- Group related commands under same category

## Script Writing Standards

### Shell Script Guidelines
- Use `#!/bin/bash` shebang
- Include error handling with `set -e`
- Use meaningful variable names
- Add comments for complex logic
- Use functions for reusable code

### Script Organization
```bash
#!/bin/bash

# Configuration
readonly SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
readonly PROJECT_ROOT="$(dirname "$SCRIPT_DIR")"

# Functions
function check_dependencies() {
  # Function implementation
}

function main() {
  # Main logic
}

# Execution
main "$@"
```

## Context File Standards

### Context File Categories
- **project-overview.md**: High-level project summary
- **project-structure.md**: Directory and file organization
- **tech-context.md**: Technical stack and dependencies
- **system-patterns.md**: Architectural patterns and design decisions
- **product-context.md**: User needs and business requirements
- **project-brief.md**: Scope, goals, and success criteria
- **project-vision.md**: Long-term goals and strategic direction
- **project-style-guide.md**: Coding standards and conventions
- **progress.md**: Current status and recent work

### Content Guidelines
- Use consistent structure across similar files
- Include specific, actionable information
- Update timestamps when content changes
- Cross-reference related files where appropriate

## Git Integration Standards

### Commit Message Format
```
type(scope): brief description

Detailed explanation if needed.

Closes #issue-number
```

### Branch Naming
- Use `feature/description` for feature branches
- Use `fix/description` for bug fixes
- Use `chore/description` for maintenance tasks

### GitHub Integration
- Use descriptive issue titles
- Apply appropriate labels (epic, task)
- Reference issues in commits and pull requests
- Use comments for progress updates

## Quality Standards

### Documentation Quality
- All commands must have clear documentation
- Examples should be tested and accurate
- Error messages should be helpful and actionable
- Cross-references should be accurate

### Code Quality
- Scripts should handle errors gracefully
- Commands should validate inputs
- File operations should check permissions
- Dependencies should be clearly documented

### Usability Standards
- Commands should be intuitive to use
- Help should be readily available
- Error messages should guide users to solutions
- Workflows should be efficient and logical

## Review and Maintenance

### Regular Reviews
- Review and update documentation monthly
- Validate all commands still work
- Check for broken links or references
- Update version numbers as needed

### Maintenance Guidelines
- Keep documentation in sync with functionality
- Remove deprecated features promptly
- Update examples to reflect current behavior
- Maintain consistent formatting across all files