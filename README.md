# Starborn Acadamy

Exported AI Dungeon adventures/scenarios for the **Dungeon Extension v2 Resurrected** browser
extension. Add this repo under the extension's **Settings, Scenarios, GitHub Repos**, then import a
file from the adventure picker's **Import, From GitHub** tab.

## Large files must use Git LFS (over 50 MB)

Any adventure/scenario `.json` export larger than **50 MB** must be committed with **Git LFS**
(https://git-lfs.com/), not as a plain file.

Why:

- GitHub warns on any file over 50 MB and **hard-rejects any push containing a file over 100 MB**.
- These exports embed their icons/portraits as base64, so a single adventure grows past 50 MB fast
  (the one in this repo is ~70 MB).
- LFS keeps the repo clone small and lets the big file be pushed at all.

### 1. One-time setup

Install Git LFS from https://git-lfs.com/, then run once per machine/account:

```bash
git lfs install
```

### 2. Track the large export before committing it

LFS matches by file path/pattern, not by size, so add each big export to tracking (or use a pattern
for your large files). Keep small files (split story-card JSON, etc.) out of LFS to save bandwidth.

```bash
git lfs track "Your_Big_Adventure.json"
git add .gitattributes
```

This writes a line to `.gitattributes` like:

```
Your_Big_Adventure.json filter=lfs diff=lfs merge=lfs -text
```

### 3. Commit and push as usual

```bash
git add Your_Big_Adventure.json
git commit -m "Add Your Big Adventure"
git push origin main
```

### Already committed a large file without LFS?

If a big file is already in the history as a normal blob (or your push was rejected), convert it,
then force-push the rewritten history:

```bash
git lfs migrate import --include="Your_Big_Adventure.json"
git push --force origin main
```

See `git lfs migrate` for more options.

## Notes

- `raw.githubusercontent.com` serves LFS-tracked files in full, so LFS scenarios import normally in
  the extension; no special handling is needed on the reader's side.
- Git LFS the tool has no quota of its own; any limit is set by the host. On GitHub, Free and Pro
  accounts include 10 GiB of LFS storage and 10 GiB of download bandwidth per month (Team and
  Enterprise get 250 GiB), and usage past that is billed per GiB. Every download counts toward that
  bandwidth, including each import through `raw.githubusercontent.com`, so a very large, very popular
  file adds up. Trim the file where you can (for example, reference remote image URLs instead of
  embedding base64) before reaching for LFS. See
  https://docs.github.com/billing/managing-billing-for-git-large-file-storage/about-billing-for-git-large-file-storage

## Currently LFS-tracked

- `Starborn_Acadamy_adventure.json` (~70 MB)