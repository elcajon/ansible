# GitHub Actions Workflows Documentation

This repository uses two main GitHub Actions workflows to ensure code quality, security, and maintainability for Ansible automation projects.

---

## Workflows Overview

### 1. CI Workflow (`ci.yml`)

**Purpose:**  
Lint, syntax check, and validate all Ansible playbooks and Jinja2 templates across supported Ansible versions.

**Triggers:**  
- Pull requests to `main`
- Pushes to non-main branches
- Manual dispatch

**Jobs:**
- **Lint & Syntax Check (matrix):**
  - Runs for each supported Ansible version (currently 2.16, 2.17)
  - Installs all required Ansible collections
  - Runs `ansible-lint` for best practices and errors
  - Runs `yamllint` for YAML formatting and syntax
  - Performs `ansible-playbook --syntax-check` on all main playbooks
  - Validates all Jinja2 (`.j2`) templates for syntax errors

**Best Practices:**
- Fast feedback: fails early on errors
- Matrix strategy ensures compatibility with multiple Ansible versions
- All dependencies are installed automatically

---

### 2. Security Workflow (`security.yml`)

**Purpose:**  
Comprehensive security scanning for secrets, 1Password reference validation, and static analysis.

**Triggers:**  
- Pull requests to `main`
- Pushes to non-main branches
- Weekly scheduled run (Sunday, 2 AM UTC)
- Manual dispatch

**Jobs:**
- **Secret Scan (TruffleHog):**  
  Scans the entire git history for leaked secrets using TruffleHog.
- **1Password Secret Validation:**  
  Validates all 1Password lookups in YAML files using a service account token (`OP_SERVICE_ACCOUNT_TOKEN`).  
  Also scans for hardcoded secrets using custom patterns.
- **CodeQL Analysis:**  
  Runs static security analysis on Python code (if present) using GitHub CodeQL.

**Requirements:**
- Set the `OP_SERVICE_ACCOUNT_TOKEN` secret in repository settings for 1Password validation.

---

## Secret Management with 1Password

- All secrets should be referenced using the Ansible `community.general.onepassword` lookup.
- The `security.yml` workflow will fail if any referenced item or vault does not exist or is inaccessible.
- Hardcoded secrets (passwords, tokens, keys) are flagged as potential security issues.

**How to configure:**
1. Create a 1Password service account with read access to required vaults.
2. Add the service account token as a repository secret named `OP_SERVICE_ACCOUNT_TOKEN`.

---

## Branch Protection Recommendations

To enforce these workflows:
1. Go to repository **Settings → Branches**.
2. Add a rule for the `main` branch.
3. Enable "Require status checks to pass before merging".
4. Select the following checks:
   - `Lint & Syntax Check`
   - `Secret Scan (TruffleHog)`
   - `1Password Secret Validation`
   - `CodeQL Analysis`

---

## Extending the Workflows

- **Add new Ansible versions:**  
  Edit the `matrix.ansible-version` in `ci.yml`.
- **Add new secret patterns:**  
  Extend the `secret_patterns` list in the Python script in `security.yml`.
- **Add new collections:**  
  Update the collection installation step in `ci.yml`.

---

## Troubleshooting

- **1Password validation fails:**  
  Ensure `OP_SERVICE_ACCOUNT_TOKEN` is set and has access to the required vaults.
- **Collection missing:**  
  Add missing collections to the install step in `ci.yml`.
- **Syntax check failures:**  
  These may indicate missing variables or collections.
- **Secret detection false positives:**  
  Update the patterns in `security.yml` to exclude legitimate usage.

---

## Security Considerations

- Service account tokens should have minimal required permissions.
- Rotate `OP_SERVICE_ACCOUNT_TOKEN` regularly.
- TruffleHog scans include full git history—ensure sensitive data was never committed.
- CodeQL helps identify vulnerabilities in Python code.

---

## File Coverage

The workflows scan:
- `*.yml`, `*.yaml`: Ansible playbooks, roles, vars
- `*.j2`: Jinja2 templates
- Python files: For CodeQL analysis

---

## Questions?

For help with CI or security workflows, open an issue or check the Actions tab for detailed logs.