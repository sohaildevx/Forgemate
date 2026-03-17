## Forgemate

Command‑line AI assistant and web dashboard built with a Node.js server and a Next.js client.

The server provides authentication, AI chat, and an agent mode that can scaffold full applications. The CLI wraps these capabilities, while the client offers a browser UI.

---

## Features

- **CLI AI chat**: Start interactive chats with OpenAI models directly from your terminal.
- **Agent mode**: Generate full application structures (files, folders, setup commands) from natural language descriptions.
- **Tool calling (extensible)**: Framework for adding tools such as web search or code execution.
- **Authentication**: Device-code style login flow backed by Prisma and PostgreSQL.
- **Web client**: Next.js app for login and basic interaction with the server.

---

## Repository Structure

```text
.
├─ client/                 # Next.js web client
│  ├─ app/                 # App router pages and layouts
│  ├─ components/          # Shared UI components
│  ├─ lib/                 # Client-side utilities
│  ├─ public/              # Static assets
│  └─ package.json
│
├─ server/                 # Node/Express API + CLI
│  ├─ src/
│  │  ├─ cli/              # CLI entrypoint and commands
│  │  │  ├─ ai/            # OpenAI service wrapper
│  │  │  ├─ chat/          # Chat and agent mode flows
│  │  │  └─ commands/      # Auth and AI commands (login, wakeUp, etc.)
│  │  ├─ config/           # OpenAI, agent, and tool configuration
│  │  ├─ lib/              # Prisma client, auth helpers, token storage
│  │  ├─ service/          # Domain services (chat service)
│  │  └─ index.ts          # HTTP server entry
│  ├─ prisma/              # Prisma schema and migrations
│  └─ package.json
│
├─ github/
│  └─ screenshot/          # Project screenshots used in this README
│     ├─ cli-login.png
│     ├─ cli-options.png
│     └─ Device-auth.png
│
└─ client/README.md        # Client-specific documentation
```

---

## Prerequisites

- Node.js 20+ (recommended)
- npm (or another Node package manager)
- PostgreSQL database (for Prisma)
- OpenAI API key

---

## Server Setup (`server/`)

From the `server` directory:

```bash
cd server
npm install
```

Create a `.env` file in `server/`:

```bash
OPENAI_API_KEY=sk-your-openai-key
DATABASE_URL=postgresql://user:password@localhost:5432/cli_ai_agentd
GITHUB_CLIENT_ID=MCDSIGDAYGDGG6544HHUUUHUF4U
GITHUB_CLIENT_SECRET=8555666JJHFHJDHHSHVZHHCJXJBV
BETTER_AUTH_SECRET=SBDVHBDHBVSBDBIGFEUFDBJBBVV8H
BETTER_AUTH_URL=http://localhost:3000
PORT=3000
```

Run migrations:

```bash
npx prisma migrate deploy
```

Build and start the HTTP server:

```bash
npm run build
npm start
```

The HTTP API will start on the port configured in `src/index.ts` (default 3006).

---

## CLI Usage

### Build the CLI

```bash
cd server
npm run build
```

### Quick Start

Follow these steps to authenticate and start using the CLI:

**1. Start the Backend & Frontend**

In separate terminals:

```bash
# Terminal 1: Start the backend server
cd server
npm start

# Terminal 2: Start the frontend client
cd client
npm run dev
```

The frontend will typically run on `http://localhost:3000`.

**2. Sign Up via GitHub**

- Open the frontend URL in your browser
- Sign up or authenticate using your GitHub account

**3. Device Code Authentication (CLI Login)**

In a new terminal:

```bash
cd server
forgemate login
```

The command will display:
- A device authentication URL
- A **6-character code** (digits + letters)

Copy the code and:
1. Go back to the frontend URL in your browser
2. Paste the code into the device authentication box
3. Confirm the authentication

**4. Wait for Confirmation**

After authentication succeeds, you'll see a login confirmation message in your terminal.

**5. Wake Up the Agent**

```bash
forgemate wakeup
```

This will display **3 available modes**:

- **Chat**: regular AI conversation in the terminal
- **Tool**: chat with tool calling (e.g., web search, real-time news, etc.)
- **Agent**: application generator mode (scaffold full projects from descriptions)

Select your desired mode and start interacting!

---

## Agent Mode (Application Generator)

The agent mode uses `generateObject` from the `ai` SDK with a Zod schema (`ApplicationSchema`) defined in `server/src/config/agent.config.ts`.

You describe the application you want (for example, “Generate a React + Tailwind todo app”), and the agent:

- Proposes a folder name.
- Generates all required files with content.
- Writes them into your current working directory.
- Prints suggested setup commands (e.g. `npm install`, `npm run dev`).

Generated files are written under the directory returned in the CLI output. Review the files before committing them to version control.

---

## Web Client Setup (`client/`)

From the `client` directory:

```bash
cd client
npm install
npm run dev
```

By default, the client expects the server to run on `http://localhost:3005`. Adjust the client environment configuration if you change the server URL.

---

## Screenshots

### CLI Demo Video
https://youtu.be/TD_sWbtU1lE

### CLI Login
![CLI Login](github/screenshot/cli-login.png)

### CLI Options
![CLI Options](github/screenshot/cli-options.png)

### Device Authorization Flow
![Device Authorization](github/screenshot/Device-auth.png)

---

## Development Notes

- The AI integration uses the `ai` SDK with the `@ai-sdk/openai` provider. The provider is explicitly configured with your `OPENAI_API_KEY` and the OpenAI base URL to avoid going through the AI Gateway.
- Zod schemas are used to define structured outputs (for example, the application generator’s file list and metadata).
- Prisma is used for persistence (users, sessions, conversations, messages).

For deeper details, inspect:

- `server/src/cli/ai/openai-service.ts` for AI calls.
- `server/src/config/agent.config.ts` for the application generator configuration.
- `server/src/service/chat-service.ts` for chat message handling.

