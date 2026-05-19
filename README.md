# ha-hacs-template

[![Validate][validate-badge]][validate-url]
[![HACS Custom][hacs-badge]][hacs-url]
[![Release][release-badge]][release-url]
[![License: MIT][license-badge]][license-url]

[validate-badge]: https://github.com/SashaBusinaro/ha-hacs-template/actions/workflows/validate.yml/badge.svg
[validate-url]: https://github.com/SashaBusinaro/ha-hacs-template/actions/workflows/validate.yml
[hacs-badge]: https://img.shields.io/badge/HACS-Custom-41BDF5?style=for-the-badge&logo=homeassistantcommunitystore&logoColor=white
[hacs-url]: https://www.hacs.xyz/docs/faq/custom_repositories/
[release-badge]: https://img.shields.io/github/v/release/SashaBusinaro/ha-hacs-template?style=for-the-badge&color=blue
[release-url]: https://github.com/SashaBusinaro/ha-hacs-template/releases
[license-badge]: https://img.shields.io/badge/License-MIT-yellow.svg
[license-url]: https://github.com/SashaBusinaro/ha-hacs-template/blob/main/LICENSE

A production-ready GitHub template for building **HACS-compatible Home Assistant custom integrations** — CI/CD, automated releases, pre-commit hooks and Dependabot pre-configured out of the box.

