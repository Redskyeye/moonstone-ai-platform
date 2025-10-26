---
created: 2025-10-12T11:19:12Z
last_updated: 2025-10-12T11:19:12Z
version: 1.0
author: Claude Code PM System
---

# Technical Context

## Technology Stack

### Core Technologies
- **Language**: Shell/Bash (primary automation)
- **Platform**: Cross-platform (Unix/Linux/macOS/Windows)
- **Integration**: GitHub CLI, Git, GitHub API
- **AI Platform**: Claude Code (Anthropic)

### Dependencies
- **GitHub CLI (gh)**: Required for GitHub operations
- **Git**: Version control and repository management
- **gh-sub-issue extension**: Parent-child issue relationships
- **Shell environment**: Bash/POSIX compatible shell

### Development Tools
- **Markdown**: Documentation and command definitions
- **Frontmatter**: YAML metadata in markdown files
- **Git Hooks**: Automation triggers
- **GitHub Actions**: Potential CI/CD integration

## System Architecture

### Command Processing
- Markdown-based command definitions
- Frontmatter-specified tool permissions
- Context-aware command execution
- Error handling and validation

### GitHub Integration
- Issues API for task management
- Labels for organization (epic, task)
- Comments for progress tracking
- Extensions for enhanced functionality

### Context System
- Persistent storage in `.claude/context/`
- Timestamped documentation
- Version-controlled context updates
- Template-based file generation

## File Formats

### Command Format
```yaml
---
allowed-tools: Tool1, Tool2, Tool3
---
Command description and instructions
```

### Context Format
```yaml
---
created: YYYY-MM-DDTHH:MM:SSZ
last_updated: YYYY-MM-DDTHH:MM:SSZ
version: x.x
author: Claude Code PM System
---
Content in markdown format
```

### PRD Format
- Frontmatter with metadata
- Structured sections for requirements
- User stories and acceptance criteria
- Technical constraints and considerations

## Security Considerations
- GitHub token management via CLI
- No hardcoded credentials
- Permission-based tool access
- Secure file handling

## Performance Characteristics
- Lightweight shell-based operations
- Minimal external dependencies
- Fast local file operations
- Efficient GitHub API usage