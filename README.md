# UIGen — AI-Powered React Component Generator

> **Claude Code Practice Project** — Built as a hands-on exercise using [Claude Code](https://claude.ai/code), Anthropic's AI-powered CLI for software development.

## What is UIGen?

UIGen is a full-stack web application that lets you generate React components using natural language. You describe the component you want — a button, a form, a dashboard card — and Claude AI writes the code for you instantly, with a live preview rendered right in the browser.

No file I/O, no boilerplate. Just describe and preview.

---

## Features

- **AI-Powered Generation** — Describe any React component in plain English and get production-ready JSX back instantly
- **Live Preview** — Components render in a sandboxed iframe with Tailwind CSS, no build step required
- **Virtual File System** — All generated files exist in memory; the AI can create, edit, and delete files just like a real editor
- **Code Editor** — Browse and edit generated files with Monaco Editor (the same editor powering VS Code)
- **Multi-file Support** — Generate complex components split across multiple files with proper import resolution
- **Auth & Persistence** — Sign up to save and revisit your projects; anonymous users can generate without an account
- **Mock Mode** — Works without an Anthropic API key by returning static component templates (great for development)

---

## Tech Stack

| Layer | Technology |
|---|---|
| Framework | Next.js 15 (App Router) |
| UI | React 19, Tailwind CSS v4, Radix UI |
| Editor | Monaco Editor |
| AI | Anthropic Claude via Vercel AI SDK v4 |
| JSX Compilation | Babel Standalone (in-browser) |
| Database | SQLite via Prisma v6 |
| Auth | JWT (jose) + bcrypt |
| Testing | Vitest + Testing Library |

---

## Getting Started

### Prerequisites

- Node.js 18+
- npm

### Setup

1. Clone the repo and install dependencies:

```bash
git clone https://github.com/your-username/uigen.git
cd uigen
npm run setup
```

`npm run setup` installs dependencies, generates the Prisma client, and runs database migrations.

2. (Optional) Add your Anthropic API key to `.env`:

```env
ANTHROPIC_API_KEY=your-api-key-here
```

Without an API key, UIGen runs in **mock mode** and returns static component templates instead of calling Claude.

3. Start the development server:

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) in your browser.

---

## Usage

1. **Start a conversation** — Type a description like *"Create a pricing card with a gradient background and a CTA button"*
2. **Watch it generate** — The AI creates the files and your component renders live in the preview panel
3. **Iterate** — Ask for changes, additions, or refinements in follow-up messages
4. **Inspect the code** — Switch to the Code tab to browse and edit the generated files
5. **Save your work** — Sign up to persist projects across sessions

---

## Project Structure

```
src/
├── app/                  # Next.js App Router pages and API routes
├── components/           # UI components (chat, editor, preview, auth)
├── lib/
│   ├── contexts/         # React contexts (chat, file system)
│   ├── tools/            # AI tool definitions (str_replace_editor, file_manager)
│   ├── transform/        # JSX → blob URL compilation pipeline
│   ├── prompts/          # System prompt for Claude
│   ├── file-system.ts    # In-memory virtual file system
│   ├── provider.ts       # AI provider (Claude or mock)
│   └── auth.ts           # JWT auth helpers
├── actions/              # Next.js server actions (project CRUD)
└── hooks/                # Custom React hooks
```

---

## Available Scripts

```bash
npm run dev        # Start dev server with Turbopack
npm run build      # Production build
npm run test       # Run tests with Vitest
npm run lint       # ESLint
npm run db:reset   # Reset the SQLite database
```

---

## About This Project

This project was built as a **Claude Code practice exercise** — exploring how Claude Code can assist in scaffolding, building, and iterating on a real full-stack application. The development workflow leaned heavily on Claude Code's ability to understand context, edit multiple files, run commands, and reason about architecture.

It demonstrates practical use of:
- Vercel AI SDK streaming with custom tool calls
- In-browser JSX compilation with import maps
- Virtual file system design for AI-driven code generation
- Next.js 15 App Router with server actions and JWT auth

---

## License

MIT
