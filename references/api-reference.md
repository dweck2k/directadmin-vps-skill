# DirectAdmin API Reference

Full endpoint list. Authentication: HTTP Basic Auth. SSL: self-signed (use `-k`).
Full OpenAPI spec available at: `BASE_URL/static/swagger.json`

## Admin
| Method | Path | Description |
|--------|------|-------------|
| GET | `/api/admin-usage` | Admin usage statistics |

## Password
| Method | Path | Description |
|--------|------|-------------|
| POST | `/api/change-password` | Change Unix/mail/FTP credentials |

## User Migration
| Method | Path | Description |
|--------|------|-------------|
| POST | `/api/change-user-creator` | Transfer user between resellers |
| POST | `/api/convert-reseller-to-user` | Downgrade reseller to user |
| POST | `/api/convert-user-to-reseller` | Upgrade user to reseller |

## ClamAV
| Method | Path | Description |
|--------|------|-------------|
| GET | `/api/clamav` | List active scans |
| POST | `/api/clamav` | Start scan (`{"directory": "/path"}`) |
| DELETE | `/api/clamav/{pid}` | Stop scan by PID |

## cPanel Import
| Method | Path | Description |
|--------|------|-------------|
| POST | `/api/cpanel-import/check-remote` | Check SSH + get remote user list |
| GET | `/api/cpanel-import/tasks` | List all import tasks |
| POST | `/api/cpanel-import/tasks/start` | Begin remote account migration |
| GET | `/api/cpanel-import/tasks/{id}` | Get single task details |
| DELETE | `/api/cpanel-import/tasks/{id}` | Remove pending task |
| GET | `/api/cpanel-import/tasks/{id}/log` | Get task log |
| GET | `/api/cpanel-import/tasks/{id}/log-sse` | Stream task log (SSE) |

## CustomBuild
| Method | Path | Description |
|--------|------|-------------|
| GET | `/api/custombuild/actions` | List available build actions |
| GET | `/api/custombuild/compile-scripts` | All app compile script metadata |
| GET | `/api/custombuild/compile-scripts/{app}` | Compile script for specific app |
| GET | `/api/custombuild/compile-scripts-custom/{app}` | Custom compile script |
| PUT | `/api/custombuild/compile-scripts-custom/{app}` | Set custom compile script |
| DELETE | `/api/custombuild/compile-scripts-custom/{app}` | Remove custom compile script |
| POST | `/api/custombuild/kill` | Stop active build |
| GET | `/api/custombuild/logs` | List log file metadata |
| DELETE | `/api/custombuild/logs/{logname}` | Delete a log file |
| GET | `/api/custombuild/logs/{logname}/sse` | Stream log (SSE) |
| GET | `/api/custombuild/options` | Get build configuration |
| PATCH | `/api/custombuild/options` | Update build config |
| GET | `/api/custombuild/options-v2` | Build config (v2) |
| PATCH | `/api/custombuild/options-v2` | Update build config (v2) |
| GET | `/api/custombuild/options/validate` | Validate config changes |
| GET | `/api/custombuild/removals` | List removal commands |
| POST | `/api/custombuild/run` | Execute build (`{"action":"update","software":"php"}`) |
| GET | `/api/custombuild/software` | List installed software |
| GET | `/api/custombuild/state` | Current build state / progress |
| GET | `/api/custombuild/state/sse` | Stream build state (SSE) |
| GET | `/api/custombuild/updates` | Available software updates |
| GET | `/api/custombuild/versions` | Default app versions |
| GET | `/api/custombuild/versions-custom` | Custom app versions |
| PUT | `/api/custombuild/versions-custom/{app}` | Set custom version |
| DELETE | `/api/custombuild/versions-custom/{app}` | Remove custom version |

## Database Management
| Method | Path | Description |
|--------|------|-------------|
| POST | `/api/db-manage/clone-db` | Clone a database |
| POST | `/api/db-manage/clone-dbuser` | Clone a DB user |
| POST | `/api/db-manage/create-db` | Create empty database |
| POST | `/api/db-manage/create-db-with-user` | Create database + user |
| POST | `/api/db-manage/create-user` | Create database user |
| DELETE | `/api/db-manage/databases/{database}` | Delete a database |
| POST | `/api/db-manage/databases/{database}/check` | Check database integrity |
| GET | `/api/db-manage/databases/{database}/export` | Download database backup (SQL) |
| GET | `/api/db-manage/databases/{database}/export-definition` | Export structure + users |
| POST | `/api/db-manage/databases/{database}/fix-definers` | Repair broken ownership |
| POST | `/api/db-manage/databases/{database}/import` | Import from backup file |
| POST | `/api/db-manage/databases/{database}/import-definition` | Import structure + users |
| POST | `/api/db-manage/databases/{database}/optimize` | Optimize (defragment) tables |
| POST | `/api/db-manage/databases/{database}/repair` | Repair corrupted tables |
| DELETE | `/api/db-manage/users/{dbuser}` | Delete a DB user |
| POST | `/api/db-manage/users/{dbuser}/change-hosts` | Modify host access restrictions |

## Legacy CMD_API Endpoints

Append `?json=yes` to get JSON responses:

| Path | Description |
|------|-------------|
| `/CMD_API_SHOW_DOMAINS` | List all domains |
| `/CMD_API_SHOW_USER_CONFIG` | User configuration |
| `/CMD_API_SHOW_USERS` | List all users |
| `/CMD_API_SHOW_RESELLERS` | List resellers |
| `/CMD_API_DNS_CONTROL` | DNS management |
| `/CMD_API_SHOW_SUBDOMAINS` | List subdomains |
| `/CMD_API_SHOW_EMAIL` | Email accounts |
| `/CMD_API_SHOW_FQUOTAS` | Disk quotas |
| `/CMD_API_SHOW_BANDWIDTH` | Bandwidth usage |

Full legacy API docs: https://docs.directadmin.com/api/
