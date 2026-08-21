# Changelog

All notable changes to GitEmUp will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

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
