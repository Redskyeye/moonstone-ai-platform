---
created: 2025-10-12T11:19:12Z
last_updated: 2025-10-12T11:19:12Z
version: 1.0
author: Claude Code PM System
---

# System Patterns

## Architectural Patterns

### Command-Driven Architecture
- All operations initiated through slash commands
- Commands encapsulate specific workflows
- Frontmatter defines tool permissions and constraints
- Consistent error handling and validation

### Context Preservation Pattern
- State maintained in `.claude/` directory
- Each epic maintains isolated context
- Agents read from context directory for project state
- Updates happen locally before GitHub sync

### Parallel Execution Pattern
- Single issue decomposes into multiple work streams
- Different agents handle specialized tasks simultaneously
- Git commits coordinate between parallel agents
- Main thread maintains oversight without implementation details

### GitHub Native Pattern
- Issues serve as the source of truth
- Comments provide audit trail
- Labels organize and categorize work
- No separate databases required

## Design Patterns

### Specification-Driven Development
- Every line of code traces back to requirements
- PRD → Epic → Task → Issue → Code flow
- No "vibe coding" from memory
- Full traceability at every step

### Agent Specialization Pattern
- Different agents for different work types
- Frontend agents handle UI components
- Backend agents handle APIs and services
- Database agents handle schemas and migrations

### Incremental Sync Pattern
- Local operations happen first for speed
- Synchronization with GitHub is explicit
- Bidirectional sync maintains consistency
- Conflict resolution through clear priority rules

## Data Flow Patterns

### PRD Processing Flow
1. Brainstorming → PRD document
2. PRD → Technical epic
3. Epic → Task decomposition
4. Tasks → GitHub issues
5. Issues → Parallel execution

### Context Management Flow
1. Initial context creation
2. Context loading in new sessions
3. Incremental context updates
4. Context synchronization

### Progress Tracking Flow
1. Local progress files
2. GitHub issue comments
3. Status aggregation
4. Dashboard reporting

## Error Handling Patterns

### Graceful Degradation
- GitHub extensions optional, fallback to basic functionality
- Network issues don't block local work
- Partial success states handled appropriately
- Clear error messages with actionable guidance

### Validation Patterns
- Preflight checks before major operations
- Permission validation before file operations
- Data integrity checks after sync operations
- Rollback capabilities for failed operations

## File Organization Patterns

### Separation of Concerns
- Commands separated by category
- Context files have specific purposes
- Scripts organized by function
- Rules provide reusable guidelines

### Naming Conventions
- Commands: `category:action` format
- Files: kebab-case for readability
- Directories: singular nouns for organization
- Templates: clear purpose identification