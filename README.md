# FEMA Daily Briefing Redirect

This repository provides a permanent public URL that redirects users to the latest publicly available FEMA Daily Operations Briefing PDF.

## Permanent briefing link

**Open the latest FEMA Daily Operations Briefing:**

https://melaniemeroni-usda.github.io/fema-daily-briefing/

This URL remains the same even though FEMA publishes each daily briefing at a different PDF URL. It can therefore be used as a stable link in ArcGIS Experience Builder and other applications.

## How it works

1. A FEMA Daily Operations Briefing email arrives.
2. Microsoft Power Automate extracts the public PDF URL from the email.
3. Power Automate updates the associated SharePoint list.
4. Power Automate sends a `fema_update` repository dispatch event to GitHub.
5. The GitHub Actions workflow updates `latest.json` with the new PDF URL.
6. GitHub Pages publishes the updated files.
7. `index.html` reads `latest.json` and redirects the user to the current briefing.

```text
FEMA email
    ↓
Power Automate
    ↓
Extract PDF URL
    ↓
SharePoint list
    ↓
GitHub repository dispatch
    ↓
Update latest.json
    ↓
GitHub Pages
    ↓
Current FEMA briefing PDF
```

## Repository files

| File | Purpose |
|---|---|
| `index.html` | Reads the latest briefing URL and redirects the visitor |
| `latest.json` | Stores the current FEMA Daily Operations Briefing PDF URL |
| `.github/workflows/update-url.yml` | Receives the Power Automate dispatch event and updates `latest.json` |
| `README.md` | Documents the repository and automation workflow |

## Expected payload

Power Automate sends a repository dispatch event named:

```text
fema_update
```

The event payload uses this structure:

```json
{
  "url": "https://content.govdelivery.com/attachments/.../FEMA-Daily-Ops-Briefing.pdf"
}
```

The GitHub Actions workflow writes this value to `latest.json`.

## GitHub Pages configuration

GitHub Pages is configured with:

- **Source:** Deploy from a branch
- **Branch:** `main`
- **Folder:** `/(root)`
- **Published URL:** https://melaniemeroni-usda.github.io/fema-daily-briefing/

## Verification

After a new briefing email is processed:

1. Check the Power Automate run history and confirm all steps succeeded.
2. Open the repository's **Actions** tab and confirm the `Update FEMA URL` workflow succeeded.
3. Open `latest.json` and confirm it contains the newest FEMA PDF URL.
4. Wait for the GitHub Pages deployment to complete.
5. Test the permanent briefing link.

GitHub Pages deployment may take a few minutes after `latest.json` is updated.

## Troubleshooting

### The permanent link opens an older briefing

First open the published JSON file:

https://melaniemeroni-usda.github.io/fema-daily-briefing/latest.json

- If the published JSON contains the previous URL, check the latest GitHub Pages deployment.
- If the published JSON contains the current URL, test the permanent link in a private browser window.
- Confirm that `index.html` requests the JSON with a cache-busting timestamp:

```javascript
fetch('./latest.json?ts=' + Date.now())
```

### `latest.json` did not change

Check:

- The Power Automate flow was saved and enabled.
- The **Create a repository dispatch event** step succeeded.
- The repository owner is `melaniemeroni-usda`.
- The repository name is `fema-daily-briefing`.
- The event name is `fema_update`.
- The payload contains the output from the **Extract FEMA PDF URL** step.
- The GitHub Actions workflow exists on the `main` branch.

### The GitHub workflow succeeded, but the published JSON is old

Check the repository's **Actions** tab for the subsequent GitHub Pages build and deployment. If necessary, trigger a new deployment by recommitting a harmless change to `index.html` or resaving the Pages branch settings.

## Data and access

This repository does not store the FEMA briefing PDF. It stores only the public URL supplied through FEMA's public GovDelivery distribution.

The repository and GitHub Pages site are public. Do not use this workflow to publish private, controlled, sensitive, or non-public information.

## Maintenance

Routine operation should not require manual updates. Maintenance may be needed if:

- FEMA changes the email structure or PDF-link format.
- The Power Automate GitHub connection expires.
- The GitHub repository or account is renamed.
- The repository dispatch event name changes.
- GitHub Pages deployment settings change.

## Disclaimer

This repository provides an automated redirect to a publicly released FEMA briefing. It is not an official FEMA website and does not modify or reproduce the briefing content.
