# directadmin-vps

A Claude Code skill for managing a [DirectAdmin](https://directadmin.com/) VPS control panel via its REST API.

## Features

| Category | What you can do |
|----------|----------------|
| **Server Status** | Check CPU, memory, and disk usage via admin resource stats |
| **CustomBuild** | Check for software updates, view build state, list installed software, run or stop builds, tail build logs, get/set build options |
| **Database Management** | Create databases and users, export/import SQL dumps, optimize, repair, clone, check integrity, delete |
| **ClamAV Antivirus** | Start directory scans, list running scans, stop a scan |
| **FTP Management** | List FTP accounts, create/delete accounts, change passwords |
| **User & Account Management** | Change passwords, convert users to resellers (and back), move users between resellers |
| **cPanel Migration** | Check remote SSH connectivity, create and monitor migration tasks, stream migration logs |

## Installation

1. Copy the `SKILL.md` and `evals/` folder into your Claude skills directory:
   ```
   %APPDATA%\Claude\local-agent-mode-sessions\skills-plugin\<session-id>\<instance-id>\skills\directadmin-vps\
   ```
2. Register it in `manifest.json` alongside the other skill entries:
   ```json
   {
     "skillId": "directadmin-vps",
     "name": "directadmin-vps",
     "description": "...",
     "creatorType": "user",
     "updatedAt": "2026-03-19T00:00:00.000000Z",
     "enabled": true
   }
   ```
3. Restart Claude Code (or reload the skill plugin).

## Usage

Just describe what you want in plain English. The skill triggers automatically when you mention your VPS, server, or any of the features above.

**Examples:**

```
Check if there are any software updates on my server
```
```
Create a MySQL database called wp_mysite on my VPS
```
```
List all FTP accounts on example.com
```
```
Create an FTP account ftpuser for example.com with password MyPass123
```
```
Start a ClamAV scan on /home
```
```
Run a custombuild update for PHP 8.3
```
```
Is there a build currently running?
```

On first use in a session the skill will ask for:
- **Server URL** — e.g. `https://my-server.example.com:2222`
- **Credentials** — `username:password` or `username:loginkey` (login keys are recommended)

Admin impersonating a user: `admin|targetuser:adminpassword`

## API Coverage

The skill uses DirectAdmin's **JSON REST API** (`/api/...`) where available and falls back to the **legacy CMD_API** (`/CMD_API_*?json=yes`) for features not yet in the new API (currently: FTP, domains, email, DNS, bandwidth).

Full API documentation: https://docs.directadmin.com/api/

## Files

```
SKILL.md          — skill instructions loaded by Claude
evals/            — evaluation test cases
references/       — API reference notes
README.md         — this file
```
