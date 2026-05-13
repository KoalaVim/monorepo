# koala-monorepo

Umbrella repo that pins the Koala projects together as git submodules.

## Quick start

```sh
git clone --recurse-submodules git@github.com:KoalaVim/monorepo.git
# or after a plain clone:
make init
```

## Common tasks

| Command | Purpose |
| --- | --- |
| `make init` | Clone/initialize submodules after the first checkout. |
| `make update` | Fast-forward every submodule to its remote default branch. Aborts if any submodule has uncommitted changes or untracked files. |
| `make status` | One-line status per submodule. |
| `make foreach CMD='git log -1'` | Run a shell command in every submodule. |

## Adding / removing repos

```sh
make add REPO=git@github.com:KoalaVim/new-thing.git
make add REPO=git@github.com:KoalaVim/new-thing.git PATH_=custom/dir
make remove NAME=ez-workspaces
```

Each `add` / `remove` modifies `.gitmodules` — commit the change to persist it.
The `update` target reads `.gitmodules` directly, so newly-added repos are
picked up automatically with no Makefile edits.

> `PATH_` (with trailing underscore) is used instead of `PATH` to avoid
> clobbering the shell `PATH` variable inside `make`.
