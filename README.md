# goto-tty

Jump to a terminal window or tab by process name, PID, or TTY — works with iTerm2 and Terminal.app on macOS.

## Why

When you have dozens of terminal tabs open, finding the one running a specific process means hunting through them manually. `goto-tty` lets you jump directly to it from anywhere.

## Installation

```bash
curl -o ~/bin/goto-tty https://raw.githubusercontent.com/cycl0nus/goto-tty/main/goto-tty
chmod +x ~/bin/goto-tty
```

Or clone and symlink:

```bash
git clone https://github.com/cycl0nus/goto-tty.git
ln -s "$PWD/goto-tty/goto-tty" ~/bin/goto-tty
```

Make sure `~/bin` is in your `$PATH`.

**Requirements:** macOS, zsh, Python 3 (pre-installed on macOS 12+). No external dependencies.

**Permissions:** On first run, macOS will prompt for Accessibility and Automation permissions for Terminal/iTerm2 switching to work.

## Usage

```
goto-tty [--continuous] [process-name | PID | TTY]
```

### Jump by process name

```bash
goto-tty claude
goto-tty vim
goto-tty python
```

If one match is found, jumps immediately. If multiple matches are found, shows an interactive menu to pick from.

### Jump by PID

```bash
goto-tty 91234
```

Resolves the PID to its TTY and jumps directly.

### Jump by TTY

```bash
goto-tty ttys003
goto-tty s003
goto-tty /dev/ttys003
```

All three forms are accepted and normalized automatically.

### Interactive prompt

```bash
goto-tty
```

Prompts for input if no argument is given.

### Continuous mode

```bash
goto-tty --continuous zsh
```

Keeps a live menu open in a terminal tab. The list auto-refreshes every 30 seconds in the background (no blank screen during refresh). Press `r` to refresh immediately, `Enter` to jump to a tab, `q` or `Esc` to exit.

This is useful as a persistent "tab switcher" — dedicate one terminal tab to it and use it as a jump pad.

## Menu controls

| Key | Action |
|-----|--------|
| `↑` / `k` | Move up |
| `↓` / `j` | Move down |
| `←` / `→` | Scroll horizontally |
| `1`–`9` | Jump directly to item N |
| `Enter` | Select / jump |
| `r` | Refresh now (continuous mode only) |
| `q` / `Esc` | Cancel / quit |

## How it works

- **Process lookup:** `ps -eo pid,tty,comm` with a case-insensitive substring match on the process name
- **Tab titles:** A single AppleScript call fetches all open TTY→title mappings from iTerm2 and Terminal.app at once
- **Switching in iTerm2:** Native AppleScript (`select window`, `select tab`, `select session`)
- **Switching in Terminal.app:** `set index of window to 1` activates the target tab, then `AXRaise` via System Events raises the OS window. A background AppleScript with a short delay prevents focus flickering back to the calling tab.
- **Interactive UI:** Python `curses` embedded in the shell script — no external tools required

## Supported terminals

| Terminal | Tab listing | Switching |
|----------|-------------|-----------|
| iTerm2 | ✓ | ✓ |
| Terminal.app | ✓ | ✓ |
| Other (tmux, SSH, etc.) | — | — |

Processes running in tmux panes, SSH sessions, or other terminals will appear in the process list but cannot be switched to automatically.

## License

MIT
