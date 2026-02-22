# Quick guide to understanding the workflow, followed by a cheat sheet of the daily commands.

### The LunarVim Mental Model

* **Normal Mode is Home:** You should always be in Normal mode (press `Esc`) unless you are actively typing code. All your shortcuts and navigation happen here.
* **Buffers vs. Tabs:** LunarVim uses "buffers" for open files, which look like tabs at the top of your screen. You navigate between them rather than opening multiple UI windows.
* **Telescope is your Search Engine:** You rarely need to click through folders. You will use Telescope (a fuzzy finder) to instantly search for files, text strings, or git commits.

---

### LunarVim Cheat Sheet

#### 1. The Absolute Basics (Vim Legacy)

These commands are standard to Vim/Neovim but are the foundation of moving around.

| Action | Command (in Normal Mode) |
| --- | --- |
| **Move Cursor** | `h` (left), `j` (down), `k` (up), `l` (right) |
| **Enter Insert Mode** | `i` (insert before cursor), `a` (append after cursor) |
| **Enter Visual Mode** | `v` (highlight characters), `V` (highlight lines) |
| **Save File** | `:w` or `Space` + `w` |
| **Quit** | `:q` or `Space` + `q` |
| **Return to Normal Mode** | `Esc` (or `jk` / `kj` rapidly if configured) |

#### 2. File Explorer (NvimTree)

LunarVim comes with a built-in file tree on the left side.

| Action | Command |
| --- | --- |
| **Toggle File Explorer** | `Space` + `e` |
| **Open File / Expand Folder** | `Enter` (while hovering over it in the tree) |
| **Add New File/Folder** | `a` (while inside the tree) |
| **Delete File/Folder** | `d` (while inside the tree) |
| **Rename File/Folder** | `r` (while inside the tree) |

#### 3. Navigation & Search (Telescope)

This is how you find things instantly without using the file tree.

| Action | Command |
| --- | --- |
| **Find File by Name** | `Space` + `f` |
| **Search Text Globally (Grep)** | `Space` + `s` + `t` |
| **Search Open Buffers** | `Space` + `b` + `b` |
| **Recent Files** | `Space` + `s` + `r` |
| **Close Telescope** | `Esc` (often requires two presses) |

#### 4. Buffer & Window Management

How to handle your open files and split screens.

| Action | Command |
| --- | --- |
| **Next Open Buffer (Tab)** | `Shift` + `l` |
| **Previous Open Buffer (Tab)** | `Shift` + `h` |
| **Close Current Buffer** | `Space` + `c` |
| **Split Window Vertically** | `Ctrl` + `w` then `v` |
| **Split Window Horizontally** | `Ctrl` + `w` then `s` |
| **Move Between Splits** | `Ctrl` + `h` / `j` / `k` / `l` |

#### 5. Code Intelligence (LSP)

LunarVim has built-in Language Server Protocol (LSP) support, giving it VS Code-like intelligence.

| Action | Command |
| --- | --- |
| **Go to Definition** | `g` + `d` |
| **Go to References** | `g` + `r` |
| **Hover Documentation** | `Shift` + `k` |
| **Code Actions (Fixes)** | `Space` + `l` + `a` |
| **Format Code** | `Space` + `l` + `f` |
| **Rename Variable** | `Space` + `l` + `r` |

#### 6. Built-in Terminal

You don't need to leave LunarVim to run shell commands.

| Action | Command |
| --- | --- |
| **Toggle Floating Terminal** | `Ctrl` + `\` or `Option` + `\` (depends on keyboard) |
| **Exit Terminal Mode** | `Ctrl` + `\` |

---
