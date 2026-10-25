# Alpha site: install and deploy instructions

Project folder: `C:\Users\lenno\Documents\Invitt_Client_Websites\alpha-site`
Live domain: `alfasounds.co.zw` (set in the `CNAME` file)

All commands below use `$site` for the project folder:

```powershell
$site = Join-Path ([Environment]::GetFolderPath('MyDocuments')) "Invitt_Client_Websites\alpha-site"
```

## 1. Download

Download `alpha-site.zip` from Claude. It normally lands in `C:\Users\lenno\Downloads`.
If the name is `alpha-site (1).zip`, change `$zip` below to match.

## 2. Install the upgraded site (PowerShell)

This backs up the current `alpha-site` folder (renamed with a timestamp), extracts the upgraded site in its place, and opens it in your browser.

```powershell
$parent = Join-Path ([Environment]::GetFolderPath('MyDocuments')) "Invitt_Client_Websites"
$zip    = "$env:USERPROFILE\Downloads\alpha-site.zip"

if (-not (Test-Path $zip)) { Write-Error "Zip not found: $zip"; return }

if (Test-Path "$parent\alpha-site") {
    Rename-Item "$parent\alpha-site" "alpha-site-backup-$(Get-Date -Format 'yyyyMMdd-HHmm')"
}

Expand-Archive -Path $zip -DestinationPath $parent -Force
Start-Process "$parent\alpha-site\index.html"
```

Note: the backup step moves `CNAME` and `google26b7603aa9be1a65.html` into the backup folder. After installing, copy them back from the backup into the new `alpha-site` folder (or skip the backup step if you only want to add files).

## 3. Add robots.txt, sitemap.xml and the updated instructions

Move the three downloaded files into the project folder (this replaces the old `instructions.md`):

```powershell
$site = Join-Path ([Environment]::GetFolderPath('MyDocuments')) "Invitt_Client_Websites\alpha-site"
Move-Item "$env:USERPROFILE\Downloads\robots.txt","$env:USERPROFILE\Downloads\sitemap.xml","$env:USERPROFILE\Downloads\instructions.md" -Destination $site -Force
Get-ChildItem $site
```

If the downloads are named `robots (1).txt`, `instructions (1).md` or similar, change the names to match.

The folder should now contain: `images`, `CNAME`, `index.html`, `instructions.md`, `google26b7603aa9be1a65.html`, `robots.txt`, `sitemap.xml`.

## 4. Preview over a local server (optional)

Needed only if you want to test exactly as it behaves online.

```powershell
cd $site
python -m http.server 8080
```

Open `http://localhost:8080`. Stop with `Ctrl+C`.

## 5. Push to GitHub

```powershell
cd $site
git status
git add .
git commit -m "Add robots.txt and sitemap.xml"
git push
```

First time only, if the folder is not yet a git repo:

```powershell
cd $site
git init
git branch -M main
git remote add origin https://github.com/YOUR-USER/YOUR-REPO.git
git add .
git commit -m "Initial site"
git push -u origin main
```

Replace the `origin` URL with the real repository address.

## 6. After the push

- Open `https://alfasounds.co.zw/robots.txt` and `https://alfasounds.co.zw/sitemap.xml` and confirm both load.
- In Google Search Console, go to Sitemaps and submit `sitemap.xml`.
- The `google26b7603aa9be1a65.html` file must stay on the site so verification keeps working.

## 7. What changed in the site

- Hero uses the mixing desk photo with the animated level meter on top
- WhatsApp buttons added (hero, contact section, mobile bar)
- Sticky Call and WhatsApp bar on phones
- All photos converted from JPG to WebP (about 1 MB total, was 1.8 MB), with set dimensions to stop layout jumping
- Gallery opens photos full size in a viewer
- New "How it works" section (3 steps), nav link added
- Scroll fade-in, card hover lift (disabled for reduced-motion users)
- Favicon, social preview tags, structured business data for Google
- robots.txt and sitemap.xml added for search engines

## 8. Before going live

- Confirm +263 776 229 138 is on WhatsApp (links use `wa.me/263776229138`).
- Check the "How it works" wording matches how Alpha actually works.
- Social previews need an absolute image URL. In `index.html`, change:
  `<meta property="og:image" content="images/alpha-on-stage.webp">`
  to
  `<meta property="og:image" content="https://alfasounds.co.zw/images/alpha-on-stage.webp">`
- Keep the `images` folder next to `index.html` on the host.

## 9. Rollback

Locally: delete the new `alpha-site` folder and rename `alpha-site-backup-<timestamp>` back to `alpha-site`.

On GitHub: undo the last commit with:

```powershell
cd $site
git revert HEAD
git push
```
