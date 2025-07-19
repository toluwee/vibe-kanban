# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Standard Workflow Commands

1. First think through the problem, read the codebase for relevant files, and write a plan to todo.md.
2. The plan should have a list of todo items that you can check off as you complete them
3. Before you begin working, check in with me and I will verify the plan.
4. Then, begin working on the todo items, marking them as complete as you go.
5. Please every step of the way just give me a high level explanation of what changes you made
6. Make every task and code change you do as simple as possible. We want to avoid making any massive or complex changes. Every change should impact as little code as possible. Everything is about simplicity.
7. Finally, add a review section to the todo.md file with a summary of the changes you made and any other relevant information.

## Development Commands

### Setup
```bash
pnpm i                  # Install all dependencies
pnpm run dev           # Start frontend and backend with live reloading
```

### Development
```bash
pnpm run frontend:dev   # Start frontend dev server only (port 3000)
pnpm run backend:dev:watch  # Start backend with auto-reload
pnpm run dev:clear-ports    # Clear stuck development ports
```

### Building
```bash
pnpm run build         # Build both frontend and backend
pnpm run build:single  # Build backend without MCP task server
pnpm run build:npm     # Build npm package (./build-npm-package.sh)
```

### Testing and Quality
```bash
npm run cargo -- test --lib            # Run Rust backend tests
cd frontend && npm run lint             # Lint frontend code
cd frontend && npm run lint:fix         # Fix frontend linting issues
cd frontend && npm run format           # Format frontend code with Prettier
cd frontend && npm run format:check     # Check frontend formatting
```

### Type Generation
```bash
pnpm run generate-types        # Generate TypeScript types from Rust
pnpm run generate-types:check  # Check if types are up to date
```

### Database
```bash
pnpm run prepare-db           # Setup development database
```

## Architecture Overview

Vibe Kanban is a full-stack application that orchestrates AI coding agents (Claude Code, Gemini CLI, etc.) with a kanban-style task management interface.

### Stack
- **Backend**: Rust (Axum web framework, SQLx with SQLite)
- **Frontend**: React + TypeScript (Vite, TailwindCSS, Radix UI)
- **Database**: SQLite with migrations
- **Deployment**: Single binary with embedded frontend assets

### Key Components

#### Backend (`backend/src/`)
- **Executors** (`executors/`): Integrations with AI coding agents (Claude, Gemini, etc.)
- **Models** (`models/`): Database entities (projects, tasks, task_attempts, execution_processes)
- **Routes** (`routes/`): REST API endpoints
- **Services** (`services/`): Business logic (git, GitHub, analytics, notifications)
- **MCP** (`mcp/`): Model Context Protocol server implementation
- **Utils** (`utils/`): Worktree management, shell operations, path utilities

#### Frontend (`frontend/src/`)
- **Pages**: Main application views (projects, project-tasks, settings)
- **Components**: Reusable UI components with task management focus
- **Task Management**: Kanban board, task details, diff viewing, conversation logs
- **API Integration**: Real-time communication with backend via fetch and SSE

### Core Concepts

- **Projects**: Git repositories managed by the application
- **Tasks**: Work items with descriptions that get executed by AI agents
- **Task Attempts**: Individual execution runs of a task by a specific AI agent
- **Execution Processes**: Runtime processes spawned during task execution
- **Executors**: Pluggable AI agent integrations (claude, gemini, amp, etc.)
- **Worktrees**: Git worktrees for isolated task execution environments

### Database Schema
Key tables: `projects`, `tasks`, `task_attempts`, `execution_processes`, `executor_sessions`, `task_templates`

### Important Patterns

1. **Type Safety**: Rust types are generated to TypeScript using `ts-rs`
2. **Real-time Updates**: Server-Sent Events for live task execution streaming
3. **Git Integration**: Automated branch/worktree management for task isolation
4. **Process Management**: Careful handling of spawned AI agent processes
5. **Error Handling**: Comprehensive error propagation from Rust to frontend

### Configuration
- Runtime config stored in SQLite database
- Build-time environment variables for GitHub OAuth and analytics
- Development uses auto-assigned ports with fallbacks

### Entry Points
- **Main binary**: `backend/src/main.rs` - Web server and application logic
- **MCP Server**: `backend/src/bin/mcp_task_server.rs` - Model Context Protocol server
- **Type Generator**: `backend/src/bin/generate_types.rs` - TypeScript type generation