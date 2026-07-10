# Contributing

## Getting started

```bash
git clone git@github.com:rkristelijn/ROZ-nanobots-for-your-pc-.git
cd ROZ-nanobots-for-your-pc-
make install   # install git hooks
```

Requirements: `python3`, `ruff`, `mypy`, `gitleaks`, `semgrep` (via pipx).

```bash
pip install ruff mypy
pipx install semgrep
# gitleaks: https://github.com/gitleaks/gitleaks#installing
```

## Quality checks

```bash
make lint    # ruff + mypy
make check   # lint + semgrep + gitleaks
make test    # run tests
make ci      # test + check (full pipeline, same as GitHub Actions)
```

Checks:

1. **ruff** — linting, import sorting, security, complexity (≤10)
2. **mypy** — type checking
3. **semgrep** — security scan
4. **gitleaks** — secret scanning

## Workflow

```text
main (protected — no direct commits)
  └── feat/<name>
        └── PR → CI → merge
```

1. Create a branch: `git checkout -b feat/my-change`
2. Make changes
3. Run `make ci` locally before pushing
4. Open a PR — CI runs automatically
5. Bump `VERSION` before merging to main (CI enforces this)

## Why so many checks?

This project is built with AI assistance. AI generates plausible-looking code that can be subtly wrong, unnecessarily complex, or insecure. The checks enforce a baseline that keeps the code maintainable regardless of who (or what) wrote it:

| Check | Prevents |
|-------|---------|
| ruff | Style issues, unused imports, security anti-patterns |
| mypy | Type errors |
| semgrep | Security anti-patterns |
| gitleaks | Accidentally committed secrets |
