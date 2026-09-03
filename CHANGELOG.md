# Changelog

All notable changes to GitEmUp will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [0.4.4] - 2026-09-03
### Added
- Repo Digest: header stats (total commits/contributors/active branches), per-branch diffstat and live ahead/behind count, last-commit summary shown even on branches with no new activity, stale branches collapsed into their own section, new-remote-branch detection, and a per-author commit drill-down window.
- Commit graph: optional Changes column (insertions/deletions pie + count), a hover Info button per row (also in the Branch/Contributor History windows), and SHA search (full or short, prefix-matched) alongside description/author.
- Blame now available in the Staging panel and File Compare Window, in addition to the Commit Detail Panel and Diff Viewer Window, blaming each side of a diff independently where there are two. Repo Explorer's file history Blame now follows whichever version is currently previewed instead of only HEAD.
- Image preview (PNG/JPEG/GIF/SVG/WEBP/BMP/ICO) and HTML preview (read-only, sanitized — scripts/iframes/event-handlers always stripped).
- Diff settings tab: diff algorithm (Myers/Minimal/Patience), whitespace handling, merge conflict marker style (Merge/Diff3), and an optional auto-resolve favor (ours/theirs/union).
- Stats: top commits by additions and by removals, per selected range.
- Branches panel context menu: Copy Branch Name, Compare with Current Branch, Create Branch/Tag/Worktree Here, Push, Publish Branch, Change Upstream, Stop Tracking, Fetch This Branch, Create Pull Request from Branch, Reflog for This Branch, Delete Remote Branch.

### Fixed
- Markdown preview sanitization now explicitly matches the same safe-HTML allowlist used for PR descriptions/comments.
- A pushed branch's ref chip (e.g. `origin/main`) in the History tab could stay stuck on the old commit — push now also invalidates the commit graph's cached ref labels, not just branch ahead/behind state.
- Commit history list could lose its scroll position after switching away from the History tab and back.

## [0.4.3] - 2026-08-31
### Added
- SSH support for repos: clone/fetch/pull/push over SSH with automatic ssh-agent auth, an optional key-file fallback (path + passphrase, per credential profile or one-off per-clone), and known_hosts-style host key verification (silent when known, one-time confirm when unknown, hard refusal when changed).
- Credential profiles now hold SSH fields alongside HTTPS ones, so one profile can serve either kind of remote.
- On an SSH repo with a PR-capable provider, a separate "This Repository's Credentials for Pull Requests" profile picker (PR/API access is always HTTPS-token-based, even over an SSH remote).
- Repo Explorer window, replacing the separate File Explorer and Content Search windows: one per-repo window with a file tree / content-search-results toggle.

### Changed
- Windows/Linux: the AES-256 key that decrypts stored credentials now lives in the OS keychain (Windows Credential Manager / Secret Service) instead of a plaintext-adjacent key file, migrated automatically on first launch; falls back to the old file-based approach if no keychain is reachable. macOS unchanged.

