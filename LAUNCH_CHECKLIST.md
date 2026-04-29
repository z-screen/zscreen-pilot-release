# Z-Screen Pilot Release - Launch Checklist

End-to-end steps to publish the website at www.z-screen.com, deposit the dataset on Zenodo, and wire everything up. Estimated total active time: ~2-3 hours, plus 10-60 minutes of waiting for DNS / HTTPS provisioning.

---

## Phase 1 - Pre-flight (do these locally before touching the internet)

### 1.1 Re-run the SMILES audit on the public bundle

```powershell
cd "C:\Users\Swamy\OneDrive - Zafrens\Desktop\ZScreen_Preprint_Public"
python scripts\audit_no_smiles.py > audit_receipt.txt
```

Save `audit_receipt.txt` somewhere outside the bundle. This is your "we checked, no SMILES leaked" receipt.

### 1.2 Place the five preprint PDFs in the website folder

The current website expects these filenames in `docs/pdfs/`:

- `zscreen-paper-01-activeseq.pdf`
- `zscreen-paper-02-generative-chemistry.pdf`
- `zscreen-paper-03-generalization-ladder.pdf`
- `zscreen-paper-04-cross-cell-transfer.pdf`
- `zscreen-paper-05-causal-reasoning.pdf`
- `zscreen-pilot-release-bundle.pdf` (all 5 concatenated, for the homepage "All five preprints" link)

Copy the rendered PDFs from `ZScreen_Preprint_Public/paperN/manuscript/preprint.pdf` and rename them. For the bundled PDF, concatenate the five with a tool like `pdftk` or any PDF merger.

Place them inside this handoff folder at `project\pdfs\`. We will rename `project/` to `docs/` in the next step.

### 1.3 Rename `project/` to `docs/`

GitHub Pages serves from `/docs` on the main branch in our chosen config.

```powershell
cd "C:\Users\Swamy\Downloads\Updated Z-Screen Website-handoff\updated-z-screen-website"
ren project docs
```

After this, the structure is:

```
updated-z-screen-website/
├── README.md
├── LICENSE
├── .gitignore
├── LAUNCH_CHECKLIST.md  (this file)
└── docs/
    ├── CNAME
    ├── index.html
    ├── platform.html
    ├── paper-01-activeseq.html
    ├── ...
    ├── assets/
    └── pdfs/
        └── (six PDFs from step 1.2)
```

---

## Phase 2 - Zenodo deposit (do this before the website goes live, so the DOI exists)

### 2.1 Make the bundle zip

```powershell
cd "C:\Users\Swamy\OneDrive - Zafrens\Desktop"
Compress-Archive -Path "ZScreen_Preprint_Public" -DestinationPath "ZScreen_Preprint_Public.zip" -CompressionLevel Optimal
```

Resulting file: ~6-7 GB compressed (from 9.3 GB raw). Confirm with `Get-Item ZScreen_Preprint_Public.zip | Select-Object Length`.

### 2.2 Create the Zenodo deposit

1. Sign in at [zenodo.org](https://zenodo.org) with the Zafrens account (or your work email).
2. Click "New Upload."
3. **Files**: drag in `ZScreen_Preprint_Public.zip`.
4. **Resource type**: "Dataset."
5. **Title**: "Z-Screen pilot release: combinatorial chemistry-to-transcriptome canonical dataset and reproduction bundle (April 2026, pre-bioRxiv draft)."
6. **Authors**: `Zafrens, Inc.` (single corporate creator). Affiliation: "Zafrens, Inc., San Diego, CA." This matches the pre-bioRxiv draft posture - when the formal preprints post to bioRxiv with a final author list, you can issue a Zenodo version-2 with individual authors at that point.
7. **Description**: short summary linking to www.z-screen.com. Suggested wording: *"Pre-bioRxiv draft release. Canonical dataset and reproduction bundle for five draft preprints from the Z-Screen pilot (April 2026). 615,793 mRNA-seq profiles across 12 combinatorial libraries and 4 cell lines, paired with imaging in the same micro-well, plus per-paper analysis scripts. Posted publicly under soft launch to collect technical feedback before formal bioRxiv submission. See www.z-screen.com for layman summaries and PDFs."*
8. **License**: CC-BY 4.0.
9. **Keywords**: phenotypic screening, combinatorial chemistry, transcriptomics, Perturb-seq, drug discovery, Z-Screen, Zafrens, draft preprint.
10. **Related identifiers**: leave blank for now; add the bioRxiv DOIs later as a version-2 update when the formal preprints post.
11. **Publication date**: today's date (release date).
12. Click "Save," then "Publish."

**You cannot edit the file after publishing**, but you CAN issue versioned updates (same DOI prefix, new version suffix). The plan: version 1 today as the pre-bioRxiv draft, version 2 when the formal preprints post on bioRxiv with the final author list. Make sure the audit receipt (`audit_receipt.txt` from step 1.1) is inside the zip.

### 2.3 Capture the DOI

After publishing, the page will show a DOI like `10.5281/zenodo.XXXXXXX`. Note both:
- The DOI: `10.5281/zenodo.XXXXXXX`
- The full URL: `https://zenodo.org/records/XXXXXXX`