> Based on the excellent [integration_blueprint](https://github.com/ludeeus/integration_blueprint) by [@ludeeus](https://github.com/ludeeus).

---

## What's included

| Tool | What it does |
|---|---|
| **hassfest** | Validates `manifest.json`, translations and component structure on every push |
| **HACS validation** | Checks that the integration meets [HACS requirements](https://hacs.xyz/docs/publish/requirements) |
| **Ruff** (CI) | Lint and format check on every push and PR |
| **Pre-commit** | Ruff + JSON/YAML/whitespace checks before every local commit |
| **release-please** | Opens a Release PR automatically on every Conventional Commit merged to `main`; bumps `manifest.json` version and generates `CHANGELOG.md` |
| **Dependabot** | Weekly grouped PRs for GitHub Actions and Python dev-deps |
| **devcontainer** | One-click Home Assistant dev environment in VS Code |

---

## File structure

| File / Directory | Purpose |
|---|---|
| `.devcontainer.json` | VS Code dev container — live HA instance for testing |
| `.github/dependabot.yml` | Automated dependency updates (weekly, grouped) |
| `.github/ISSUE_TEMPLATE/*.yml` | Bug report and feature request templates |
| `.github/workflows/lint.yml` | Ruff lint + format check |
| `.github/workflows/validate.yml` | hassfest and HACS validation |
| `.github/workflows/release-please.yml` | Automated releases via Conventional Commits |
| `.pre-commit-config.yaml` | Pre-commit hooks (JSON/YAML checks + Ruff) |
| `.ruff.toml` | Ruff configuration (aligned with HA Core) |
| `release-please-config.json` | release-please configuration |
| `.release-please-manifest.json` | Current version tracking for release-please |
| `CHANGELOG.md` | Auto-generated changelog (managed by release-please) |
| `custom_components/integration_blueprint/` | Integration source — rename to your domain |
| `config/configuration.yaml` | HA config used by the devcontainer |
| `scripts/develop` | Start the HA dev server |
| `requirements.txt` | Dev / lint Python dependencies |
| `CONTRIBUTING.md` | Contribution guidelines |

---

## How to use this template

### 1. Create your repository

Click **"Use this template" → "Create a new repository"** on GitHub.

> **Required GitHub setting**: go to **Settings → Actions → General** and enable
> **"Allow GitHub Actions to create and approve pull requests"** — this is needed
> for release-please to open Release PRs automatically.

### 2. Clone and install pre-commit

```bash
git clone git@github.com:<your-user>/<your-repo>.git
cd <your-repo>

pip install pre-commit
pre-commit install
```

Pre-commit will now run automatically before every `git commit`.

### 3. Rename the integration domain

Replace every occurrence of `integration_blueprint` with your integration's domain
(e.g. `my_awesome_integration`) and rename the directory:

```bash
# macOS
find . -not -path './.git/*' -type f \
  | xargs grep -l "integration_blueprint" \
  | xargs sed -i '' 's/integration_blueprint/my_awesome_integration/g'

mv custom_components/integration_blueprint \
   custom_components/my_awesome_integration
```

```bash
# Linux
find . -not -path './.git/*' -type f \
  | xargs grep -l "integration_blueprint" \
  | xargs sed -i 's/integration_blueprint/my_awesome_integration/g'

mv custom_components/integration_blueprint \
   custom_components/my_awesome_integration
```

### 4. Update manifest.json

Edit `custom_components/<your_domain>/manifest.json` and fill in:

```json
{
  "domain": "my_awesome_integration",
  "name": "My Awesome Integration",
  "codeowners": ["@your-github-username"],
  "documentation": "https://github.com/your-user/your-repo",
  "issue_tracker": "https://github.com/your-user/your-repo/issues",
  "version": "0.1.0"
}
```

Also update `release-please-config.json` — change the `path` under `extra-files` to point
to your new domain:

```json
"path": "custom_components/my_awesome_integration/manifest.json"
```

### 5. Start developing

Open the repository in VS Code and choose
**"Dev Containers: Reopen in Container"** to get a full Home Assistant instance running
locally with your integration already loaded.

Or run the dev server directly:

```bash
scripts/develop
```

---

## Pre-commit

Pre-commit runs a set of fast checks before every `git commit`, catching issues locally
before they reach CI.

### Hooks included

| Hook | What it checks |
|---|---|
| `check-json` | Validates `manifest.json`, `translations/*.json`, `hacs.json` |
| `check-yaml` | Validates all workflow and config `.yml` files |
| `trailing-whitespace` | Removes trailing spaces |
| `end-of-file-fixer` | Ensures files end with a newline |
| `check-merge-conflict` | Blocks accidental merge-conflict markers |
| `ruff` | Lints Python and auto-fixes safe issues |
| `ruff-format` | Formats Python code |

### Common commands

```bash
# Install hooks (once per clone)
pip install pre-commit
pre-commit install

# Run manually on all files
pre-commit run --all-files

# Run a single hook
pre-commit run ruff --all-files

# Update hooks to latest versions
pre-commit autoupdate
```

> The Ruff version in `.pre-commit-config.yaml` is pinned to match `requirements.txt`.
> When bumping Ruff via Dependabot, update both files together.

---

## Releases (release-please)

This template uses [release-please](https://github.com/googleapis/release-please)
to automate changelogs and GitHub Releases — no manual tagging required.

### How it works

1. Merge commits to `main` following the [Conventional Commits](https://www.conventionalcommits.org/) spec.
2. release-please opens (or updates) a **Release PR** that:
   - Bumps the version in `manifest.json`
   - Generates a `CHANGELOG.md` entry
3. Merge the Release PR → a GitHub Release and git tag are created automatically.

### Conventional Commits quick reference

| Commit prefix | Version bump | Appears in changelog |
|---|---|---|
| `feat: ...` | minor | Yes — **Features** |
| `fix: ...` | patch | Yes — **Bug Fixes** |
| `perf: ...` | patch | Yes — **Performance** |
| `feat!: ...` or `BREAKING CHANGE:` in footer | major | Yes |
| `refactor: ...` | none | Hidden |
| `chore: ...` | none | Hidden |
| `docs: ...` | none | Hidden |
| `ci: ...` | none | Hidden |

**Examples:**

```
feat: add sensor for battery level
fix: correct unit of measurement for weight sensor
feat!: drop support for HA < 2025.1
chore(deps): update ruff to v0.12.0
```

### Pre-major versioning

While the version is below `1.0.0`:
- `feat:` bumps the **patch** (e.g. `0.1.0` → `0.1.1`)
- `feat!:` bumps the **minor** (e.g. `0.1.0` → `0.2.0`)

This matches the `bump-minor-pre-major` / `bump-patch-for-minor-pre-major` flags
set in `release-please-config.json`. Remove them once you reach `1.0.0`.

---

## Dependabot

Dependabot opens **weekly grouped PRs** (every Monday) for:

| Ecosystem | PR strategy |
|---|---|
| GitHub Actions | Minor + patch versions bundled into one PR; major updates separate |
| Python (pip) | Minor + patch bundled; patch PRs wait 3 days, minor PRs 7 days before opening |
| Dev containers | Weekly, ungrouped |

`homeassistant` is excluded from automatic updates — it must stay in sync with
the `homeassistant` key in `hacs.json`. Bump them together manually.

---

## Next steps

- Add **tests** using [`pytest-homeassistant-custom-component`](https://github.com/MatthewFlamm/pytest-homeassistant-custom-component)
- Add **brand images** (logo/icon) to `custom_components/<domain>/brand/`
- Publish to HACS as a [Custom Repository](https://www.hacs.xyz/docs/faq/custom_repositories/)
  or submit to the [HACS default store](https://hacs.xyz/docs/publish/start)
- Share on the [Home Assistant Community Forum](https://community.home-assistant.io/)

## License

MIT — see [LICENSE](LICENSE).