### Fixed
- App-close confirmation dialog could crash the app on Linux (native GTK dialog racing the app's own event loop during teardown); replaced with an in-app dialog.
- SSH with an Ed25519 key silently failed to authenticate on Windows only (incomplete Ed25519 support in Windows' default crypto backend); now routed through the same OpenSSL backend used for HTTPS.
- A console window would briefly flash on Windows during SSH host-key checks.
- Color pickers on Windows closed after the first click, before a color could be picked.
- Adding a new credential profile from inside the Clone dialog closed the whole Clone dialog.
- `origin/HEAD` could leak into the branch list with an empty commit ID, breaking branch-scoped commands.
- Creating and checking out a new branch from a dirty working tree now succeeds whenever the new branch points at HEAD's own commit, matching plain `git checkout -b`.
- Guarded against a keychain-read hiccup silently minting a replacement encryption key and orphaning stored credentials.

## [0.4.2] - 2026-08-24
### Added
- Pull request polish: draft PRs, mark-ready-for-review, edit title/description, PR/MR templates, "create a PR?" prompt after first push — across GitHub/GitLab/Azure DevOps/Gitea/Bitbucket
- Commit composer Simple/Extended modes (conventional-commit fields, subject counter, co-author picker), plus commit.template support
- Repo digest window — activity summary per branch since a chosen date
- Command palette: git-mutating commands, :group filtering, per-item actions (checkout/cherry-pick), live commit search
- Real CSP enabled on the webview (was previously disabled)

### Fixed
- Push left a stale "ahead" count until next fetch
- Push button didn't show its in-progress state
- Toolbar collapses to icon-only on narrow windows
- Help window cross-page anchor links now scroll correctly; external doc links open in the OS browser
- Proper Cut/Copy/Paste context menu on text fields
- Guard against removing a currently-open repo from the Repositories Hub
- A couple of overlay menus (notification bell included) no longer render behind other UI
- (unreleased, just fixed) the CSP hash Tauri auto-injects for index.html's inline <style> block was silently disabling unsafe-inline for all other inline styles in production builds — broke Help docs and diff rendering after tauri build. Fixed by moving that CSS out of index.html into the bundled stylesheet.

## [0.4.1] - 2026-08-21
### Added
- Hex preview for binary files: right-click a binary file → "View as Hex" opens a detached, paginated hex-dump viewer with jump-to-offset and click-to-select-byte.
- Binary file diffs: a real side-by-side byte-level comparison in every diff view, instead of "Binary file — cannot display diff".
- Printable keyboard shortcuts cheat sheet (Ctrl/Cmd+Shift+?), as its own window alongside the existing `?` cheat sheet.

### Fixed
- Console export button silently doing nothing (missing filesystem write permission).
- Repo Stats silently regenerating (a full history walk) on routine cache invalidation instead of only on an explicit Refresh.
- DevTools/Inspect Element were still actually shipping in production builds despite the previous release's fix attempt — the Cargo feature gate never worked as intended; this is now genuinely resolved.
- Text selection and copy broken app-wide in production builds (WebKitGTK requires the `-webkit-user-select` CSS prefix, not just the standard property).

## [0.4.0] - 2026-08-21
### Added
- Git worktree support: list/add/open/remove/lock/prune from a sidebar panel and management window.
- Interactive rebase, startable from a right-clicked commit.
- Submodule support: add, remove, and update recursively, including on clone.
- Self-hosted Git provider support (GitHub Enterprise, GitLab, Bitbucket Server, Azure DevOps Server, Gitea) via a custom-provider override in Settings.
- Cross-repo dashboard in the Repositories Hub showing branch, dirty count, ahead/behind, and unread-PR count for every repo.
- Command palette (Cmd/Ctrl+K) for navigation and repo switching.
- Reflog browser tab in Branch Recovery.
- Shallow clone support with a configurable depth.
- Git blame in the file explorer and content search windows.
- Stage and unstage individual hunks from the diff gutter.
- Amend the last commit from the staging panel.
- Auto-refresh when the repository changes outside the app.
- Merged-branches cleanup tool.
- Global watches management from Settings.
- Undo for soft reset, mixed reset, stash drop, and force push.
- Keyboard shortcuts (v1).

### Changed
- Worktrees panel moved below the branches panel.
- Repo list: added a remove-item confirmation and refreshed stale wording.

### Fixed
- Delete-branch safety check no longer allows a force-delete to slip through.
- Symlinked repos no longer lose credentials, stats cache, or branch-deletion history (repo path keys are now canonicalized).
- Credential-bearing remote URLs are no longer persisted to `.git/config` on clone/add-remote/edit-remote.
- Several cross-window bugs: missing toasts, duplicate credential profiles, and stale Settings state.
- Stale/hanging diff pane for unstaged files.
- Force push, merge strategy, and fetch-ref reporting being ignored in some cases.
- DevTools no longer ship enabled in production builds.