You will paste the URL into the website's `ZENODO_DOI_TBD` placeholders.

### 2.4 Replace `ZENODO_DOI_TBD` placeholders in the website

```powershell
cd "C:\Users\Swamy\Downloads\Updated Z-Screen Website-handoff\updated-z-screen-website\docs"
$zenodoUrl = "https://zenodo.org/records/XXXXXXX"  # paste your actual URL
Get-ChildItem *.html | ForEach-Object {
    (Get-Content $_.FullName -Raw) -replace "ZENODO_DOI_TBD", $zenodoUrl | Set-Content $_.FullName -NoNewline
}
```

Also paste the DOI into `README.md` where it says `[TBD - paste Zenodo URL after deposit]`.

---

## Phase 3 - GitHub repo

### 3.1 Pick the org and repo name

**Org**: `z-screen` (confirmed). **Repo**: `zscreen-pilot-release` (confirmed).

The repo must be **public** for free GitHub Pages with a custom domain (private Pages requires Enterprise + paid Pages addon, and we're going public anyway).

### 3.2 Create and push

```powershell
cd "C:\Users\Swamy\Downloads\Updated Z-Screen Website-handoff\updated-z-screen-website"
git init -b main
git add .
git commit -m "Initial pilot release: website, PDFs, repo skeleton"

# Create the repo on GitHub via gh CLI
gh repo create z-screen/zscreen-pilot-release --public --source . --remote origin --description "Z-Screen pilot release - website + preprint PDFs. Dataset on Zenodo."
git push -u origin main
```

If `gh` defaults to your personal `swamy71-cloud` account instead of the `z-screen` org, run `gh auth switch` first or `gh auth login` and re-authenticate against the enterprise account.

### 3.3 Enable GitHub Pages

1. Repo Settings &rarr; Pages.
2. **Source**: "Deploy from a branch."
3. **Branch**: `main`, folder `/docs`. Save.
4. Pages will report "Your site is live at https://z-screen.github.io/zscreen-pilot-release/" within ~30 seconds.
5. **Custom domain**: enter `www.z-screen.com`. Pages reads the `CNAME` file in `docs/` to confirm; expect "DNS check in progress" until step 4 is done.
6. Tick **Enforce HTTPS** once the cert provisions (10-60 min after DNS resolves).

---

## Phase 4 - DNS at the domain registrar

Wherever you registered `z-screen.com`, log into the DNS panel.

### 4.1 Apex domain (z-screen.com without www)

Add four `A` records pointing the apex to GitHub's Pages IPs:

```
A    @    185.199.108.153
A    @    185.199.109.153
A    @    185.199.110.153
A    @    185.199.111.153
```

(The `@` symbol means the apex / naked domain; some registrars use a blank field instead.)

### 4.2 www subdomain

Add one `CNAME` record:

```
CNAME    www    z-screen.github.io
```

(That's literally `z-screen.github.io` — the org name is `z-screen`.)

### 4.3 Wait

DNS propagation: usually a few minutes, occasionally up to a couple of hours. Test with:

```powershell
nslookup www.z-screen.com
```

When it returns one of the GitHub IPs above, DNS is live. Then GitHub will auto-provision the Let's Encrypt cert (another ~10-60 min).

---

## Phase 5 - Verification

### 5.1 Open in a browser

- `https://www.z-screen.com` &rarr; renders the homepage with HTTPS lock.
- Click into Platform, each of the 5 papers; spot-check one finding card on each page.
- Click "Download" on a preprint card &rarr; PDF downloads.
- Click "Open the deposit" &rarr; lands on the Zenodo page.
- Click "Email the team" &rarr; opens mail client to `z-screen@zafrens.com`.

### 5.2 Apex redirect

`https://z-screen.com` (no www) should auto-redirect to `https://www.z-screen.com`. GitHub Pages handles this automatically once DNS is set up.

### 5.3 Run a Lighthouse check

In Chrome DevTools &rarr; Lighthouse &rarr; Generate Report. Should score 95+ on Performance and Accessibility for a static site. If anything's red, fix before sharing.

### 5.4 Final SMILES audit, one more time

Re-download the Zenodo bundle to a clean directory and re-run `audit_no_smiles.py` on it. Belt-and-suspenders confirmation that what's published is what was audited.

### 5.5 Confirm noindex is live

```powershell
curl https://www.z-screen.com | Select-String "noindex"
```

Should return one match. The site is intentionally invisible to Google during the silent-launch period; the `<meta name="robots" content="noindex, nofollow">` tag on every page enforces this. Remove the tag at the formal-preprint launch (find-and-replace across `docs/*.html`).

---

## Phase 6 - Silent launch (private sharing, no public broadcast)

This is a pre-bioRxiv draft posted publicly to collect feedback. Do NOT broadcast it. The plan is targeted sharing with a hand-picked list, not a Twitter thread.

### 6.1 Build the share list

Three buckets, in priority order:

1. **Technical reviewers** - 5-10 people whose feedback you actually want before bioRxiv. Trusted academic collaborators, methodologically careful peers, potential future co-authors. Send a short personal note with the URL and a specific ask ("can you take a look at Paper 03's L3 holdout? I want to know if my benchmark framing is fair").
2. **Pharma collaborators / BD leads** - existing partners and warm BD relationships. Frame as "early look at where the platform is, before we go public" - this is high-signal access.
3. **Investors** - bridge round and Series B context. Frame as "the formal scientific case for the platform claims you've heard us make."

Suggested send vehicle: 1:1 emails. Each one ~3 sentences max, with the URL. NOT a mass mail-merge.

### 6.2 What NOT to do during silent launch

- No tweets / LinkedIn posts / Hacker News.
- No press release.
- Do not submit to aggregators (BiorXiv-equivalent indexes, paper alert services).
- Do not enable Google indexing (the noindex meta tag stays).
- Do not issue Kaggle / Hugging Face mirrors yet - those amplify discoverability and don't fit the silent-launch posture.

### 6.3 Track incoming feedback

Set up a simple log (Notion page, markdown file, or shared doc) keyed by reviewer. For each: date sent, date responded, key feedback points, follow-up needed. This becomes the revision punch-list for the bioRxiv-ready versions.

---

## Phase 7 - Formal preprint launch (later, when manuscripts are revised)

When you are ready to flip from silent draft to formal preprint:

1. Incorporate revision feedback into the manuscripts. Re-render PDFs.
2. Update website copy to drop "draft" framing; update README, Zenodo description.
3. Remove `<meta name="robots" content="noindex, nofollow">` from all `docs/*.html`.
4. Issue Zenodo version 2 with the final author list (individual names, Zafrens affiliation) and the bioRxiv DOIs in "Related identifiers."
5. Submit to bioRxiv. Reference the Zenodo DOI in the data-availability section of each preprint.
6. THEN do the amplification moves: Kaggle dataset + hero notebooks, Hugging Face Datasets mirror, Twitter/LinkedIn threads, targeted outreach to a wider audience.

The Kaggle competition angle for Paper 03's L1-L5 ladder is the single highest-leverage post-launch move - hold it for after bioRxiv, not now.

---

## Quick-reference URLs to capture as you go

- Zenodo deposit URL: ____________________________________________
- Zenodo DOI: ____________________________________________
- GitHub repo URL: https://github.com/z-screen/zscreen-pilot-release
- GitHub Pages auto-URL: https://z-screen.github.io/zscreen-pilot-release
- Live site URL: https://www.z-screen.com
- Share-list tracker (notion / md file / etc): ____________________________________________
