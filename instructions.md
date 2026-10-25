# Alpha site: multi-page upgrade, install and deploy

Project folder: `C:\Users\lenno\Documents\Invitt_Client_Websites\alpha-site`
Live domain: `alfasounds.co.zw` (set in the `CNAME` file)
GitHub repo: `https://github.com/lennexpo/alpha-sounds`

## 1. Download

Download `alpha-site-upgrade.zip` from Claude. It normally lands in `C:\Users\lenno\Downloads`.
If the name is `alpha-site-upgrade (1).zip`, change `$zip` below to match.

The zip contains code files only. It does not contain the `images` folder, `CNAME` or the Google verification file, so those stay exactly as they are.

## 2. Install (PowerShell)

This backs up the files that will be replaced, then extracts the upgrade into the project folder.

```powershell
$site = Join-Path ([Environment]::GetFolderPath('MyDocuments')) "Invitt_Client_Websites\alpha-site"
$zip  = "$env:USERPROFILE\Downloads\alpha-site-upgrade.zip"

if (-not (Test-Path $zip))  { Write-Error "Zip not found: $zip"; return }
if (-not (Test-Path $site)) { Write-Error "Folder not found: $site"; return }

$backup = Join-Path (Split-Path $site) "alpha-site-backup-$(Get-Date -Format 'yyyyMMdd-HHmm')"
New-Item -ItemType Directory -Path $backup | Out-Null
Get-ChildItem $site -File | Where-Object { $_.Extension -in '.html','.css','.js','.xml','.txt','.md' } | Copy-Item -Destination $backup

Expand-Archive -Path $zip -DestinationPath $site -Force

# Each page is now standalone, so remove the old shared files if an earlier install left them behind
Remove-Item "$site\styles.css","$site\main.js" -ErrorAction SilentlyContinue

Get-ChildItem $site
Start-Process "$site\index.html"
```

Expected files afterwards: `images`, `CNAME`, `google26b7603aa9be1a65.html`, `index.html`, `services.html`, `work.html`, `about.html`, `contact.html`, `404.html`, `robots.txt`, `sitemap.xml`, `instructions.md`. There is no `styles.css` or `main.js`.

## 3. Preview over a local server (optional)

```powershell
cd $site
python -m http.server 8080
```

Open `http://localhost:8080`. Stop with `Ctrl+C`.

## 4. Push to GitHub

```powershell
cd $site
git status
git add .
git commit -m "Multi-page site upgrade"
git push
```

If the push is rejected because GitHub has newer commits:

```powershell
git pull --rebase
git push
```

## 5. After the push

- Wait a minute or two for GitHub Pages to deploy, then open each page on `https://alfasounds.co.zw`: home, services.html, work.html, about.html, contact.html.
- In Google Search Console, open URL inspection for the homepage and click Request indexing.
- Sitemaps: `sitemap.xml` is already submitted. Google re-reads it on its own and will pick up the 5 pages. You can resubmit it to speed that up.
- Update the website field on the Google Business Profile to `https://alfasounds.co.zw`.

## 6. What changed

- One page became five: Home, Services, Work, About, Contact (with quote form, FAQ and service area)
- Clean corporate light design (ink #14161A and gold #F2C14E), Roboto font, favicon files in the site root. The home hero has no Call or WhatsApp buttons, only the quote link; the nav Call now button and the CTA band stay. Every page is a standalone file with its own CSS and JavaScript built in (no `styles.css` or `main.js`). Only the `images` folder and Google Fonts are loaded separately.
- Home hero keeps the full-width mixing desk photo background with the animated level meter
- Phone number is the main call to action everywhere, with WhatsApp and quote request as backups
- Quote form builds a WhatsApp message to +263 776 229 138 (nothing is stored)
- Each service links to the quote form with the service pre-selected
- FAQ with matching structured data for Google
- Gallery photo viewer with next, previous and Esc to close
- Mobile menu and sticky Call and WhatsApp bar on phones
- 404 page, absolute social preview image, canonical links on every page
- Sitemap lists all 5 pages

## 7. Confirm with the client

- Service area: the pages say Alpha is based in Harare and to call for other areas. Correct this if it is wrong.
- FAQ answers: "bring the equipment or only mix", "how early to book" and "which areas" are written in general terms. Make them specific if Alpha has set policies.
- The WhatsApp number +263 776 229 138 must be registered on WhatsApp.

## 8. Rollback

Copy the files from `alpha-site-backup-<timestamp>` back into `alpha-site`, delete the new pages (`services.html`, `work.html`, `about.html`, `contact.html`, `404.html`), then commit and push.

Or undo the commit on GitHub:

```powershell
cd $site
git revert HEAD
git push
```
