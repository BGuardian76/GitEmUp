# Changelog

All notable changes to GitEmUp will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [0.4.7] - 2026-09-15
### Added
- CI/CD Deployments: a second tab in the CI/CD Builds window listing recent deployments per environment (status, branch/commit when resolvable, and time), for GitHub, GitLab, Bitbucket, and Azure DevOps. Azure tries the modern multi-stage Environments API first and falls back to classic Release Pipelines automatically; if a deployment's linked build was purged by Azure's retention policy, the build number is shown in place of a missing branch name. Gitea has no deployment-tracking API, so its Deployments tab shows an explanatory message instead of an empty list.
- CI/CD stage/job-level detail: expand a run in the Builds tab to see its individual stages or jobs (name, status, timing), fetched only on expand — shows a multi-stage pipeline's manual approval gate as its own entry instead of one overall run status.
- CI/CD status badges now also appear on commit rows in the commit graph (new, separate Settings → CI/CD toggle) and next to each pull request's target branch in the Pull Requests list (reuses the existing branch-badge setting).
- CI/CD auto-refresh: the Builds and Deployments lists now poll automatically, but only while something in the list is running, queued, or pending, and back off entirely if a provider returns a rate-limit error.
- Gitea CI/CD support (self-hosted only, via a registered custom provider) alongside the four native cloud providers.

### Changed
- Settings: CI/CD badge settings moved out of Issue Trackers into their own dedicated CI/CD tab (eleven tabs total), alongside the new commit-graph badge toggle. The Settings dialog also got a visual refresh — per-tab icons and a wider dialog.

## [0.4.6] - 2026-09-14
### Added
- CI/CD Builds: a read-only list of recent runs for the repository's native CI provider (GitHub Actions, GitLab CI, Azure Pipelines, or Bitbucket Pipelines) via Collaborate → CI/CD Builds — auto-detected the same way Pull Requests are, reusing the same assigned credential, no separate connection or token needed. Each run shows status, branch, commit, trigger, and time; click to open it in the browser. A branch filter narrows the list, and right-click offers "Open Pipeline Definition" (the workflow's/pipeline's own run-history page) on GitHub Actions and Azure Pipelines in addition to the specific run. Self-hosted providers and deployments are not included yet.
- CI/CD branch badges: an opt-in status badge on branch names in the Branches panel reflecting each branch's latest CI/CD run, so you don't need to open the Builds window to check. Off by default (Settings → Issue Trackers → CI/CD Branch Badges), since enabling it means a background API call whenever the panel is visible; choose local branches only (default) or local and remote-tracking branches.

## [0.4.5] - 2026-09-09
### Added
- Issue tracker badges: link Jira (Cloud/Server) and/or Azure DevOps Boards accounts with a personal access token in Settings → Authentication. Commits and branches referencing a ticket key get a clickable badge with a live title/status/assignee tooltip; multiple independent connections are supported. Read-only — no ticket is ever created, edited, or transitioned.
- Per-line staging: hunk-level staging now goes down to individual lines, in both Full file diff and Side by side view. A Staged/Unstaged badge on the diff pane makes clear which side of the index is shown.
- Repo Explorer: untracked/ignored files now show in the tree with four independent filter chips (tracked/deleted/untracked/ignored), a real hex-grid preview for binary files, blame "reblame" navigation (step a blamed line's edit history back/forward), and a status-aware right-click menu (restore a deleted file, toggle `.gitignore`).
- Git notes: a shareable, per-commit annotation stored in git's own standard `refs/notes/commits`, separate from the existing private per-repo Notes tab. Add/edit from a commit's right-click menu; shown in the Commit Info dialog and as a graph badge. Sync is a manual, separate action next to Force Push.
- Git LFS awareness: an LFS-tracked file's diff now shows its real object size instead of raw pointer text, for committed, staged, and untracked pointer files alike. New File size format setting (raw bytes vs. human-readable KB/MB/GB).
- Diff toolbar: a ¶ toggle to show whitespace characters directly (spaces as dots, tabs as arrows).

### Fixed
- Pasting into any text field silently failed on Linux ("clipboard access was denied") — the Cut/Copy/Paste menu relied on the browser's own clipboard API, which Tauri's Linux webview doesn't reliably support for reading; switched to Tauri's native clipboard plugin on every platform.
- Azure DevOps pull requests had no way to open in a browser — Azure's PR API doesn't return a browsable link the way its repository API does, so the button silently never appeared; GitEmUp now builds the standard PR URL itself when the API doesn't supply one.
- "Open in browser" links (PR details, issue tracker badges) could silently do nothing on Linux, relying on a plain link click a webview can swallow instead of launching the system browser; routed through Tauri's opener plugin everywhere.
- A file staged/unstaged partially and then acted on with a whole-file Stage/Unstage/Discard could leave its diff pane showing stale, already-reverted content indefinitely.
- A staging click fired while another was still in flight could occasionally compute against stale content instead of the one just applied; concurrent clicks are now serialized so one is always either applied cleanly or safely ignored.

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
