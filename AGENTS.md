# Koala Monorepo

This monorepo contains the repositories that make up the KoalaVim ecosystem.

## Repositories

### kv — The Launcher

`kv` is the external orchestrator that lives outside of Neovim. It is responsible for preparing a fully isolated, dependency-complete environment and then handing control to KoalaVim.

**Responsibilities:**

- **Environment isolation** — Creates and manages virtual environments so multiple independent KoalaVim setups can coexist without interfering with each other.
- **Dependency management** — Ensures all required binaries are installed and available within each environment.
- **Launch modes** — Supports launching KoalaVim in different modes (normal editing, git status, git tree, git diff, AI) by signaling intent through environment variables.
- **Plugin version synchronization** — Manages plugin lockfiles to keep plugins pinned to tested versions and handles syncing them.
- **KoalaVim updates** — Fetches and applies updates to the KoalaVim distribution itself.
- **Restart management** — Monitors for restart signals after KoalaVim exits and re-launches when requested.
- **Health checks** — Verifies that all required dependencies are available and functional within an environment.
- **Configuration delivery** — Passes the path to the user's configuration file so KoalaVim can load it.

### KoalaVim — The Editor

KoalaVim is a batteries-included Neovim distribution. It owns everything that happens inside the editor.

**Responsibilities:**

- **Plugin curation and configuration** — A curated set of plugins organized by category (AI, autocomplete, coding, debug, editor, git, LSP, sessions, search, terminal, UI, and more).
- **Editor defaults** — Sensible baseline options for indentation, line numbers, undo, scrolling, and display.
- **Keybindings** — Comprehensive keymaps for editing, navigation, search, splits, tabs, git operations, and AI interactions.
- **UI layer** — Colorscheme, statusline, bufferline, file explorer, notifications, dashboard, and visual polish.
- **AI integration** — In-editor AI workflows including context-sending, edit prompts, and prompt navigation.
- **Git workflow** — Inline diffs, staging, commit, blame, visual diff browsing, and pull request management from within the editor.
- **LSP orchestration** — Pre-configured language servers, formatting, and linting for multiple languages.
- **Session management** — Automatic per-directory session save and restore.
- **User configuration** — A JSON-based configuration system with schema validation, supporting layered defaults and per-repo overrides.
- **Launch mode handling** — Reads the mode signaled by `kv` and configures the UI and behavior accordingly.

## How They Interact

The interaction between `kv` and KoalaVim is primarily **unidirectional — from kv to KoalaVim** — using environment variables and the filesystem as the communication layer.

### kv → KoalaVim

Before spawning the editor process, `kv` prepares the environment by:

1. Setting up an isolated directory structure for the target environment.
2. Ensuring all dependencies are on the search path.
3. Setting environment variables that encode the launch mode, configuration path, debug settings, and session preferences.

KoalaVim reads these environment variables during initialization and adjusts its behavior — for example, entering a git-focused UI when launched in git mode, or skipping session restore for single-purpose launches.

### KoalaVim → kv

The only feedback channel from KoalaVim back to `kv` is a **restart indicator file**. When KoalaVim needs to restart (e.g., after a configuration change), it writes a file to a known location. After the editor process exits, `kv` checks for this file and, if present, re-launches the editor in a loop.

### Shared State

- **Plugin lockfile** — `kv` writes the lockfile to pin plugin versions; KoalaVim's plugin manager reads it to install or restore the correct versions.
- **User configuration file** — Written by the user, its path is passed by `kv`, and it is read by KoalaVim to apply settings.

### Summary

`kv` handles the **"around the editor"** concerns — isolation, dependencies, updates, and launch orchestration. KoalaVim handles the **"inside the editor"** concerns — plugins, keybindings, UI, AI, git, LSP, and the full editing experience. Together they form a portable, reproducible, and feature-rich Neovim environment.
