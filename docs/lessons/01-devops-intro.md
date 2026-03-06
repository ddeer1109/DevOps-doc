# 01 -- DevOps Introduction

**Topics:** DevOps philosophy, SDLC, culture, automation, metrics, environment setup

---

## What is DevOps?

**Development + Operations** -- a set of practices that unifies software development and IT operations. The goal: shorter development cycles, more frequent deployments, higher reliability.

### The problem DevOps solves

Before DevOps, development and operations were separate teams with conflicting goals:

- Developers wanted to ship features fast
- Operations wanted to keep production stable
- Releases happened every few months, ~50% caused incidents

DevOps breaks down these walls with shared ownership of the entire lifecycle.

---

## Core Principles

### 1. Culture of shared responsibility

Every team member:

- Participates in the full product lifecycle (build, deploy, run)
- Has a voice in decisions
- Is accountable for the end result

!!! example "Netflix"
    Netflix eliminated all walls between teams. Result: idea-to-production in hours instead of weeks.

### 2. Automate everything

Manual processes are slow, error-prone, and don't scale. Automate:

- Building and testing (CI)
- Deployment (CD)
- Infrastructure provisioning (IaC)
- Monitoring and alerting

!!! example "Amazon"
    136,000 deployments/day. Average deploy time: 1.6 seconds. Failure rate: 0.001%.

### 3. Measure everything (DORA metrics)

DevOps decisions are data-driven. The four key metrics from the DORA (DevOps Research and Assessment) team:

| Metric | What it measures | Elite target |
|--------|-----------------|--------------|
| **Deployment Frequency** | How often you deploy to production | Multiple times per day |
| **Lead Time for Changes** | Commit to production | Less than 1 hour |
| **MTTR** (Mean Time to Recovery) | How fast you recover from failure | Less than 1 hour |
| **Change Failure Rate** | % of deployments causing incidents | Less than 5% |

### 4. Continuous improvement (Kaizen)

Inspired by the Japanese Kaizen philosophy -- small daily improvements compound into large results.

The cycle: **Plan -> Do -> Check -> Act** (repeat).

---

## DevOps Toolchain Overview

The course will cover each of these areas:

| Area | Tools (examples) | Course module |
|------|-------------------|---------------|
| Version control | Git, GitHub | 04 |
| CI/CD | Jenkins, GitHub Actions | 10 |
| Containers | Docker, Docker Compose | 08 |
| Orchestration | Kubernetes | 12 |
| Config management | Ansible | 07 |
| IaC | Terraform | 12 |
| Monitoring | Prometheus, Grafana, ELK | 12 |
| Cloud | AWS (EC2, S3, IAM, VPC) | 11 |

---

## Environment Setup

### Essential tools

```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install core tools
sudo apt install -y git curl wget unzip htop tree jq

# Install Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker $USER
# Log out and back in for group change to take effect
```

### Git configuration

```bash
# Identity
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Useful aliases
git config --global alias.s "status -s"
git config --global alias.lg "log --oneline --graph --decorate"
git config --global alias.amend "commit --amend --no-edit"
git config --global alias.undo "reset HEAD~1 --mixed"
```

### Oh My Zsh (optional, improves terminal UX)

```bash
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# Autosuggestions plugin
git clone https://github.com/zsh-users/zsh-autosuggestions \
  ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

### Verify setup

```bash
docker run hello-world    # Docker works?
git s                     # Git alias works?
git lg                    # Graph log works?
```

---

## Key Takeaways

- DevOps is a **culture shift** first, tooling second
- The four DORA metrics are the standard way to measure DevOps maturity
- Automate everything that can be automated -- manual = slow + risky
- A well-configured environment saves hours every day

---

## Recommended reading

- "The Phoenix Project" -- Gene Kim (novel about IT transformation)
- "The DevOps Handbook" -- Gene Kim et al. (practical guide)

---

[Next: 02 -- Linux Basics](02-linux-basics.md){ .md-button }
