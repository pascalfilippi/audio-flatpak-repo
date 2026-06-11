# audio-flatpak-repo

Central GitHub Pages-hosted Flatpak repo. Apps are built in their own source
repos and dispatched here for publication.

## Add flatpak repo

```sh
flatpak remote-add --user --if-not-exists \
  audio-flatpak https://pascalfilippi.github.io/audio-flatpak-repo/audio-flatpak.flatpakrepo

flatpak install --user audio-flatpak fm.reaper.Reaper
```
