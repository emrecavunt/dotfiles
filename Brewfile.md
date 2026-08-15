# Brewfile

Install list for this workstation. Homebrew owns packages. [mise](https://mise.jdx.dev/) owns language and CLI versions. Chezmoi owns the files.

`brew bundle` is built into Homebrew. Do not tap `homebrew/bundle`.

```bash
brew bundle --file="$(chezmoi source-path)/Brewfile"
brew bundle check --file="$(chezmoi source-path)/Brewfile" -v
brew outdated --greedy
```

`run_onchange_after_brew-bundle.sh.tmpl` runs the first command when that script changes. After you edit this Brewfile, run `brew bundle` yourself or touch the script so chezmoi reruns it.

`brew bundle cleanup --force` uninstalls anything not listed. Read the removal list first.

## What stays out of the Brewfile

| Tool | Why | Install |
| --- | --- | --- |
| [Claude Code](https://code.claude.com/docs/en/install) | Native installer auto-updates. Homebrew and npm do not. | `curl -fsSL https://claude.ai/install.sh \| bash` |
| Terraform, Terragrunt | Version-sensitive. Pin per repo. | `mise use --global terraform@latest` (already in mise config) |
| nvm, gvm, goenv, pyenv, tfenv | Replaced by mise. | Do not install |

## mise (replaces nvm, gvm, pyenv, tfenv)

[mise](https://mise.jdx.dev/) is one version manager for Go, Node, Python, Terraform, kubectl, and the rest. It also sets environment variables when you `cd` into a repo.

Homebrew installs the binary. The versions live in `~/.config/mise/config.toml` (global) and `mise.toml` (per repo).

`.zshrc` already runs `eval "$(mise activate zsh)"`. After that, `cd` is enough. No `nvm use`, no `gvm use`, no `pyenv shell`.

### Global defaults

These are the machine-wide versions from `dot_config/mise/config.toml`:

```toml
[tools]
go = "latest"
kubectl = "latest"
node = "lts"
python = "3.13"
terraform = "latest"
terragrunt = "latest"
uv = "latest"
ruff = "latest"
pnpm = "latest"
```

```bash
mise install          # download whatever the current directory asks for
mise ls --current     # what this shell will actually run
mise upgrade          # or: mise run up
which go node python terraform
```

`go = "latest"` means "track latest". mise may rewrite the live file to a pin such as `1.26.6`. That is the chezmoi conflict you will see. Keep `latest` in the repo. Overwrite the destination on apply.

### Per repo (this is the nvm / gvm replacement)

In the project root:

```bash
cd ~/Projects/some-service
mise use go@1.23
mise use node@22
mise use terraform@1.9
```

That writes `mise.toml` (or `.mise.toml`) in the repo. Commit it. Everyone who uses mise gets the same versions on `cd`.

```toml
# mise.toml in a repo
[tools]
go = "1.23"
node = "22"
python = "3.13"
terraform = "1.9"
kubectl = "1.31"

[env]
CLOUDSDK_ACTIVE_CONFIG_NAME = "my-ai-cluster-dev"
AWS_PROFILE = "personal"
KUBECONFIG = "~/.kube/personal.yaml"
```

`[env]` is why mise is more than a version manager. Changing directory can switch language versions **and** cloud identity. Run `ctx` after `cd` before you apply anything.

mise also reads legacy files if you want a softer migration: `.nvmrc`, `.node-version`, `.python-version`, `.go-version`, `.terraform-version`, `.tool-versions` (asdf). Prefer `mise.toml` for new repos.

### Everyday commands

| Old habit | mise |
| --- | --- |
| `nvm install 22 && nvm use 22` | `mise use node@22` |
| `gvm install go1.23 && gvm use go1.23` | `mise use go@1.23` |
| `pyenv install 3.13 && pyenv local 3.13` | `mise use python@3.13` |
| `tfenv use 1.9.0` | `mise use terraform@1.9` |
| `nvm alias default 22` | `mise use --global node@lts` |

```bash
mise use go@1.23              # pin this repo, write mise.toml
mise use --global go@latest   # change the machine default
mise install                  # fetch missing versions
mise ls                       # installed
mise ls-remote go             # what you can pin
mise exec -- go version       # run one command in the mise env
```

### Python: mise vs uv

mise owns the **interpreter version**. [uv](https://docs.astral.sh/uv/) owns packages, lockfiles, and venvs.

```bash
mise use python@3.13
uv init
uv add httpx
uv run python main.py
```

Do not let uv download its own Python unless you intend that. If both fight, pin Python in `mise.toml` and run `uv` from that shell.

## Tool list

Links are official docs or project homes.

### Shell and terminal

| Formula / cask | What it is |
| --- | --- |
| [mise](https://mise.jdx.dev/) | Versions + env. Replaces nvm, gvm, pyenv, tfenv. |
| [starship](https://starship.rs/) | Prompt. Cloud and k8s context on the right. |
| [tmux](https://github.com/tmux/tmux/wiki) | Agent panes. Prefix `Ctrl-a`. See the root README. |
| [ghostty](https://ghostty.org/) | Terminal. Config must be `~/.config/ghostty/config`. |
| [zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions) | Fish-style suggestions. |
| [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting) | Command colouring. Load after autosuggestions. |
| [font-jetbrains-mono-nerd-font](https://www.nerdfonts.com/) | Prompt and tmux icons. |

### Everyday CLI

| Formula | Replaces | Docs |
| --- | --- | --- |
| [ripgrep](https://github.com/BurntSushi/ripgrep) | grep | `rg` |
| [bat](https://github.com/sharkdp/bat) | cat | syntax highlighting |
| [eza](https://eza.rocks/) | ls | git-aware listing |
| [fd](https://github.com/sharkdp/fd) | find | |
| [fzf](https://github.com/junegunn/fzf) | | fuzzy finder, `cheat` companions |
| [zoxide](https://github.com/ajeetdsouza/zoxide) | cd | `z` / aliased as `cd` |
| [atuin](https://docs.atuin.sh/) | history | SQLite, searchable |
| [jq](https://jqlang.github.io/jq/) | | JSON |
| [yq](https://mikefarah.gitbook.io/yq/) | | YAML |
| [chezmoi](https://www.chezmoi.io/) | | this repo |
| [fastfetch](https://github.com/fastfetch-cli/fastfetch) | neofetch | |
| [mas](https://github.com/mas-cli/mas) | | Mac App Store CLI |

### Git

| Formula | Docs |
| --- | --- |
| [git](https://git-scm.com/doc) | Homebrew git, not the Xcode stub |
| [gh](https://cli.github.com/) | PRs, auth, checks |
| [lazygit](https://github.com/jesseduffield/lazygit) | TUI. Alias `lg` |
| [git-delta](https://github.com/dandavison/delta) | pager in `.gitconfig` |

### Kubernetes and containers

| Formula / cask | Docs |
| --- | --- |
| [kubectl](https://kubernetes.io/docs/reference/kubectl/) | Also pinable in mise per cluster version |
| [helm](https://helm.sh/docs/) | |
| [kustomize](https://kustomize.io/) | |
| [k9s](https://k9scli.io/) | cluster TUI |
| [kubectx](https://github.com/ahmetb/kubectx) | `kc` / `kn` |
| [stern](https://github.com/stern/stern) | multi-pod logs |
| [orbstack](https://orbstack.dev/) | Docker-compatible runtime. Commercial seat for work. |
| [openlens](https://github.com/MuhammedKalkan/OpenLens) | k8s GUI |
| [dive](https://github.com/wagoodman/dive) | image layers |
| [ctop](https://github.com/bcicen/ctop) | container metrics |

### Cloud and IaC

| Formula / cask | Docs |
| --- | --- |
| [awscli](https://docs.aws.amazon.com/cli/) | |
| [aws-vault](https://github.com/ByteNess/aws-vault) | keychain-backed AWS creds. SSO shops can use `aws sso login` instead. |
| [azure-cli](https://learn.microsoft.com/en-us/cli/azure/) | |
| [gcloud-cli](https://cloud.google.com/sdk/docs) | cask. `gcloud`, `gsutil`, `bq` |
| [sops](https://github.com/getsops/sops) | encrypted secrets |
| [age](https://github.com/FiloSottile/age) | sops backend |
| [mkcert](https://github.com/FiloSottile/mkcert) | local TLS |
| [tflint](https://github.com/terraform-linters/tflint) | |
| [terraform-docs](https://terraform-docs.io/) | |
| [infracost](https://www.infracost.io/) | plan cost |

Terraform and Terragrunt: mise, not this file.

### AI

| Formula / cask | Docs |
| --- | --- |
| [ollama](https://ollama.com/) | local models. On 32GB+ Apple Silicon, prefer MLX-tagged / safetensors models. |
| [codex](https://github.com/openai/codex) | OpenAI agent CLI |
| [gemini-cli](https://github.com/google-gemini/gemini-cli) | Gemini agent CLI |

Claude Code: native installer, see the table at the top.

### Editors and desktop

| Cask | Docs |
| --- | --- |
| [zed](https://zed.dev/) | default editor |
| [visual-studio-code](https://code.visualstudio.com/) | when an extension forces it |
| [1password](https://developer.1password.com/docs/ssh/) | passwords + SSH agent |
| [raycast](https://www.raycast.com/) | launcher |
| [rectangle](https://rectangleapp.com/) | window tiling |
| [tableplus](https://tableplus.com/) | SQL GUI |

Arc is commented out. It is in maintenance. Do not uncomment it on a new machine.

### Security, net, load

| Formula / cask | Docs |
| --- | --- |
| [trivy](https://trivy.dev/) | image, IaC, and secret scan |
| [httpie](https://httpie.io/) | API client |
| [mitmproxy](https://mitmproxy.org/) | HTTPS debug proxy |
| [grpcurl](https://github.com/fullstorydev/grpcurl) | gRPC |
| [k6](https://grafana.com/docs/k6/) | load tests |
| [hey](https://github.com/rakyll/hey) | quick HTTP bench |
| [nmap](https://nmap.org/) | |
| [mtr](https://www.bitwizard.nl/mtr/) | traceroute + ping |

## Add or remove a package

1. Edit `Brewfile` in the chezmoi source.
2. `brew bundle --file="$(chezmoi source-path)/Brewfile"`
3. Commit. Mention the tool in this file if it is not obvious.

Pin versions in mise, not in the Brewfile. Homebrew tracks current formulae. mise tracks the version the repo needs.
