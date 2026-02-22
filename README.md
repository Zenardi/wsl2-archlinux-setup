- [WSL2 + ArchLinux Setup](#wsl2--archlinux-setup)
    - [1. Create Your New User](#1-create-your-new-user)
    - [2. Initialize the Package Manager and Enable Sudo Privileges](#2-initialize-the-package-manager-and-enable-sudo-privileges)
    - [3. Set the Default WSL User](#3-set-the-default-wsl-user)
    - [4. Install NeoVim \& git](#4-install-neovim--git)
    - [5. Install LunarVim](#5-install-lunarvim)
    - [6. Install yay](#6-install-yay)
    - [7. Install on-my-zsh \& Powerlevel10k](#7-install-on-my-zsh--powerlevel10k)
      - [7.1 Add lvim export](#71-add-lvim-export)
      - [7.2 zsh plugins](#72-zsh-plugins)
    - [8. Shell Commands on Rust](#8-shell-commands-on-rust)
- [Install Windows Terminal](#install-windows-terminal)


# WSL2 + ArchLinux Setup 

Setting up Arch Linux on WSL2 is a bit like building a ship in a bottle—it’s rewarding, but the initial environment is usually a "bare-bones" root shell. Since WSL Arch distributions often don't come with a default user, you'll want to get out of the `root` account as quickly as possible for safety.

Here is the step-by-step process to create your user, grant them administrative powers, and set them as the default.

---

### 1. Create Your New User

While logged in as **root** in your Arch terminal, run the following command to create a new user. Replace `yourusername` with your desired name.

```bash
useradd -m -G wheel -s /bin/bash yourusername

```

* `-m`: Creates the home directory (`/home/yourusername`).
* `-G wheel`: Adds the user to the "wheel" group (the standard group for sudoers).
* `-s /bin/bash`: Sets Bash as your default shell.

Next, **set a password** for the new user:

```bash
passwd yourusername

```

---

### 2. Initialize the Package Manager and Enable Sudo Privileges

By default, members of the `wheel` group don't have "sudo" powers until you uncomment a specific line in the sudoers file.

1. **Install sudo** (if it isn't already there):
```bash
pacman-key --init
pacman-key --populate
pacman -Ssy archlinux-keyring && pacman -Syu
pacman -Su
pacman -S sudo
```


2. **Open the sudoers file** using `visudo` (this safely checks for syntax errors):
```bash
EDITOR=nano visudo

```


3. **Find this line** (scroll down or use `Ctrl+W` to search):
`# %wheel ALL=(ALL:ALL) ALL`
4. **Uncomment it** by removing the `#` at the beginning:
`%wheel ALL=(ALL:ALL) ALL`
5. **Save and exit** (`Ctrl+O`, `Enter`, `Ctrl+X`).

---

### 3. Set the Default WSL User

Currently, every time you open Arch, it will still log you in as `root`. To change this so you automatically log in as your new user:

1. **Close your Arch terminal.**
2. **Open PowerShell** or Command Prompt on Windows.
3. **Run the following command**, replacing `Arch` with your specific distribution name (use `wsl -l` if you aren't sure) and `yourusername` with the one you just created:

```powershell
wsl --manage archlinux --set-default-user developer
```

> **Note:** If you are using a custom Arch build (like `ArchWSL`), the command might be `arch.exe config --default-user yourusername`.

---

### 4. Install NeoVim & git
```sh
sudo pacman -S neovim git yarn npm
```

### 5. Install LunarVim
```sh
sudo pacman -S git yarn npm python3 rust base-devel make python cargo ripgrep python-pipx
sudo pacman -S python-pynvim
 LV_BRANCH='release-1.4/neovim-0.9' bash <(curl -s https://raw.githubusercontent.com/LunarVim/LunarVim/release-1.4/neovim-0.9/utils/installer/install.sh)

##################################################################
########## , selecting "no" for installing python dependencies.###
##################################################################
[developer@alienware51 ~]$ LV_BRANCH='release-1.4/neovim-0.9' bash <(curl -s https://raw.githubusercontent.com/LunarVim/LunarVim/release-1.4/neovim-0.9/utils/installer/install.sh)

      88\                                                   88\
      88 |                                                  \__|
      88 |88\   88\ 888888$\   888888\   888888\ 88\    88\ 88\ 888888\8888\
      88 |88 |  88 |88  __88\  \____88\ 88  __88\\88\  88  |88 |88  _88  _88\
      88 |88 |  88 |88 |  88 | 888888$ |88 |  \__|\88\88  / 88 |88 / 88 / 88 |
      88 |88 |  88 |88 |  88 |88  __88 |88 |       \88$  /  88 |88 | 88 | 88 |
      88 |\888888  |88 |  88 |\888888$ |88 |        \$  /   88 |88 | 88 | 88 |
      \__| \______/ \__|  \__| \_______|\__|         \_/    \__|\__| \__| \__|

--------------------------------------------------------------------------------
Detecting platform for managing any additional neovim dependencies
--------------------------------------------------------------------------------
Would you like to install LunarVim's NodeJS/BunJS dependencies: neovim, tree-sitter-cli?
[y]es or [n]o (default: no) : y
Installing node modules with yarn..
yarn global v1.22.22
[1/4] Resolving packages...
⠁ (node:2334) [DEP0169] DeprecationWarning: `url.parse()` behavior is not standardized and prone to errors that have security implications. Use the WHATWG URL API instead. CVEs are not issued for `url.parse()` vulnerabilities.
(Use `node --trace-deprecation ...` to show where the warning was created)
[2/4] Fetching packages...
[3/4] Linking dependencies...
[4/4] Building fresh packages...
success Installed "neovim@5.4.0" with binaries:
      - neovim-node-host
success Installed "tree-sitter-cli@0.26.5" with binaries:
      - tree-sitter
Done in 1.10s.
All NodeJS dependencies are successfully installed
--------------------------------------------------------------------------------
Would you like to install LunarVim's Python dependencies: pynvim?
[y]es or [n]o (default: no) : n


########################
# Export. Add to .zshrc
export PATH=~/.cargo/bin:~/.local/bin:$PATH

#######################
# Run
lvim
```

> [!WARNING]
> A workaround for this is to manually install the (so far only) python dependency, 'pynvim'. You can do so on Arch with the following command;
> sudo pacman -S python-pynvim
> Then install LunarVim again, selecting "no" for installing python dependencies.
> * https://github.com/LunarVim/LunarVim/issues/4403
> * https://github.com/LunarVim/LunarVim/issues/4050


### 6. Install yay
```sh
cd /tmp
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```
> [!NOTE]
> https://github.com/Jguer/yay?tab=readme-ov-file


### 7. Install on-my-zsh & Powerlevel10k
```sh
yay -S zsh
yay -S --noconfirm zsh-theme-powerlevel10k-git
yay -S ttf-meslo-nerd-font-powerlevel10k powerline-fonts awesome-terminal
echo 'source /usr/share/zsh-theme-powerlevel10k/powerlevel10k.zsh-theme' >>~/.zshrc

# Change shell
 chsh -s /usr/bin/zsh

# Type password and re-open window/tab on Windows Terminal
```

#### 7.1 Add lvim export
```
# Enable Powerlevel10k instant prompt. Should stay close to the top of ~/.zshrc.
# Initialization code that may require console input (password prompts, [y/n]
# confirmations, etc.) must go above this block; everything else may go below.
if [[ -r "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh" ]]; then
  source "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh"
fi

source /usr/share/zsh-theme-powerlevel10k/powerlevel10k.zsh-theme

# To customize prompt, run `p10k configure` or edit ~/.p10k.zsh.
[[ ! -f ~/.p10k.zsh ]] || source ~/.p10k.zsh

# This line
export PATH=$HOME/.local/bin:$HOME/.cargo/bin:$PATH
```

#### 7.2 zsh plugins

[zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions)

```sh
# On home directory
mkdir .zsh
cd .zsh
git clone https://github.com/zsh-users/zsh-autosuggestions ~/.zsh/zsh-autosuggestions

# Add the following to your .zshrc
source ~/.zsh/zsh-autosuggestions/zsh-autosuggestions.zsh
```

### 8. Shell Commands on Rust

https://zaiste.net/posts/shell-commands-rust/

```sh
cargo install bat exa procs dust tokei ytop tealdeer grex rmesg zoxide delta
```


# Install Windows Terminal 
https://windowsterminalthemes.dev/
- Moonligh II
```json
{
  "name": "Moonlight II",
  "black": "#191a2a",
  "red": "#ff757f",
  "green": "#c3e88d",
  "yellow": "#ffc777",
  "blue": "#82aaff",
  "purple": "#c099ff",
  "cyan": "#86e1fc",
  "white": "#c8d3f5",
  "brightBlack": "#828bb8",
  "brightRed": "#ff757f",
  "brightGreen": "#c3e88d",
  "brightYellow": "#ffc777",
  "brightBlue": "#82aaff",
  "brightPurple": "#c099ff",
  "brightCyan": "#86e1fc",
  "brightWhite": "#c8d3f5",
  "background": "#222436",
  "foreground": "#c8d3f5"
}
```
