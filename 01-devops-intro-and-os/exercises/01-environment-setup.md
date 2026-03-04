# Exercise 01: Environment Setup

## Objective

Set up a professional DevOps development environment using WSL2 on Windows 11, with essential CLI tools, Git configuration, and Docker integration.

## Prerequisites

- Windows 11 with WSL2 + Ubuntu installed
- Docker Desktop installed on Windows
- Git available inside WSL

## Steps

### Level 1 — Windows Host

1. **Verify WSL2 is running** (PowerShell):
   ```powershell
   wsl -l -v
   ```
   Confirm Ubuntu is listed with VERSION 2.

2. **Enable Docker Desktop WSL Integration** (manual GUI step):
   - Docker Desktop → Settings → Resources → WSL Integration
   - Enable for the default distro and Ubuntu
   - Apply & Restart

### Level 2 — Inside WSL

3. **Run the bootstrap script** from [devops-toolbox](https://github.com/ddeer1109/devops-toolbox):
   ```bash
   git clone https://github.com/ddeer1109/devops-toolbox.git ~/devops-toolbox
   chmod +x ~/devops-toolbox/wsl/bootstrap.sh
   ~/devops-toolbox/wsl/bootstrap.sh
   ```

   The script handles:
   - System package updates (`apt update && upgrade`)
   - Installing missing tools: `unzip`, `htop`, `tree`, `jq`
   - Git global configuration (identity, aliases, defaults)

4. **(Optional) Install Zsh** — for a better interactive shell on your workstation:
   ```bash
   ~/devops-toolbox/wsl/bootstrap.sh --with-zsh
   ```
   This adds Zsh, Oh My Zsh, and zsh-autosuggestions. Skip this on servers/containers where Bash is standard.

5. **Verify Docker** (after enabling WSL integration in step 2):
   ```bash
   docker run hello-world
   ```

## Verification Checklist

After completing all steps, verify each item:

- [ ] `wsl -l -v` shows Ubuntu VERSION 2
- [ ] `unzip --version` — unzip available
- [ ] `htop --version` — htop available
- [ ] `tree --version` — tree available
- [ ] `jq --version` — jq available
- [ ] `git config user.name` → Daniel
- [ ] `git config user.email` → dmwator11@gmail.com
- [ ] `git s` — alias works (status -sb)
- [ ] `git lg` — alias works (pretty log)
- [ ] `docker run hello-world` — Docker works from WSL

**If `--with-zsh` was used:**

- [ ] `zsh --version` — Zsh installed
- [ ] `echo $SHELL` — shows `/usr/bin/zsh` (or `/bin/zsh`)
- [ ] Zsh autosuggestions appear when typing (gray ghost text)

## Tools & Scripts Used

| Tool | Source |
|------|--------|
| `wsl/bootstrap.sh` | [devops-toolbox/wsl/bootstrap.sh](https://github.com/ddeer1109/devops-toolbox/blob/main/wsl/bootstrap.sh) |
| `.gitconfig-template` | [devops-toolbox/dotfiles/.gitconfig-template](https://github.com/ddeer1109/devops-toolbox/blob/main/dotfiles/.gitconfig-template) |
| WSL setup guide | [devops-toolbox/wsl/README.md](https://github.com/ddeer1109/devops-toolbox/blob/main/wsl/README.md) |

## Notes

- The bootstrap script is idempotent — running it again skips already-completed steps.
- Docker CLI works inside WSL only after enabling Docker Desktop's WSL integration (Level 1, step 2).
- The swap disk warning (`C:\temp` missing) is non-critical and can be ignored.
