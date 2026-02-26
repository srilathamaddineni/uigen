# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

UIGen is an AI-powered React component generator with live preview. Users describe React components in a chat interface, and Claude generates them in a virtual file system with real-time preview. Built with Next.js 15 App Router.

## Commands

```bash
npm run setup        # First-time setup: install deps + Prisma generate + migrate
npm run dev          # Start dev server with Turbopack
npm run build        # Production build (requires NODE_OPTIONS compat shim)
npm run test         # Run tests with Vitest
npm run lint         # ESLint
npm run db:reset     # Reset SQLite database
```

Run a single test file: `npx vitest run src/lib/transform/__tests__/jsx-transformer.test.ts`

## Architecture

### Data Flow

1. User types in `ChatInterface` → `chat-context.tsx` calls `/api/chat` with serialized VFS
2. API route streams responses from Claude (or `MockLanguageModel` if no API key)
3. Claude uses two tools: `str_replace_editor` (create/view/edit files) and `file_manager` (rename/delete/list)
4. Tool calls are processed in `file-system-context.tsx` (`handleToolCall`) to update the in-memory VFS
5. VFS changes trigger re-render of `PreviewFrame` and `FileTree`
6. After streaming completes, project state (messages + VFS) is persisted to SQLite via Prisma

### Virtual File System (`src/lib/file-system.ts`)

All files exist only in memory as a `VirtualFileSystem` class instance. It serializes to/from JSON for persistence and API transport. No disk I/O for generated files. The AI is given tools that mirror editor commands (view, create, str_replace, insert).

### Preview System (`src/lib/transform/jsx-transformer.ts`)

The preview uses Babel Standalone to compile JSX in the browser, creates blob URLs for each file, builds an import map, and renders inside a sandboxed iframe. Third-party imports resolve via `esm.sh`. Tailwind CSS is injected via CDN. Entry point detection looks for `App.jsx`, `index.jsx`, etc.

### AI Provider (`src/lib/provider.ts`)

- If `ANTHROPIC_API_KEY` is set: uses Claude via `@ai-sdk/anthropic`
- Otherwise: `MockLanguageModel` streams pre-built component templates (for demo/dev without an API key)

The system prompt (`src/lib/prompts/generation.tsx`) instructs Claude to always create `/App.jsx` as the entry point, use Tailwind CSS (no inline styles), and use `@/` alias for cross-file imports.

### Auth & Persistence

JWT sessions via `jose` library stored in HTTP-only cookies (7-day expiry). Passwords hashed with bcrypt. Database: SQLite via Prisma. Projects store serialized chat messages and VFS as JSON strings in text columns.

Anonymous users can generate components (tracked via `anon-work-tracker.ts`); projects are saved only for authenticated users.

### State Management

Two main React contexts:
- `chat-context.tsx`: Wraps Vercel AI SDK's `useChat()`, handles file serialization for API body
- `file-system-context.tsx`: Holds the `VirtualFileSystem` instance, exposes file CRUD ops and `handleToolCall`

### Key Path Aliases

`@/*` maps to `./src/*` (configured in `tsconfig.json`).

## Environment Variables

- `ANTHROPIC_API_KEY` — Optional. Without it, uses mock provider with static templates.
- `JWT_SECRET` — Auto-generated if not set. Set explicitly in production.
- `DATABASE_URL` — Defaults to `file:./dev.db` (SQLite).

## Code Style

Use comments sparingly — only for complex logic that isn't self-evident from the code.

## Tech Stack

- **Frontend:** React 19, Next.js 15, Tailwind CSS v4, Radix UI, Monaco Editor, React Resizable Panels
- **Backend:** Next.js API Routes, Prisma v6 + SQLite
- **AI:** Vercel AI SDK v4 + `@ai-sdk/anthropic`, Babel Standalone for JSX transformation
- **Testing:** Vitest + @testing-library/react
