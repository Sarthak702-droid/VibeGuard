<p align="center">
  <img src="https://raw.githubusercontent.com/Sarthak702-droid/VibeGaurd/main/assets/logo.png" alt="VibeGuard Logo" width="500px">
</p>

# VibeGuard

> **Guardrails for vibe-coded software.**

VibeGuard is an open-source Python CLI that empowers AI-assisted developers to create cleaner project context, generate targeted prompts, bundle token-aware files, compile verification reports, audit Git diffs, detect risk patterns, and draft follow-up hardening instructions.

It is **not** an autonomous AI coding agent. Its core workflow requires no API key and
operates locally. Prompt refinement through NVIDIA GLM-5.2 is optional and runs only when
you pass `--llm`.

---

## 🎯 Positioning

* **Before AI codes:** Supply context packs and optimized prompt templates to maximize coding accuracy.
* **After AI codes:** Instantly verify changes, review Git diff summaries, and catch potential security or logical regressions.
* **Core benefits:** Save context tokens, catch risky AI-generated changes, and ship code with confidence.

---

## 🚀 Installation

### Global Isolated Installation (Recommended)

Install and run VibeGuard globally across all terminal sessions using `pipx`:

```bash
pipx install vibegaurd-cli
```

To install the latest repository revision instead:

```bash
pipx install git+https://github.com/Sarthak702-droid/VibeGaurd.git
```

### pip

```bash
pip install vibegaurd-cli
```

### Install for Development

Clone the repository and set up an editable installation inside a Python virtual environment:

```bash
git clone https://github.com/Sarthak702-droid/VibeGaurd.git
cd VibeGaurd
python3 -m venv .venv
source .venv/bin/activate
pip install -e ".[dev]"
```

On Windows PowerShell, activate the environment with `.venv\Scripts\Activate.ps1`.
For a runtime-only editable install, use `python -m pip install -e .`.

---

## 💻 CLI Global Usage & Shortcuts

After installation, VibeGuard is accessible globally using either the full command or its short developer alias:

```bash
vibeguard [COMMAND] [OPTIONS]
# or
vbg [COMMAND] [OPTIONS]
```

VibeGuard intentionally does not install a `vg` command because Linux distributions may
already provide that system command. Use `vbg` for the short alias.

### Global Options
* `--version` / `-V`: Show the version and exit.
* `--help`: Show command documentation.

---

## 🔧 Command Reference

| Command | Alias | Description | Key Options |
| :--- | :--- | :--- | :--- |
| `init` | `vbg init` | Initializes a `.vibeguard/` folder with a template config file. | `-p, --project <path>` |
| `doctor` | `vbg doctor` | Diagnoses project health, CLI path issues, and tool chains. | `-p, --project <path>` |
| `scan` | `vbg scan` | Detects technology stacks, frameworks, and important files. | `-p, --project <path>` |
| `context` | `vbg context` | Creates an AI-readable context report based on project files. | `-g, --goal <str>`, `-p <path>`, `-t <tokens>` |
| `plan` | `vbg plan` | Turns a rough concept into a structured implementation plan. | `-g, --goal <str>`, `-p <path>` |
| `prompt` | `vbg prompt` | Generates a prompt locally; optionally refines it with NVIDIA GLM-5.2. | `-g, --goal <str>`, `-p <path>`, `-t <tokens>`, `--llm` |
| `pack` | `vbg pack` | Packages relevant files into a single context file. | `-g, --goal <str>`, `-p <path>`, `-t <tokens>` |
| `verify` | `vbg verify` | Performs automatic checks (lints, test suites, types). | `-p, --project <path>` |
| `diff-explain`| `vbg diff-explain` | Summarizes uncommitted code changes in plain English. | `-p, --project <path>` |
| `risks` | `vbg risks` | Audits changed files for security issues and logic flags. | `-p, --project <path>` |
| `next-prompt` | `vbg next-prompt` | Generates the next best prompt to address risks/failures. | `-p, --project <path>` |
| `all` | `vbg all` | Runs the full VibeGuard end-to-end workflow at once. | `-g, --goal <str>`, `-p <path>`, `-t <tokens>` |

> **Note:** Every command supports a `--no-banner` flag to suppress the terminal startup branding, making automated scripts cleaner.

### Optional NVIDIA GLM-5.2 refinement

Create an NVIDIA API key, store it in the `NVIDIA_API_KEY` environment variable, and
request LLM refinement explicitly:

```bash
# Linux/macOS
export NVIDIA_API_KEY="your-new-rotated-key"
vbg prompt -g "add OTP login" --llm
```

```powershell
# Windows PowerShell (current terminal session)
$env:NVIDIA_API_KEY = "your-new-rotated-key"
vbg prompt -g "add OTP login" --llm
```

The default model is `z-ai/glm-5.2`. Override it only when using another model exposed by
the same NVIDIA NIM endpoint:

```bash
vbg prompt -g "add OTP login" --llm --model z-ai/glm-5.2 --llm-max-tokens 4096
```

