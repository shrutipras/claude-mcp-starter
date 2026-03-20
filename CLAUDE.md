# MCP Integrations

This project configures Claude Code with MCP servers for Google Workspace, GitHub, Canva, and browser automation via Playwright.

---

## Playwright Browser (`playwright`)

**Package:** `@playwright/mcp`
**Auth:** None required
**Use for:** Letting Claude control a browser — navigate pages, click buttons, fill forms, take screenshots

### SECURITY RULES — MANDATORY

Playwright has full browser control. Anything visible on screen passes through the conversation context, and we cannot guarantee how data is handled on the backend. The following rules are **non-negotiable**:

**NEVER use Playwright for:**
- Email (Gmail, Outlook, etc.) — no reading, sending, or viewing
- Banking, financial, or payment sites
- Credential/admin pages (OAuth secrets, API keys, tokens, passwords)
- Any page where personal, private, or confidential information is visible
- Anything medium-to-high sensitivity

**Playwright is ONLY allowed for:**
- Public websites and documentation
- Local development servers (localhost)
- Non-sensitive, non-confidential content (e.g. a public Google Doc)
- UI testing and screenshots of non-sensitive pages

**If a task involves sensitive data, instruct the user to do it manually.** This is a hard rule. When in doubt, refuse and suggest the manual approach.

**Before every Playwright action**, describe what you will navigate to and click so the user can review and approve.

### What Claude can do
- Navigate to non-sensitive URLs
- Click, type, scroll on non-sensitive webpages
- Take screenshots of non-sensitive pages
- Fill out and submit forms on non-sensitive pages

### How to use
- "Go to the Tailwind docs and find the flex layout examples"
- "Screenshot my localhost:3000 app"
- "Fill out this public form at [url]"

---

## Google Workspace (`google-workspace`)

**Package:** [`workspace-mcp`](https://github.com/taylorwilsdon/google_workspace_mcp) by Taylor Wilsdon
**Auth:** Browser OAuth via Google Cloud Console credentials
**Covers:** Gmail, Drive, Docs, Sheets, Slides, Calendar, Tasks, Contacts

### SECURITY RULES — MANDATORY

**Always ask for explicit user confirmation before:**
- Sending, replying to, or forwarding any email
- Deleting any email, file, or Drive item
- Sharing or changing permissions on any Doc, Sheet, Slide, or Drive file
- Creating or modifying calendar events that invite other people
- Uploading files to Drive

**NEVER:**
- Bulk delete emails, files, or Drive items
- Share documents publicly or change visibility without explicit permission
- Send emails to recipients the user did not specify
- Access or display sensitive email content unnecessarily

Draft actions (creating a doc, drafting an email) are fine without confirmation. Any action that **sends, deletes, or shares** requires the user to say yes first.

### What Claude can do
- Read and send emails, search Gmail
- Create, read, edit, and share Google Docs / Sheets / Slides
- List, upload, download, and organize Google Drive files
- Read and create calendar events

### How to use
Just ask Claude naturally:
- "Draft a reply to my last email from Sarah and send it"
- "Create a Google Sheet with these columns: Name, Date, Amount"
- "Find my Q1 report in Drive and summarize it"
- "Schedule a 1-hour meeting tomorrow at 2pm called Team Sync"

### Required setup
See **Google Cloud Console Setup** in `README.md`.

---

## GitHub (`github`)

**Endpoint:** `https://api.githubcopilot.com/mcp/` (official GitHub remote server)
**Auth:** Browser OAuth via GitHub Copilot
**Requires:** GitHub Copilot subscription

### SECURITY RULES — MANDATORY

**Always ask for explicit user confirmation before:**
- Pushing code or force-pushing to any branch
- Creating, closing, or merging pull requests
- Creating, closing, or commenting on issues (these are public-facing)
- Deleting branches or repositories
- Creating forks or public gists
- Changing repository visibility or settings

**NEVER:**
- Push to `main`/`master` without explicit permission
- Force-push to any shared branch
- Delete repositories or branches without confirmation
- Make private repos public
- Create public gists containing project code without permission

Reading repos, branches, code, and commit history is fine without confirmation. Any action that **modifies, publishes, or deletes** requires the user to say yes first.

### What Claude can do
- Read, create, and update issues and pull requests
- Search code, files, and repositories
- Read repository contents, branches, and commit history
- Create and manage branches, forks, gists

### How to use
- "Open a GitHub issue in my repo called 'Fix login bug' with these steps to reproduce"
- "Show me all open PRs in my-org/my-repo"
- "Search my repos for any file that imports `lodash`"
- "Create a new branch called `feature/auth` from main"

---

## Canva (`canva`)

**Endpoint:** `https://mcp.canva.com/mcp` (official Canva remote server)
**Auth:** Browser OAuth via Canva account
**No API key required**

### What Claude can do
- Search your existing Canva designs
- Create new designs from templates
- Autofill templates with your data
- Export designs as PDF or images

### How to use
- "Find my Instagram post designs in Canva"
- "Create a new presentation using the 'Minimal' template"
- "Export my latest Canva design as a PDF"

---

## General Security Rules — ALL MCP Servers

These rules apply across every MCP integration:

**Credential hygiene:**
- NEVER log, echo, print, or write secrets, tokens, API keys, or passwords to any file that isn't in `.gitignore`
- NEVER commit `.env`, `.env.local`, `credentials.json`, `token.json`, or any file containing secrets
- If you encounter a secret in output or a file, do NOT repeat it back — warn the user instead

**Destructive actions:**
- NEVER bulk delete anything (files, emails, repos, designs) without explicit confirmation
- NEVER auto-share or change visibility/permissions on any resource without explicit permission
- NEVER perform irreversible actions without confirming with the user first

**When in doubt, ask.** It is always better to pause and confirm than to take an action that cannot be undone.

---

## Notes

- On first use of each server, Claude Code will open a browser window for OAuth login. This is expected.
- Google Workspace tokens are cached locally after first login. Other servers handle sessions remotely.
- To reset auth for any server, see the troubleshooting section in `README.md`.
