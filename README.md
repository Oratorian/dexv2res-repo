# Dungeon Extension v2 Resurrected, Shared Scenarios

Exported AI Dungeon adventures for the **Dungeon Extension v2 Resurrected** browser extension.

## Using these in the extension

1. In the extension, open **Settings, Scenarios, GitHub Repos** and add this repo
   (`Oratorian/dexv2res-repo`, or its github.com URL).
2. Open the adventure picker, choose **Import, From GitHub**, pick this repo, and import an
   adventure.

The extension reads the adventures from this repo's **latest GitHub Release** (see below).

> Importing from a release is currently **Firefox only**: GitHub's release-asset CDN sends no CORS
> headers, which the Firefox build can use with its host permissions but the Chrome build cannot.

## How adventures are hosted here: GitHub Releases (not the git tree, not Git LFS)

Adventures are attached as **assets on a GitHub Release**, not committed as normal files and not with
Git LFS. This is deliberate:

- These exports embed every icon/portrait as base64, so they are large (the ones here are ~73 MB and
  ~166 MB). GitHub **hard-rejects any file over 100 MB** in the git tree, so the 166 MB one cannot be
  committed there at all.
- **Git LFS is metered.** Every LFS download counts against the repo owner's monthly LFS bandwidth,
  and GitHub bills overages (they can accrue whether or not a payment method is on file). A popular
  adventure downloaded by many people would burn through it fast.
- **Release assets are not metered**, allow up to **2 GB** each, and download directly. No LFS, no
  bandwidth billing, no 100 MB limit.

## Adding or updating an adventure

1. In the extension, export the adventure to a `.json` file.
2. On GitHub, create a Release (or edit the existing one) and **attach the `.json` as a release
   asset**. Do **not** commit the large `.json` into the repo tree.
3. Make sure that release is the **latest** one; the extension only reads the latest release.

That is all: the extension lists the latest release's `.json` assets and imports the one you pick.

## Important: keep adventure `.json` out of the git tree

The extension uses the release assets **only when the git tree contains no `.json` files**. If an
adventure `.json` is also committed to the tree, the extension lists that tree copy instead and
ignores the release assets. So keep large exports in Releases only.

If this repo still has `.json` exports committed (LFS or otherwise), remove them from the tree and
drop the LFS tracking, then rely on the release assets:

```bash
git rm --cached "*.json"          # stop tracking the exports (keeps your local files)
git rm .gitattributes             # drop the LFS rules if they only covered those exports
git commit -m "Host adventures as release assets instead of tree files"
git push origin main
```

Small, non-adventure files (this README, `.gitignore`) are fine to keep in the tree.
