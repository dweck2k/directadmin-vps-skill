---
name: directadmin-vps
description: >
  Manage a DirectAdmin VPS control panel via its REST API. Use this skill whenever the user wants to interact
  with their VPS or DirectAdmin server — tasks like checking server status, managing databases, running
  CustomBuild updates, scanning with ClamAV, managing FTP accounts, managing users, or importing cPanel
  accounts. Trigger on phrases like "my vps", "my server", "directadmin", "custombuild", "server admin",
  "manage vps", "check the server", "list databases", "run custombuild", "antivirus scan", "ftp account",
  "create ftp user", "list ftp", or any administrative action targeting a hosting server. Even when the user
  doesn't say "DirectAdmin" explicitly, if they're asking to do something on their hosting server, use this
  skill.
---

# DirectAdmin VPS Skill

You have access to the DirectAdmin REST API. Your job is to translate the user's request into one or more
API calls, execute them, and present the results clearly.

## Gathering Connection Details

At the start of each session, collect what you need before making any calls:

1. **Server URL** — ask if not provided. Format: `https://hostname:port` (typically port 2222).
   Example: `https://my-server.example.com:2222`

2. **Credentials** — always ask for these. Recommend a **login key** over the main password for security.
   The user can create one in DirectAdmin → Account Manager → Login Keys.
   Format: `username:loginkey` or `username:password`
   Admin impersonating a user: `admin|targetuser:adminpassword`

Store both for the session — don't ask again after the first time.

## Making API Calls

Use curl for all requests. Replace `BASE_URL`, `USER`, and `PASS` with the values collected above.

**GET request:**
```bash
curl -sk -u "USER:PASS" "BASE_URL/api/ENDPOINT" | python -m json.tool
```

**POST/PATCH with JSON body:**
```bash
curl -sk -u "USER:PASS" -X POST \
  -H "Content-Type: application/json" \
  -d '{"key": "value"}' \
  "BASE_URL/api/ENDPOINT" | python -m json.tool
```

**DELETE:**
```bash
curl -sk -u "USER:PASS" -X DELETE "BASE_URL/api/ENDPOINT" | python -m json.tool
```

Flags:
- `-s` = silent (no progress noise)
- `-k` = skip SSL verification (DirectAdmin typically uses a self-signed cert)
- `| python -m json.tool` = pretty-print JSON (omit for binary download responses)

To also see the HTTP status code:
```bash
curl -sk -o /dev/null -w "%{http_code}" -u "USER:PASS" "BASE_URL/api/ENDPOINT"
```

## API Endpoints Reference

See `references/api-reference.md` for the complete endpoint list. Key ones below.

### Server Status
| Intent | Method | Endpoint |
|--------|--------|----------|
| Check server / admin resource usage | GET | `/api/admin-usage` |

### CustomBuild (software compilation & updates)
| Intent | Method | Endpoint |
|--------|--------|----------|
| Check for available software updates | GET | `/api/custombuild/updates` |
| Check if a build is in progress | GET | `/api/custombuild/state` |
| List installed software | GET | `/api/custombuild/software` |
| List valid build actions | GET | `/api/custombuild/actions` |
| Run a build | POST | `/api/custombuild/run` |
| Stop an active build | POST | `/api/custombuild/kill` |
| List build logs | GET | `/api/custombuild/logs` |
| Get/change build options | GET/PATCH | `/api/custombuild/options` |

For `POST /api/custombuild/run`, always fetch `/api/custombuild/actions` first to confirm the valid
action names and software identifiers, then build the body accordingly.

### Database Management
| Intent | Method | Endpoint |
|--------|--------|----------|
| Create database | POST | `/api/db-manage/create-db` |
| Create DB + user in one step | POST | `/api/db-manage/create-db-with-user` |
| Delete database | DELETE | `/api/db-manage/databases/{database}` |
| Export/backup database (SQL dump) | GET | `/api/db-manage/databases/{database}/export` |
| Import database from file | POST | `/api/db-manage/databases/{database}/import` |
| Optimize database tables | POST | `/api/db-manage/databases/{database}/optimize` |
| Repair corrupted tables | POST | `/api/db-manage/databases/{database}/repair` |
| Check database integrity | POST | `/api/db-manage/databases/{database}/check` |
| Clone database | POST | `/api/db-manage/clone-db` |
| Create DB user | POST | `/api/db-manage/create-user` |
| Delete DB user | DELETE | `/api/db-manage/users/{dbuser}` |

### ClamAV Antivirus
| Intent | Method | Endpoint |
|--------|--------|----------|
| List active scans | GET | `/api/clamav` |
| Start a scan | POST | `/api/clamav` |
| Stop a scan | DELETE | `/api/clamav/{pid}` |

For `POST /api/clamav`, body: `{"directory": "/path/to/scan"}`

