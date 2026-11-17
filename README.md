# The World Gate — Windows launcher updates and announcements

This repository is for distributing **The World Gate** Windows installers, signed campaign content patches and public announcement data. The full offline site is bundled in an installer or stored in GitHub Release assets, **not committed to Git history**.

- `docs/announcements.json` — announcement feed (edit using GitHub's web editor; your GitHub login is the admin access control)
- `docs/content/manifest.json` — signed campaign-content version manifest (never edit by hand)
- `content-asset-ledger.json` — publisher's content-hash registry, contains no secrets
- `.github/workflows/build-windows.yml` — optionally builds a Windows installer on a GitHub-hosted Windows runner from a source ZIP uploaded as a **draft release asset**

**One-time Pages setup:** In **Settings → Pages**, select **Deploy from a branch**, `main` and `/docs`, then Save. Announcements will be available at <https://tero300722.github.io/The-World-Gate/announcements.json> once Pages is live. The launcher can play offline without this site.

**Do not upload:** private signing keys (`*.pem` private), administrator passwords/tokens, your source ZIP directly into Git history, `node_modules`, or installers directly into Git history. Release assets and draft releases are different from repository files.

## Announcements

Edit `docs/announcements.json` on GitHub while logged in. Only repository collaborators with write access can change notices. Each announcement must have `id`, `title`, `message`, `type` (Announcement, Session, New content, Event, Maintenance), optional `campaign`, `pinned`, and ISO UTC `publishAt`. Example (add into the `announcements` list):

```json
{
  "id": "first-notice",
  "title": "Welcome to The World Gate",
  "message": "All our adventures, together in one place.",
  "type": "Announcement",
  "campaign": "",
  "pinned": true,
  "publishAt": "2026-11-17T12:00:00.000Z",
  "expiresAt": null
}
```

The desktop launcher reads the feed in the background while online and saves notices for offline viewing. This feed has no remote-write endpoint, so friends cannot publish from the desktop application. GitHub's logged-in web editor serves as the private admin publishing interface.

## Building the Windows executable (free GitHub Actions route)

1. From the **source project ZIP provided separately**, create a **DRAFT** GitHub Release with tag `v1.3.1`, then attach `The-World-Gate-v1.3.1-COMPLETE-SOURCE.zip` **as a release asset**, not in the repository's Code tab. The source and bundled artwork become publicly downloadable once the release is published; review before publishing.
2. Under the repository's **Actions** tab run **Build The World Gate Windows Installer**, specifying that draft tag.
3. The workflow unpacks the source, downloads dependencies, runs tests, builds the Windows NSIS EXE, and attaches the EXE and `latest.yml`/blockmap to the draft release.
4. Download the EXE as the repository owner and **test it on an actual Windows 10/11 PC** with Internet and offline before pressing **Publish release**. Unsigned builds may warn in Windows SmartScreen.
5. Publishing the tested `v1.3.1` Release makes the app available to the launcher's updater. For later app updates, repeat with a *higher application version* and new release; do not overwrite past version tags.

**IMPORTANT:** This workflow and its output have not yet been executed on GitHub. The build may need debugging on a Windows runner. Never publish a failed or untested build. For free tier use, repository should be public and GitHub's current allowances should be reviewed.

## Campaign content patches

The desktop source includes `scripts/create-github-content-release.js`. Using a prepared updated offline website and the **offline private Ed25519 signing key**, it creates a new signed manifest and **only changed file assets**. Create a `content-59.0.0` GitHub **prerelease** and upload the generated `release-assets/` files, then commit the generated `manifest.json` under `docs/content/`. Content prereleases are kept separate from normal application releases to avoid confusing the Windows auto-updater.

Never hand-edit the signed manifest. Store the private signing key offline; it is not included here or in any public release. See the complete project's `docs/GITHUB-DEPLOYMENT.md` for examples and failure recovery.
