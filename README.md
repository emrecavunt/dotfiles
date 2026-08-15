# Dotfiles

Chezmoi-managed workstation for macOS. Terminal-first: Ghostty, tmux, Starship, mise, and a Brewfile that can rebuild the machine.

Source of truth: this repo. Destination: `$HOME`.

Companion write-up: [The M5 Pro Setup](https://emrecavunt.com/blog/mac-m5-pro-setup-for-ai).

Package list and mise (nvm/gvm replacement): [Brewfile.md](Brewfile.md).

## Fresh Mac

Do this in order. 1Password first, so SSH and GitHub auth exist before the rest.

```bash
xcode-select --install

/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
eval "$(/opt/homebrew/bin/brew shellenv)"

brew install --cask 1password
# sign in, enable the SSH agent

brew install chezmoi
chezmoi init --apply https://github.com/emrecavunt/dotfiles
```

`chezmoi init --apply` writes the configs, then `run_onchange_after_brew-bundle.sh.tmpl` runs `brew bundle` against the Brewfile in this repo.

Then:

```bash
mise install
curl -fsSL https://claude.ai/install.sh | bash
gh auth login
```

Restore these from 1Password or an encrypted backup. They are not in this repo:

- `~/.ssh/` keys (auth + signing)
- `~/.config/gcloud`
- `~/.aws`
- `~/.kube`

Open Ghostty. If the font is missing, `brew bundle` has not finished the Nerd Font cask yet.

Sanity check:

```bash
ctx
versions
cheat k8s
```

## What chezmoi manages

Chezmoi source names use `dot_` for files that live under `$HOME` as dotfiles.

| Source | Destination |
| --- | --- |
| `dot_zshrc` | `~/.zshrc` |
| `dot_gitconfig` | `~/.gitconfig` |
| `dot_tmux.conf` | `~/.tmux.conf` |
| `dot_config/ghostty/config` | `~/.config/ghostty/config` |
| `dot_config/starship.toml` | `~/.config/starship.toml` |
| `dot_config/mise/config.toml` | `~/.config/mise/config.toml` |
| `Brewfile` | `~/Brewfile` |
| `run_onchange_after_brew-bundle.sh.tmpl` | not written; runs `brew bundle` when the script hash changes |

`README.md` and `Brewfile.md` are ignored by chezmoi. They stay in the repo and are not copied to `$HOME`.

Not managed here: SSH keys, `allowed_signers`, cloud CLIs' login state, kubeconfigs, Claude/Codex tokens.

## Daily use

Source directory on this machine:

```bash
chezmoi source-path
# ~/.local/share/chezmoi
```

```bash
chezmoi diff                  # what apply would change
chezmoi apply                 # write source -> $HOME
chezmoi apply --dry-run -v    # preview
chezmoi doctor                # health check
```

Edit a live file, then pull it back into the repo:

```bash
chezmoi add ~/.zshrc
chezmoi add ~/.tmux.conf
chezmoi add ~/.gitconfig
```

Edit the source and apply:

```bash
chezmoi edit ~/.zshrc
chezmoi apply
```

`chezmoi apply` will stop if a destination file changed since chezmoi last wrote it. That is expected when a tool rewrites its own config (mise often pins `go = "latest"` to a version).

- Source is the truth (usual case): type `overwrite`, or `chezmoi apply --force`.
- Destination is the truth: `chezmoi add ~/.config/mise/config.toml`, then commit.

Do not leave an apply sitting on that prompt. It holds the chezmoi lock and blocks every other chezmoi command.

## Push and pull

This directory is a normal git repo. Remote: `https://github.com/emrecavunt/dotfiles`.

```bash
cd "$(chezmoi source-path)"
git status
git add -A
git commit --author="Emre Cavunt <emre.cavunt@gmail.com>" -m "feat: describe the change"
git push origin main
```

On another machine that already has chezmoi:

```bash
chezmoi update
```

That is `git pull` in the source repo, then `apply`.

## Maintenance

### Packages

The Brewfile is the install list. `run_onchange_after_brew-bundle.sh.tmpl` reruns `brew bundle` only when that script's content changes, not on every apply. After you edit the Brewfile, bump or re-save the script, or run brew yourself:

```bash
brew bundle --file="$(chezmoi source-path)/Brewfile"
brew bundle check --file="$(chezmoi source-path)/Brewfile" -v
brew outdated --greedy
```

Do not run `brew bundle cleanup --force` unless you have read the removal list.

Claude Code is not in the Brewfile. Native installer auto-updates:

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

Terraform and Terragrunt stay under mise, not Homebrew.

### Language runtimes

```bash
mise install
mise ls --current
mise upgrade          # or: mise run up
```

Global tools live in `~/.config/mise/config.toml`. Per-project versions and cloud identity belong in that project's `mise.toml` `[tools]` and `[env]` blocks. Full nvm/gvm examples are in [Brewfile.md](Brewfile.md#mise-replaces-nvm-gvm-pyenv-tfenv).

`uv` is the Python package manager. mise owns the Python version.

### Shell

`cheat` lists tagged aliases. `cheat k8s` filters. `ctx` prints the active Kubernetes, GCP, AWS, Azure, and Terraform context. Use `ctx` before anything destructive.

## Terminal

Ghostty is the terminal. Config is `~/.config/ghostty/config` (that exact name; `config.ghostty` is ignored).

tmux prefix is `Ctrl-a`.

| Key | Action |
| --- | --- |
| `Ctrl-a \|` | split horizontal, keep cwd |
| `Ctrl-a -` | split vertical, keep cwd |
| `Ctrl-a h/j/k/l` | move pane |
| `Option-arrows` | move pane (Ghostty: `macos-option-as-alt`) |
| `Ctrl-a r` | reload `~/.tmux.conf` |
| `Ctrl-a A` | attach or create session `agents` |

## Work vs personal

`.gitconfig` ships the personal identity and SSH signing. `allowed_signers` is left off this repo on purpose. Set it on the machine:

```bash
git config --global gpg.ssh.allowedSignersFile ~/.config/git/allowed_signers
```

For a work laptop, keep this repo and override email with a git `includeIf`, or a second file that is not committed here.

OrbStack is free for personal use. Work use needs a commercial seat.
