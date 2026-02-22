# WSL2 + ArchLinux Setup 

Setting up Arch Linux on WSL2 is a bit like building a ship in a bottle—it’s rewarding, but the initial environment is usually a "bare-bones" root shell. Since WSL Arch distributions often don't come with a default user, you'll want to get out of the `root` account as quickly as possible for safety.

Here is the step-by-step process to create your user, grant them administrative powers, and set them as the default.



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
    - [9. Installing asdf - Manage all your runtime versions with one tool](#9-installing-asdf---manage-all-your-runtime-versions-with-one-tool)
    - [10. Install podman, kubectl, and kind](#10-install-podman-kubectl-and-kind)
      - [10.1 Configure KIND to run using podman](#101-configure-kind-to-run-using-podman)
      - [10.2 Prepare to run KIND](#102-prepare-to-run-kind)
      - [10.3 Install nftables and the compatibility layer.](#103-install-nftables-and-the-compatibility-layer)
      - [10.4 Creating the Podman Network Configuration](#104-creating-the-podman-network-configuration)
      - [10.4 Install kubectl autocomplete](#104-install-kubectl-autocomplete)
    - [Final `zshrc` file](#final-zshrc-file)
- [Install Windows Terminal](#install-windows-terminal)



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
cd
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

# Add alias to .zshrc
alias ls="exa --icons"
alias cat="bat --style=auto"
```

### 9. Installing asdf - Manage all your runtime versions with one tool
https://asdf-vm.com/

```sh
yay -S asdf-vm

# Add to .zshrc
source /opt/asdf-vm/asdf.sh
```

### 10. Install podman, kubectl, and kind
```sh
yay -S podman podman-compose podman-docker kubectl kind

# For Podman to run containers without needing sudo, it requires a range of virtual user IDs. 
# Run the command below:
sudo usermod --add-subuids 100000-165535 --add-subgids 100000-165535 $USER

# Set Permissions for newuidmap and newgidmap
# Give these tools permission to perform operations with root privileges, 
# even when called by you. The simplest and standard way to do this in Arch is by enabling the setuid bit.
sudo chmod +s /usr/bin/newuidmap /usr/bin/newgidmap

# WSL2 mounts the system root (/) as "private", but Podman rootless requires it to be "shared" for container volumes to function correctly.
sudo mount --make-rshared /

podman run hello-world

# Create alias on .zshrc
alias podman="docker"

# To silence the warnning `Emulate Docker CLI using podman. Create /etc/containers/nodocker to quiet msg.`
sudo touch /etc/containers/nodocker
```
#### 10.1 Configure KIND to run using podman
Since Podman doesn't run a daemon like Docker, we need to explicitly tell the kind to use the Podman provider instead of looking for the Docker socket.
```sh
export KIND_EXPERIMENTAL_PROVIDER=podman
```

#### 10.2 Prepare to run KIND

Podman uses a modern networking stack called Netavark. To create the isolated virtual network where the nodes in your Kubernetes cluster will communicate, Netavark attempts to use nftables (the modern replacement for iptables in Linux) to create NAT and routing rules.

Since the Arch Linux image for WSL is extremely "raw," it simply doesn't come with the nftables package installed, causing the command to fail with the error "nft" did not return successfully.

To resolve this, we need to install the necessary networking tools.

#### 10.3 Install nftables and the compatibility layer.
```sh
yay -S -S nftables iptables-nft
```
> [!IMPORTANT] 
> Warning about WSL2: On very rare occasions, the WSL kernel may need to be **restarted** to recognize the new `nftables` network modules. If the error persists exactly the same after installation, close the terminal, run `wsl --shutdown` in Windows PowerShell, open Arch again and try kind create cluster.

#### 10.4 Creating the Podman Network Configuration

Microsoft compiles the standard WSL kernel without some advanced netfilter modules (the kernel's firewall engine). When netavark (Podman's standard network engine) attempts to inject native nftables rules to create the cluster's isolated network, the kernel says "I don't recognize this resource," generating the "No such file or directory" error.

The cleanest and most efficient way to resolve this, without needing to compile a custom kernel for WSL, is to change Podman's configuration so that it ignores the creation of firewall rules. Since we are running Podman in rootless mode, port mapping is already done in "userspace" by a tool called rootlessport, so we don't strictly need these kernel rules for the kind to work.

We will create or overwrite the global container configuration file to force the firewall driver to none.

Run the command below (it will create the directory and file automatically using tee):

```sh
sudo mkdir -p /etc/containers
cat <<EOF | sudo tee /etc/containers/containers.conf
[network]
firewall_driver = "none"
EOF
```

#### 10.4 Install kubectl autocomplete

Add these lines to begin of .zshrc file
```sh
autoload -Uz compinit
compinit
```

And these lines to the end of the file
```sh
source <(kubectl completion zsh)
compdef __start_kubectl k
```

Reload with `source ~/.zshrc`

### Final `zshrc` file
```sh
─────┬──────────────────────────────────────────────────────────────────────────────────────────────────────────────────
     │ File: .zshrc
─────┼──────────────────────────────────────────────────────────────────────────────────────────────────────────────────
   1 │ autoload -Uz compinit
   2 │ compinit
   3 │
   4 │ # Enable Powerlevel10k instant prompt. Should stay close to the top of ~/.zshrc.
   5 │ # Initialization code that may require console input (password prompts, [y/n]
   6 │ # confirmations, etc.) must go above this block; everything else may go below.
   7 │ if [[ -r "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh" ]]; then
   8 │   source "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh"
   9 │ fi
  10 │
  11 │ source /usr/share/zsh-theme-powerlevel10k/powerlevel10k.zsh-theme
  12 │
  13 │ # To customize prompt, run `p10k configure` or edit ~/.p10k.zsh.
  14 │ [[ ! -f ~/.p10k.zsh ]] || source ~/.p10k.zsh
  15 │
  16 │
  17 │ ######################################
  18 │
  19 │
  20 │ # LunarVim
  21 │ export PATH=$HOME/.local/bin:$HOME/.cargo/bin:$PATH
  22 │
  23 │ # KIND
  24 │ export KIND_EXPERIMENTAL_PROVIDER=podman
  25 │
  26 │ # zsh plugins
  27 │ source ~/.zsh/zsh-autosuggestions/zsh-autosuggestions.zsh
  28 │
  29 │ alias ls="exa --icons"
  30 │ alias cat="bat --style=auto"
  31 │ alias podman="docker"
  32 │ alias k='kubectl'
  33 │ source <(kubectl completion zsh)
  34 │ compdef __start_kubectl k
─────┴──────────────────────────────────────────────────────────────────────────────────────────────────────────────────
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
