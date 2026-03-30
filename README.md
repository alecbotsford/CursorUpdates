# Automated daily customer digest → Confluence

Runs on a schedule (GitHub Actions) with **no manual input**: pulls **Jira** (your assigned issues updated in the last ~24h) and **Slack** (portfolio keyword search), then writes to **Confluence**.

## What gets created

- **Default (`DIGEST_MODE` unset or `child`):** a new child page each day under your playbook page, titled `Daily digest — YYYY-MM-DD`.
- **Optional (`DIGEST_MODE=update`):** overwrites one existing page (set `CONFLUENCE_TARGET_PAGE_ID`) for a single “live” doc.

## One-time setup

1. Create a **private** GitHub repository and push this folder (including `.github/workflows`).
2. In the repo **Settings → Secrets and variables → Actions**, add:

| Secret | Purpose |
| --- | --- |
| `ATLASSIAN_EMAIL` | alec.botsford@humansecurity.com |
| `ATLASSIAN_API_TOKEN` | From [Atlassian API tokens](https://id.atlassian.com/manage-profile/security/api-tokens) |
| `ATLASSIAN_SITE` | `perimeterx.atlassian.net` (no `https://`) |
| `CONFLUENCE_PARENT_PAGE_ID` | Playbook page id: `6241746949` (child pages appear under it) |
| `SLACK_USER_TOKEN` | **Preferred:** user token with `search:read` (sees channels you can see) |
| `SLACK_BOT_TOKEN` | Fallback bot token with `search:read` (may miss private channels) |

Optional:

| Secret / var | Purpose |
| --- | --- |
| `JIRA_EMAIL` | If Jira basic auth must use a different email than `ATLASSIAN_EMAIL` |
| `CONFLUENCE_TARGET_PAGE_ID` | Target page id when using update mode |
| Repository variable `DIGEST_MODE` | Set to `update` to overwrite the target page instead of creating children |

3. **Actions** tab → enable workflows if prompted.
4. **Test:** Actions → **Daily customer digest → Confluence** → **Run workflow**.

## Schedule

Workflow runs at **14:00 UTC** (`0 14 * * *`), roughly **7:00 Pacific** during PDT. Adjust the cron expression in `.github/workflows/daily-digest.yml` if you want a different wall time.

## Slack search caveat

Enterprise Slack may restrict **search** for bots or require admin approval for `search:read`. If results are empty, try a **user token** (`SLACK_USER_TOKEN`) created from a Slack app installed to your workspace (follow your org’s security policy).

## Editing portfolio keywords

Edit `SLACK_QUERY_CORE` in `scripts/publish_digest.py` and push.
