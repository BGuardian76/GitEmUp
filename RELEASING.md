# Release process

This repo has no source and no CI — installers are built locally in the
GitEmUp source project and published here by hand. Follow these steps each
release.

## 1. Build the installables

In the source project (`sparkgit`), bump the version in `package.json` and
`src-tauri/tauri.conf.json` (keep them in sync), then build for each
platform you can build on:

```sh
pnpm tauri build
```

Bundled installers land under `src-tauri/target/release/bundle/`:

- `nsis/GitEmUp_<version>_x64-setup.exe` (Windows)
- `dmg/GitEmUp_<version>_<arch>.dmg` (macOS)
- `deb/`, `rpm/`, `appimage/` (Linux)

Collect the artifacts you built for this release into one folder before
continuing.

## 2. Update the changelog

In this repo, move the relevant entries from `[Unreleased]` in
`CHANGELOG.md` into a new dated section, e.g.:

```md
## [0.5.0] - 2026-08-21
### Added
- ...
### Fixed
- ...
```

Commit and push:

```sh
git add CHANGELOG.md
git commit -m "Prepare v0.5.0 release notes"
git push
```

## 3. Tag the release

Tag matches the version, prefixed with `v`:

```sh
git tag v0.5.0
git push origin v0.5.0
```

## 4. Publish the GitHub Release

Using the [GitHub CLI](https://cli.github.com/) from wherever you collected
the installers:

```sh
gh release create v0.5.0 \
  path/to/GitEmUp_0.5.0_x64-setup.exe \
  path/to/GitEmUp_0.5.0_amd64.dmg \
  path/to/GitEmUp_0.5.0_amd64.deb \
  path/to/GitEmUp_0.5.0_amd64.rpm \
  path/to/GitEmUp_0.5.0_amd64.AppImage \
  --title "v0.5.0" \
  --notes-file <(sed -n '/## \[0.5.0\]/,/## \[/p' CHANGELOG.md | sed '$d')
```

Or run `gh release create v0.5.0` without flags and paste the changelog
section into the editor that opens, then upload assets with:

```sh
gh release upload v0.5.0 path/to/file1 path/to/file2 ...
```

Only attach the platforms you actually built for that release — you don't
need all three every time.
