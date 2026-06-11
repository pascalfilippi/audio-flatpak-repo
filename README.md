# audio-flatpak-repo

Central GitHub Pages-hosted Flatpak repo. Apps are built in their own source
repos and dispatched here for publication.

## End-user install

```sh
flatpak remote-add --user --if-not-exists \
  audio-flatpak https://pascalfilippi.github.io/audio-flatpak-repo/audio-flatpak.flatpakrepo

flatpak install --user audio-flatpak fm.reaper.Reaper
```

## How publishing works

```
  ┌──────────────────────┐   repository_dispatch (bundle_url)
  │ reaper-flatpak       │ ───────────────────────┐
  └──────────────────────┘                        │
  ┌──────────────────────┐                        ▼
  │ yabridge-flatpak     │ ──────────────►  ┌────────────────────────┐
  └──────────────────────┘                  │ audio-flatpak-repo     │
  ┌──────────────────────┐                  │  publish.yml           │
  │ reaper-yabridge-mgr  │ ──────────────►  │  → ostree import       │
  └──────────────────────┘                  │  → push gh-pages       │
                                            │  → deploy-pages        │
                                            └────────────────────────┘
                                                       │
                                                       ▼
                                       https://pascalfilippi.github.io/
                                              audio-flatpak-repo/
```

Each source repo's `build.yml` uploads its `.flatpak` bundle as a rolling
`latest` GitHub Release, then dispatches a `bundle-published` event here.
`publish.yml` downloads the bundle, imports it into the ostree repo on the
`gh-pages` branch, regenerates the summary, and deploys via GitHub Pages.

The `gh-pages` branch holds the published `repo/` tree directly — it is both
the state of record and the deployed artifact.

## Adding more apps later

Any repo that fires the `bundle-published` event with a valid `bundle_url`
will be merged into the same ostree repo. To add a new app:

1. Create the new source repo with its `build.yml` (copy the pattern from
   one of the existing source repos).
2. Add `DISPATCH_PAT` secret (same PAT as the existing source repos).
3. Push. The first build will appear in this repo on its next publish.

## First-time setup

See [SETUP.md](SETUP-flatpak-repo.md).

## Manual republish

If you need to re-import a specific bundle (e.g. after wiping the gh-pages
branch): Actions → publish → Run workflow, paste the bundle URL.
