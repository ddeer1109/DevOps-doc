# Module 01: DevOps Intro & OS

## Topics

- What is DevOps — philosophy, culture, practices
- Software Development Life Cycle (SDLC)
- Operating system fundamentals
- Linux installation and first steps
- Package managers (apt, yum)

## Notes

### Environment Setup (Exercise 01)

- WSL2 provides a full Linux environment inside Windows — no dual-boot needed.
- Docker Desktop integrates with WSL via a manual GUI toggle (Settings → Resources → WSL Integration).
- An idempotent bootstrap script (`devops-toolbox/wsl/bootstrap.sh`) automates tool installation and Git config — safe to re-run.
- Key tools installed: `unzip`, `htop`, `tree`, `jq` — essential for day-to-day DevOps work.
- Git aliases (`s`, `lg`, `amend`, `undo`) speed up common workflows significantly.
- Separating setup scripts (devops-toolbox) from course notes (this repo) keeps things reusable across projects.

## Exercises

See the [exercises/](exercises/) folder.
