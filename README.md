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

## How adventures are hosted here

How large an export ends up is decided by one thing: how its images were attached in the picker.

### Uploaded images, embedded as data URLs

Images added with **Upload File** are baked into the export itself as data URLs
(`data:image/<type>;base64,<string>`), at the image's full original resolution. Base64 adds roughly
another third on top of the raw bytes. An adventure with a normal number of icons and portraits
therefore lands in the tens or hundreds of megabytes; the two here are ~73 MB and ~166 MB.

These have to live as **assets on a GitHub Release**, not as committed files and not in Git LFS:

- GitHub **hard-rejects any file over 100 MB** in the git tree, so the 166 MB export cannot be
  committed there at all.
- **Git LFS is metered.** Every LFS download counts against the repo owner's monthly LFS bandwidth,
  and GitHub bills overages (they can accrue whether or not a payment method is on file). A popular
  adventure downloaded by many people would burn through it fast.
- **Release assets are not metered**, allow up to **2 GB** each, and download directly. No LFS, no
  bandwidth billing, no 100 MB limit.

### Trinetra images, stored as links

Images from **Trinetra**, whether pasted as a direct link or picked through the Trinetra Browser,
are stored as the image **URL** only. Nothing is embedded. Those exports stay small enough that
GitHub's limits never come into play, and on size alone they would sit in the git tree without any
trouble.

Size is not the whole story, though: a `.json` anywhere in the tree changes how the extension reads
the repo. See the precedence rule below before committing one.

## Adding or updating an adventure

1. In the extension, export the adventure to a `.json` file.
2. On GitHub, create a Release (or edit the existing one) and **attach the `.json` as a release
   asset**.
3. Make sure that release is the **latest** one; the extension only reads the latest release.

That is all: the extension lists the latest release's `.json` assets and imports the one you pick.

In this repo, do **not** commit an adventure `.json` into the tree, however small it is. The reason
is the next section.

## Important: any `.json` in the tree hides the whole release

The extension falls back to the release assets **only when the git tree contains no `.json` files**.
The moment a single adventure `.json` is committed, the extension lists the tree copies and ignores
the release entirely, even if that release holds a dozen other adventures.

That makes the choice all-or-nothing per repo:

- **Repo hosts any upload-image adventure:** keep *every* adventure in Releases, including the small
  Trinetra ones. Committing one lightweight export would hide all the heavy ones. This repo takes
  this route.
- **Repo is Trinetra-only:** committing the exports to the tree is fine. It also avoids the CORS
  problem above, since the tree is read through the GitHub API rather than the release CDN.

If this repo still has `.json` exports committed (LFS or otherwise), remove them from the tree and
drop the LFS tracking, then rely on the release assets:

```bash
git rm --cached "*.json"          # stop tracking the exports (keeps your local files)
git rm .gitattributes             # drop the LFS rules if they only covered those exports
git commit -m "Host adventures as release assets instead of tree files"
git push origin main
```

Small, non-adventure files (this README, `.gitignore`) are fine to keep in the tree.
