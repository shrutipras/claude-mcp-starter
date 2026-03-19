# Claude MCP Starter

A ready-to-use Claude Code configuration with MCP integrations for Google Workspace (Gmail, Drive, Docs, Sheets, Slides, Calendar), GitHub, and Canva — all using OAuth browser-based authentication. No API keys embedded.

---

## Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) installed (`npm install -g @anthropic-ai/claude-code`)
- [uv](https://docs.astral.sh/uv/getting-started/installation/) installed (for running `workspace-mcp`)
- A Google account
- A GitHub account with [GitHub Copilot](https://github.com/features/copilot) subscription
- A [Canva](https://www.canva.com) account

---

## Quick Start

```bash
git clone https://github.com/YOUR_USERNAME/claude-mcp-starter.git
cd claude-mcp-starter
cp .env.example .env.local
# Edit .env.local with your Google OAuth credentials (see below)
source .env.local
claude
```

On first run, Claude Code will open browser windows for each service's OAuth login.

---

## Google Workspace Setup (Required Manual Steps)

`workspace-mcp` uses OAuth and needs a Google Cloud OAuth client you create yourself. This is a one-time setup.

### Step 1: Create a Google Cloud Project

1. Go to [console.cloud.google.com](https://console.cloud.google.com)
2. Click the project dropdown → **New Project**
3. Name it anything (e.g., `claude-mcp`) and click **Create**
4. Make sure your new project is selected in the dropdown

### Step 2: Enable Required APIs

1. In the left sidebar, go to **APIs & Services → Library**
2. Search for and enable each of these APIs:
   - Gmail API
   - Google Drive API
   - Google Docs API
   - Google Sheets API
   - Google Slides API
   - Google Calendar API
   - People API (for Contacts)
   - Tasks API

### Step 3: Configure OAuth Consent Screen

1. Go to **APIs & Services → OAuth consent screen**
2. Select **External** and click **Create**
3. Fill in required fields:
   - App name: `Claude MCP` (or anything)
   - User support email: your email
   - Developer contact email: your email
4. Click **Save and Continue** through the remaining screens
5. On the **Test users** screen, click **Add users** and add your Google account email
6. Click **Save and Continue**, then **Back to Dashboard**

### Step 4: Create OAuth Credentials

1. Go to **APIs & Services → Credentials**
2. Click **Create Credentials → OAuth client ID**
3. Application type: **Desktop app**
4. Name: `Claude MCP Local` (or anything)
5. Click **Create**
6. Copy the **Client ID** and **Client Secret** shown

### Step 5: Add to Your Environment

Edit your `.env.local` file:

```bash
GOOGLE_OAUTH_CLIENT_ID=your-client-id.apps.googleusercontent.com
GOOGLE_OAUTH_CLIENT_SECRET=your-client-secret
```

Then source it before launching Claude Code:

```bash
source .env.local
claude
```

> **First launch:** A browser window will open for Google sign-in. Log in with the same account you added as a test user. Tokens are saved locally and you won't need to log in again.

---

## GitHub Setup

If you have a **GitHub Copilot subscription**, no setup needed. Claude Code handles OAuth automatically when you first use a GitHub-related command.

### Alternative: Personal Access Token (no Copilot required)

If you don't have Copilot, update `.mcp.json` to use the Docker-based server:

```json
"github": {
  "command": "docker",
  "args": ["run", "-i", "--rm", "-e", "GITHUB_PERSONAL_ACCESS_TOKEN", "ghcr.io/github/github-mcp-server"],
  "env": {
    "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_PERSONAL_ACCESS_TOKEN}"
  }
}
```

Then add your PAT to `.env.local`:

```bash
GITHUB_PERSONAL_ACCESS_TOKEN=ghp_your_token_here
```

Create a PAT at [github.com/settings/tokens](https://github.com/settings/tokens) with scopes: `repo`, `read:org`, `read:packages`.

---

## Canva Setup

No setup required. When you first ask Claude to do something with Canva, a browser window opens for OAuth login with your Canva account.

---

## Resetting Auth

| Service | How to reset |
|---|---|
| Google Workspace | Delete `~/.cache/workspace-mcp/` (or equivalent token cache) and re-run |
| GitHub (Copilot) | Revoke access at github.com/settings/applications |
| Canva | Revoke access at canva.com/settings/connected-apps |

---

## Project Structure

```
.
├── .mcp.json          # MCP server configuration for Claude Code
├── .env.example       # Template for required environment variables
├── .env.local         # Your credentials (gitignored, never committed)
├── .gitignore         # Excludes tokens and secrets
├── CLAUDE.md          # How to use each MCP integration with Claude
└── README.md          # This file
```

---

## Troubleshooting

**`uvx: command not found`**
Install uv: `curl -LsSf https://astral.sh/uv/install.sh | sh`

**Google auth fails with "redirect_uri_mismatch"**
Make sure you created a **Desktop app** credential type, not a Web application.

**"This app isn't verified" warning from Google**
Click **Advanced → Go to [app name] (unsafe)**. This is expected for apps in testing mode.

**MCP server doesn't connect**
Run `claude mcp list` to see server status, or `claude --debug` for detailed logs.
