# n8n-actions

Shared GitHub Actions for deploying n8n workflows from any repo.

## `deploy-n8n`

Syncs `workflows/*.json` files to an n8n instance. Tracks workflow IDs in `manifest.json` — creates on first deploy, updates on subsequent pushes.

### Usage

```yaml
# .github/workflows/deploy.yml
name: Deploy n8n Workflows

on:
  push:
    branches: [main]
    paths:
      - 'workflows/**'

permissions:
  contents: write  # required to commit manifest.json back

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          token: ${{ secrets.GITHUB_TOKEN }}

      - uses: hhqwerty/n8n-actions/.github/actions/deploy-n8n@main
        with:
          n8n-api-url: ${{ secrets.N8N_API_URL }}
          n8n-api-key: ${{ secrets.N8N_API_KEY }}
```

### GitHub Secrets required

| Secret | Value |
|--------|-------|
| `N8N_API_URL` | Your n8n base URL, e.g. `https://your-n8n.com` |
| `N8N_API_KEY` | From n8n → Settings → API → Create API key |

### How it works

1. Reads `manifest.json` for known workflow IDs
2. For each `workflows/*.json`:
   - If ID in manifest → `PUT` (update existing workflow)
   - If no ID → `POST` (create new) → saves returned ID to manifest
3. Commits updated `manifest.json` back to the repo

### Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `n8n-api-url` | ✅ | — | n8n base URL |
| `n8n-api-key` | ✅ | — | n8n API key |
| `workflows-dir` | ❌ | `workflows` | Folder containing JSON files |