### User / Account Management
| Intent | Method | Endpoint |
|--------|--------|----------|
| Change a user's password | POST | `/api/change-password` |
| Upgrade user to reseller | POST | `/api/convert-user-to-reseller` |
| Downgrade reseller to user | POST | `/api/convert-reseller-to-user` |
| Move user to a different reseller | POST | `/api/change-user-creator` |

### cPanel Migration
| Intent | Method | Endpoint |
|--------|--------|----------|
| Check remote cPanel server SSH | POST | `/api/cpanel-import/check-remote` |
| List all import tasks | GET | `/api/cpanel-import/tasks` |
| Start a migration | POST | `/api/cpanel-import/tasks/start` |
| Get task status | GET | `/api/cpanel-import/tasks/{id}` |
| Get task log | GET | `/api/cpanel-import/tasks/{id}/log` |
| Delete pending task | DELETE | `/api/cpanel-import/tasks/{id}` |

## Workflow

1. **Collect** — get BASE_URL and credentials if not already provided in session
2. **Parse intent** — understand what the user wants
3. **Plan calls** — identify which endpoint(s) to hit, in what order
4. **Fetch metadata first** when needed (e.g., list actions before running a build, list databases before deleting one)
5. **Execute** — run the curl command(s), capture output
6. **Present results** — format clearly, highlight key info, explain what happened

## Presenting Results

- For list responses: show a summary table, not raw JSON
- For status checks: call out the most actionable fields (e.g., which software has updates, build progress %)
- For success confirmations: state clearly what was done
- For errors: show HTTP status code + API error message; suggest likely cause and fix
- For long-running operations (builds, scans): tell the user which endpoint to poll for progress

## Error Handling

| HTTP Code | Likely Cause | Action |
|-----------|-------------|--------|
| 401 | Bad credentials | Ask user to verify username/password or login key |
| 403 | Insufficient permissions | Explain what privilege level is needed |
| 404 | Resource not found | Confirm the name/ID with the user |
| 409 | Conflict (e.g., DB already exists) | Suggest alternative (rename, delete first, etc.) |
| 500 | Server error | Show the error body; may require checking server logs |

If curl itself fails (timeout, connection refused): check if the server URL and port are correct.

## Legacy CMD_API Endpoints

If a feature isn't available in the JSON API, use legacy `CMD_API_*` paths with `?json=yes`:

```bash
curl -sk -u "USER:PASS" "BASE_URL/CMD_API_SHOW_DOMAINS?json=yes" | python -m json.tool
```

Common legacy endpoints: `CMD_API_SHOW_DOMAINS`, `CMD_API_SHOW_USERS`, `CMD_API_DNS_CONTROL`,
`CMD_API_SHOW_EMAIL`, `CMD_API_SHOW_BANDWIDTH`. Full list: https://docs.directadmin.com/api/

## FTP Management

DirectAdmin FTP is managed via the legacy CMD_API (the new JSON API has no FTP endpoints).
Always append `?json=yes` to get JSON responses.

### Endpoints

| Intent | Method | Path | Key Parameters |
|--------|--------|------|----------------|
| List FTP accounts | GET | `/CMD_API_FTP` | — |
| Create FTP account | POST | `/CMD_API_FTP` | `action=create&user=NAME&passwd=PASS&passwd2=PASS&domain=DOMAIN` |
| Delete FTP account | POST | `/CMD_API_FTP` | `action=delete&user=NAME&domain=DOMAIN` |
| Change FTP password | POST | `/CMD_API_FTP` | `action=passwd&user=NAME&passwd=PASS&passwd2=PASS&domain=DOMAIN` |
| Show FTP settings | GET | `/CMD_API_FTP_SHOW` | — |

### Examples

**List FTP accounts:**
```bash
curl -sk -u "USER:PASS" "BASE_URL/CMD_API_FTP?json=yes" | python -m json.tool
```

**Create an FTP account:**
```bash
curl -sk -u "USER:PASS" -X POST \
  "BASE_URL/CMD_API_FTP?json=yes" \
  --data "action=create&user=ftpuser&passwd=FtpPass123&passwd2=FtpPass123&domain=example.com"
```

**Delete an FTP account:**
```bash
curl -sk -u "USER:PASS" -X POST \
  "BASE_URL/CMD_API_FTP?json=yes" \
  --data "action=delete&user=ftpuser&domain=example.com"
```

**Change FTP password:**
```bash
curl -sk -u "USER:PASS" -X POST \
  "BASE_URL/CMD_API_FTP?json=yes" \
  --data "action=passwd&user=ftpuser&passwd=NewPass123&passwd2=NewPass123&domain=example.com"
```

**Notes:**
- FTP usernames in DirectAdmin are typically stored as `domain_ftpuser` internally
- The `domain` parameter is required for all FTP operations
- A success response looks like: `{"error": "0", "text": "FTP account created", "details": ""}`
- An error response includes a non-zero `error` field with a description in `text`