Without `--llm`, prompt generation remains fully local. With `--llm`, VibeGuard sends the
generated prompt—which contains project metadata and selected file paths, but not the
selected files' contents—to NVIDIA. Never place API keys in source code, command flags,
`.vibeguard/` outputs, or committed `.env` files.

---

## ⚙️ Core Workflows

### 1. Before Asking AI to Code (Preparing Context)
1. **Initialize** the workspace:
   ```bash
   vbg init
   ```
2. **Scan** the stack and frameworks:
   ```bash
   vbg scan
   ```
3. Generate **context, plan, and pack files**:
   ```bash
   vbg context -g "add OTP login"
   vbg plan -g "add OTP login without changing database schemas"
   vbg pack -g "add OTP login" -t 8000
   ```
4. Copy the generated files from `.vibeguard/` directly into your AI chat session to guide the coding tool.

### 2. After AI Modifies Code (Verifying & Hardening)
1. **Verify** that code compiles and tests pass:
   ```bash
   vbg verify
   ```
2. **Review risks** (detects API keys, security breaches, or altered authentication methods):
   ```bash
   vbg risks
   ```
3. Get an **explanation of changes**:
   ```bash
   vbg diff-explain
   ```
4. Generate the **hardening prompt** to fix any identified failures:
   ```bash
   vbg next-prompt
   ```

---

## 📁 Workspace Layout

All VibeGuard state and outputs are localized within a `.vibeguard/` folder:
```text
.vibeguard/
├── config.yaml          # Project-specific paths, verification steps, and rules
├── context.md           # Bundled context for AI
├── prompt.md            # Target prompt template
├── task.md              # Detailed implementation plan
├── pack.md              # Token-packed code segments
├── cache/               # Scan results cache
└── reports/             # Post-coding audit reports
    ├── diff_report.md
    ├── risk_report.md
    ├── next_prompt.md
    └── verification_report.md
```

### Recommended Git Configuration
Add VibeGuard's generated reports and caches to your `.gitignore` to keep commits clean:
```gitignore
.vibeguard/cache/
.vibeguard/reports/
```

---

## 🛡️ Security Defaults
VibeGuard is designed to be safe-by-default for enterprise workspaces:
* 🔒 **Zero Code Modifications:** Never modifies your source code files directly.
* 🛡️ **No Shell Execution:** Avoids running untrusted code via standard OS shell shells.
* 🔐 **Automated Secret Redaction:** Automatically skips sensitive files like `.env`, private keys (`.pem`), and database configuration variables.
* 🚫 **Safe Scans:** Automatically ignores binary assets, node modules, build targets, and large generated directories.

---

## 🗺️ Project Roadmap

### v0.1.1 (Current)
* Multi-command CLI and `vbg` shortcut setup.
* Project scanner, prompt generator, and risk auditing framework.
* Diagnostics checklist (`vbg doctor`).

### v0.2.0 (Upcoming)
* Custom configuration rules engine for custom team guardrails.
* Refined secret leak detection.
* Integration options for CI workflows (GitHub Actions).
* Structured JSON outputs for automation.

### v0.3.0 (Planned)
* Pull Request audit reporting.
* Framework dependency graphs.
* Dynamic HTML reporting dashboard.

---

## 📦 Package Validation

Run the release checks from an activated development environment:

```bash
python -m pytest
python -m build
python -m twine check dist/*
```

To test the built wheel without changing the development environment:

```bash
python -m venv /tmp/vibeguard-wheel-test
/tmp/vibeguard-wheel-test/bin/python -m pip install dist/*.whl
/tmp/vibeguard-wheel-test/bin/vibeguard --help
/tmp/vibeguard-wheel-test/bin/vbg --help
```

On Windows, use the equivalent executables under
`%TEMP%\vibeguard-wheel-test\Scripts\`.

## 🚢 Publishing a Release

Releases are published by `.github/workflows/publish.yml` when a semantic version tag is
pushed. The workflow checks that the tag matches the package version, builds the wheel and
source distribution, validates both with Twine, and publishes with a PyPI API token stored
as a GitHub environment secret.

### Configure the PyPI token

1. Sign in to PyPI and open **Account settings → API tokens → Add API token**.
2. Because `vibegaurd-cli` has not been published yet, create an account-scoped token for
   the first upload. Copy it once; PyPI will not show it again.
3. In GitHub, open **Settings → Environments → pypi → Environment secrets**.
4. Create a secret named `PYPI_API_TOKEN` and paste the complete token, including its
   `pypi-` prefix.
5. After the first successful upload, delete the account-scoped token, create a token scoped
   only to `vibegaurd-cli`, and replace the GitHub secret with the new token.

The workflow references `${{ secrets.PYPI_API_TOKEN }}`; the token itself must never be
written into `publish.yml` or committed to Git.

After updating and committing the version, publish `0.1.1` with:

```bash
git push origin main
git tag v0.1.1
git push origin v0.1.1
```

PyPI does not allow an existing distribution file or version to be overwritten. Never
reuse `v0.1.0`; increment the version and create a new tag for every release.

