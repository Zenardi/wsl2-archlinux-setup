### Phase 1: Manual Bootstrap (Run as `root`)

Execute these commands the very first time you open the raw Arch terminal. Replace `yourusername` with your actual username.

```bash
# Create user and set password
useradd -m -G wheel -s /bin/bash yourusername
passwd yourusername

# Initialize pacman keys and install sudo
pacman-key --init
pacman-key --populate
pacman -Sy archlinux-keyring --noconfirm
pacman -Syu --noconfirm
pacman -S sudo nano --noconfirm

# Automatically enable the wheel group in sudoers (bypasses manual visudo)
sed -i 's/^# %wheel ALL=(ALL:ALL) ALL/%wheel ALL=(ALL:ALL) ALL/' /etc/sudoers

```

Close the Arch terminal, open Windows **PowerShell**, and set the default user:

```powershell
wsl --manage archlinux --set-default-user yourusername

```

---

### Phase 2: The Main Automation Script (Run as your new user)

Create a file named `setup.sh` (`nano setup.sh`), paste the code below, save it, and then run `chmod +x setup.sh` followed by `./setup.sh`.

```bash
#!/bin/bash
set -e # Stops the script immediately if any error occurs

echo "🚀 Starting Arch Linux WSL2 setup..."

# 4. Install base tools and LunarVim dependencies
echo "📦 Installing base packages and dependencies via pacman..."
sudo pacman -S --noconfirm neovim git yarn npm python3 rust base-devel make python cargo ripgrep python-pipx python-pynvim

# 6. Install yay (AUR Helper)
if ! command -v yay &> /dev/null; then
    echo "🛠️ Compiling and installing yay..."
    cd /tmp
    git clone https://aur.archlinux.org/yay.git
    cd yay
    makepkg -si --noconfirm
    cd ~
fi

# 5. Install LunarVim
echo "🌙 Installing LunarVim..."
echo "⚠️ WARNING: When the installer asks, answer [y] for NodeJS and [n] for Python dependencies!"
sleep 3
LV_BRANCH='release-1.4/neovim-0.9' bash <(curl -s https://raw.githubusercontent.com/LunarVim/LunarVim/release-1.4/neovim-0.9/utils/installer/install.sh)

# 7. Install Zsh, Powerlevel10k, and fonts
echo "🐚 Installing Zsh and Powerlevel10k..."
yay -S --noconfirm zsh zsh-theme-powerlevel10k-git ttf-meslo-nerd-font-powerlevel10k powerline-fonts awesome-terminal

# Download zsh-autosuggestions plugin
mkdir -p ~/.zsh
if [ ! -d "$HOME/.zsh/zsh-autosuggestions" ]; then
    git clone https://github.com/zsh-users/zsh-autosuggestions ~/.zsh/zsh-autosuggestions
fi

# 8. Install Rust CLI tools
echo "🦀 Installing Rust tools via cargo (This might take a while)..."
cargo install bat exa procs dust tokei ytop tealdeer grex rmesg zoxide delta

# 9. Install asdf
echo "🔄 Installing asdf-vm..."
yay -S --noconfirm asdf-vm

# 10. Install Containers ecosystem (Podman, kubectl, kind)
echo "🐳 Installing Podman, Kind, Kubectl, and Nftables..."
yay -S --noconfirm podman podman-compose podman-docker kubectl kind nftables iptables-nft

echo "⚙️ Configuring Podman Rootless permissions..."
sudo usermod --add-subuids 100000-165535 --add-subgids 100000-165535 $USER
sudo chmod +s /usr/bin/newuidmap /usr/bin/newgidmap
sudo touch /etc/containers/nodocker

echo "🔗 Creating systemd service for Podman shared mount..."
cat <<EOF | sudo tee /etc/systemd/system/podman-shared-mount.service > /dev/null
[Unit]
Description=Make root mount shared for Podman rootless
After=network.target

[Service]
Type=oneshot
ExecStart=/usr/bin/mount --make-rshared /
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
EOF
sudo systemctl enable --now podman-shared-mount.service

echo "🕸️ Configuring Podman network (Firewall bypass in WSL kernel)..."
sudo mkdir -p /etc/containers
cat <<EOF | sudo tee /etc/containers/containers.conf > /dev/null
[network]
firewall_driver = "none"
EOF

# Generate the final .zshrc file
echo "📝 Generating ~/.zshrc file..."
cat << 'EOF' > ~/.zshrc
autoload -Uz compinit
compinit

# Enable Powerlevel10k instant prompt. Should stay close to the top of ~/.zshrc.
if [[ -r "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh" ]]; then
  source "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh"
fi

source /usr/share/zsh-theme-powerlevel10k/powerlevel10k.zsh-theme

# To customize prompt, run `p10k configure` or edit ~/.p10k.zsh.
[[ ! -f ~/.p10k.zsh ]] || source ~/.p10k.zsh

######################################

# LunarVim
export PATH=$HOME/.local/bin:$HOME/.cargo/bin:$PATH

# KIND
export KIND_EXPERIMENTAL_PROVIDER=podman

# zsh plugins
source ~/.zsh/zsh-autosuggestions/zsh-autosuggestions.zsh

alias ls="exa --icons"
alias cat="bat --style=auto"
alias podman="docker"
alias k='kubectl'
source <(kubectl completion zsh)
compdef __start_kubectl k
EOF

# Change default shell (Will prompt for user password)
echo "🔑 Changing default shell to Zsh..."
chsh -s /usr/bin/zsh

echo "✅ Installation successfully completed! Close the terminal and open it again."

```

