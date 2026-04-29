# cleantechhub-monitoring

Daily Buffer health check for CleantechHUB social media campaigns.

## What it does

`.github/workflows/buffer_health.yml` runs at **22:00 UTC daily** (17:00 Bogotá).

It queries Buffer's GraphQL API for each campaign channel and checks for:
- **Stuck posts** — status `scheduled` but `dueAt` is in the past (should have sent, didn't)
- **Errored posts** — status `error` with `dueAt` in the last 24h (explicitly failed)

On any failure: posts a Slack alert + exits with code 1 (red ❌ on GitHub Actions).
All clear: exits 0, no noise.

## Required repo secrets

| Secret | Description |
|--------|-------------|
| `BUFFER_ACCESS_TOKEN` | Buffer direct access token (Buffer → Settings → Apps) |
| `BUFFER_CHANNEL_IDS` | Comma-separated Buffer channel IDs for all monitored channels |
| `SLACK_WEBHOOK_URL` | Slack incoming webhook URL for failure alerts |

## Adding a new country

Append the new country's LI/IG/FB channel IDs to `BUFFER_CHANNEL_IDS`:

```bash
gh secret set BUFFER_CHANNEL_IDS \
  --repo YOUR_USER/cleantechhub-monitoring \
  --body "EXISTING_IDS,NEW_LI_ID,NEW_IG_ID,NEW_FB_ID"
```

No code changes needed — the workflow iterates over all IDs.

## Triggering manually

```bash
gh workflow run buffer_health.yml --repo YOUR_USER/cleantechhub-monitoring
gh run watch --repo YOUR_USER/cleantechhub-monitoring
```

## Current campaign channels (Colombia CLP26)

| Platform | Channel ID |
|----------|-----------|
| LinkedIn | `69d44b11031bfa423cd81103` |
| Instagram | `69d6caee031bfa423ce315e5` |
| Facebook | `69d6c7e1031bfa423ce30a91` |

---

*Part of the CleantechHUB social media infrastructure — batch architecture (VPS + Buffer).*
*See `clp26_redesign/` in the CTH Social Media workspace for the full pipeline.*
