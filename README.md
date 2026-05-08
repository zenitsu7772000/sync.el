# sync.el

GET sync.el: https://sync-el.web.app/

<img width="860" height="693" alt="01" src="https://github.com/user-attachments/assets/b7a8f2e5-d1aa-4bca-a586-d1b711a59c77" />


`sync.el` is a minimal, asynchronous, Git-native synchronization package for Org-mode.

It aims to provide:
- automatic sync on save/startup/interval
- conflict detection with review UI
- file watching for external updates
- offline-safe behavior with queued retries
- recovery tooling for interrupted states

## Quick Start

```elisp
(require 'sync)

(setq sync-directory "~/org")
(sync-mode 1)
```

That is enough for baseline usage.

## MELPA-Ready Structure

```text
sync/
  README.md
  lisp/
    sync.el
    sync-core.el
    sync-git.el
    sync-watch.el
    sync-conflict.el
    sync-ui.el
    sync-log.el
    sync-async.el
  docs/
    INSTALLATION.md
    USAGE.md
    CONFLICTS.md
    RECOVERY.md
```

## Design

- Local-first: edits are always local and never blocked by sync failures.
- Async-first: all Git/snapshot work runs through non-blocking processes.
- Git-compatible: plain repositories, no Magit dependency, no proprietary backend.
- Failure-aware: locking, conflict detection, and recovery commands included.

## Core Commands

- `M-x sync-mode` - enable/disable sync engine
- `M-x sync-now` - trigger immediate sync
- `M-x sync-status` - show state summary
- `M-x sync-show-log` - view detailed logs
- `M-x sync-show-conflicts` - list unresolved conflicts
- `M-x sync-recover` - recover from interrupted rebase/sync state

## Customization

- `sync-directory`
- `sync-remote-url`
- `sync-auto-sync`
- `sync-sync-interval`
- `sync-enable-watchers`
- `sync-enable-auto-commit`
- `sync-log-level`
- `sync-conflict-strategy`
- `sync-backup-directory`

## Sync Flow

1. Collect trigger (save/startup/interval/manual/watch).
2. Debounce and queue.
3. Create async backup snapshot.
4. Stage Org files (`git add`).
5. Commit if needed (`sync: update tasks.org`, etc.).
6. Pull with rebase.
7. Detect conflicts (`git diff --diff-filter=U`).
8. Push on clean state.

## Conflict Handling Example

When pull/rebase creates conflicts:

1. sync state changes to `conflict` in mode line.
2. Run `M-x sync-show-conflicts`.
3. Open file with `RET` and resolve markers.
4. Use `M-x sync-now` after resolution.

## Recovery Workflow Example

If a sync is interrupted:

1. Run `M-x sync-recover`.
2. It attempts `git rebase --abort` asynchronously.
3. It immediately triggers a fresh sync cycle.
4. Inspect `M-x sync-show-log` for details.

## Performance Notes

- Uses async process sentinels for all Git operations.
- Debounced sync scheduling avoids excessive churn.
- Watcher refresh is deduplicated with short idle timers.
- No heavy polling loops.

## Optional Advanced Extensions

Planned extension points:
- encrypted repository workflow hooks
- Syncthing profile integration
- selective sync filters
- multiple sync profiles

