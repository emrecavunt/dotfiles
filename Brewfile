# Brewfile
# Install: brew bundle install --file=Brewfile
# Check:   brew bundle check --file=Brewfile -v
# Check for updates: brew outdated --greedy
# Cleanup: brew bundle cleanup --file=Brewfile --force # Uninstalls any apps not listed in the Brewfile! Caution: review what will be removed before confirming.
# List installed: brew bundle list --file=Brewfile


# ─── Shell & Terminal ──────────────────────────────────────────────────────────
brew "zsh-autosuggestions"
brew "zsh-syntax-highlighting"
brew "starship"               # prompt
brew "tmux"                   # multiplexer — agent session orchestration
brew "mise"                   # version manager — replaces nvm, pyenv, goenv, tfenv

cask "ghostty"                # terminal — native macOS, Claude Code compatible
cask "font-jetbrains-mono-nerd-font"

# ─── CLI Utilities ─────────────────────────────────────────────────────────────
brew "ripgrep"                # grep replacement
brew "bat"                    # cat with syntax highlighting
brew "eza"                    # ls replacement
brew "fd"                     # find replacement
brew "fzf"                    # fuzzy finder
brew "zoxide"                 # cd with memory
brew "atuin"                  # shell history — SQLite, searchable, synced
brew "jq"                     # JSON processor
brew "yq"                     # YAML processor
brew "watch"                  # watch kubectl get pods
brew "tree"                   # directory tree view
brew "chezmoi"                # dotfiles manager
brew "htop"                   # process viewer
brew "fastfetch"              # system info (replaces archived neofetch)
brew "asciinema"              # terminal session recording
brew "mas"                    # Mac App Store CLI

# ─── Git ───────────────────────────────────────────────────────────────────────
brew "git"
brew "gh"                     # GitHub CLI
brew "lazygit"                # terminal Git UI
brew "git-delta"              # diff renderer

# ─── Kubernetes ────────────────────────────────────────────────────────────────
brew "kubectl"
brew "helm"
brew "kustomize"
brew "k9s"
brew "kubectx"                # kubectx + kubens
brew "stern"                  # multi-pod log tailing

cask "orbstack"               # container runtime — Docker Desktop replacement
cask "openlens"               # Kubernetes GUI

# ─── Container Tooling ─────────────────────────────────────────────────────────
brew "dive"                   # Docker image layer explorer
brew "ctop"                   # real-time container metrics

# ─── Cloud ─────────────────────────────────────────────────────────────────────
brew "awscli"
brew "aws-vault"              # credential storage
brew "azure-cli"

cask "gcloud-cli"       # gcloud, gsutil, bq

# ─── Infrastructure ────────────────────────────────────────────────────────────
# terraform + terragrunt managed via mise for per-project pinning:
# mise use --global terraform@latest
# mise use --global terragrunt@latest

brew "sops"                   # secret encryption
brew "age"                    # encryption backend for sops
brew "mkcert"                 # local TLS certificates
brew "tflint"                 # Terraform linter
brew "terraform-docs"         # module documentation generator
brew "infracost"              # Terraform cost estimation

# ─── Networking & Debugging ────────────────────────────────────────────────────
brew "wget"                   # wget -c resume downloads
brew "nmap"                   # network scanner
brew "mtr"                    # traceroute + ping combined
brew "grpcurl"                # gRPC service debugging
brew "httpie"                 # HTTP client — cleaner than curl for API testing
cask "mitmproxy"              # interactive HTTPS proxy for debugging

# ─── AI Tools ──────────────────────────────────────────────────────────────────
# claude-code — use native installer for auto-updates:
# curl -fsSL https://claude.ai/install.sh | bash

brew "gemini-cli"
brew "ollama"                 # local model inference
cask "codex"

# ─── Security Scanning ─────────────────────────────────────────────────────────
brew "trivy"                  # CVE scanner — images, IaC, repos
cask "1password"

# ─── Load Testing ──────────────────────────────────────────────────────────────
brew "k6"                     # load testing
brew "hey"                    # quick HTTP benchmarking

# ─── Editors ───────────────────────────────────────────────────────────────────
cask "zed"
cask "visual-studio-code"

# ─── Productivity ──────────────────────────────────────────────────────────────
cask "raycast"
cask "rectangle"
# cask "arc" 
cask "tableplus"

# ─── Fonts ─────────────────────────────────────────────────────────────────────
cask "font-fira-code"
cask "font-hack-nerd-font"