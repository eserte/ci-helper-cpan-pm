# AGENTS.md

## Context
This repository contains a cross-platform Perl script with significant CI logic implemented in GitHub Actions workflows.

Workflows are as important as application code and must be treated as part of the system design.

---

## Critical CI / Workflow Constraints

### 1. Environment variables in cross-platform-actions
When using `cross-platform-actions/action` (or similar multi-OS actions):

- Every environment variable used must be explicitly declared in the action step
- Do NOT assume environment variables are inherited from the runner or previous steps
- If a variable is used in any script block, ensure it is explicitly passed via `env:`

Failure to do this will break Windows/macOS/Linux parity.

---

### 2. Windows shell behavior (whitespace + splitting)
Be strict about Windows differences:

- Do NOT assume Unix shell word splitting rules
- Windows `cmd` and PowerShell handle whitespace differently than `bash`
- Always treat quoted strings carefully; avoid implicit splitting
- Prefer explicit argument arrays over string-based command construction where possible

If unsure, prefer correctness over compact shell expressions.

---

### 3. Workflow failure interpretation
Do NOT assume that CI failures are caused by repository changes.

Important rule:
- Most CI failures may be due to test failures in the CPAN module or upstream dependencies
- Only attempt to modify workflow files if there is strong evidence the failure is caused by:
  - syntax errors in workflow YAML
  - invalid GitHub Actions configuration
  - incorrect action usage introduced in recent changes

Otherwise:
- Prefer investigating test/module failures instead of editing workflows
- Do NOT “fix” CI by masking failures or weakening tests

---

### 4. Minimality of changes
When modifying code or workflows:

- Prefer minimal diffs
- Do NOT introduce additional abstraction or conditional logic unless required
- Avoid “defensive generalization” (e.g. adding platform checks without a demonstrated need)
- Keep workflow logic explicit and readable

---

### 5. Shell availability assumptions
Do NOT assume `bash` is available everywhere.

- Some environments only provide `sh`
- Use POSIX-compliant shell syntax when portability matters
- If bash-specific features are required, explicitly set `shell: bash`
- Never rely on bash-isms in default shell contexts

---

## General Principle
When in doubt:
- preserve existing behavior
- do not over-optimize
- prefer explicit cross-platform correctness over convenience
