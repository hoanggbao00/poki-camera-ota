# poki-camera-ota

Published JavaScript bundles for **Poki Camera**. Nothing here is written by hand.

The app repo is private; this one is public because it has to be. A device checks for
updates before it has any credentials to check with, so both the manifest and the
bundle archive are fetched anonymously.

## Branches

`main` holds this file and nothing else. Each platform gets an orphan branch, so its
archive carries one bundle and no history from the others:

| Branch | Contents |
| --- | --- |
| `ota-android` | `index.android.bundle`, its assets, `ota.version.json`, `patches/` |
| `ota-ios` | `index.jsbundle`, its assets, `ota.version.json`, `patches/` |

## What a device reads

```
https://raw.githubusercontent.com/hoanggbao00/poki-camera-ota/ota-android/ota.version.json
https://github.com/hoanggbao00/poki-camera-ota/archive/refs/heads/ota-android.zip
```

The manifest names the bundle version and the app versions it may land on:

```json
{
  "version": "1.0.1",
  "isSemver": true,
  "targetVersions": { "android": ["1.0.0"] },
  "releaseNotes": "...",
  "patches": { "1.0.0": "patches/1.0.0-1.0.1.zip" }
}
```

`targetVersions` pins a bundle to the binary already installed, so a bundle built
against a newer native build is never offered to an older one. `patches` lets a device
download a bsdiff delta instead of the whole archive; `.gitattributes` keeps those out
of the archive itself, so a full download does not pay for every patch ever published.

## Publishing

From the app repo, after the version has been bumped:

```
bun run release
```

That dispatches `publish-ota.yml`, which bundles the JavaScript for both platforms and
force-moves these branches. Committing here by hand will be overwritten by the next run.

Only JavaScript ships this way. A new native module, permission, or bundled asset needs
a store build: the bundle is swapped, the binary is not.
