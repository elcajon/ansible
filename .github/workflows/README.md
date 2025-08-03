# GitHub Actions Workflows Documentation

This repository includes three GitHub Actions workflows for automated code quality and security checks for Ansible scripts.

## Workflows Overview

### 1. Lint Workflow (`lint.yml`)

**Purpose**: Ensures Ansible code quality and YAML formatting standards.

**Triggers**: 
- Pull requests to `main` branch
- Pushes to non-main branches

**Features**:
- Tests against multiple Ansible versions (2.16, 2.17) using matrix strategy
- Runs `ansible-lint` to check Ansible best practices and errors
- Validates YAML syntax with `yamllint`
- Separate job for general YAML linting

**Requirements**: None (installs dependencies automatically)

### 2. Test Workflow (`test.yml`)

**Purpose**: Validates Ansible playbook syntax and Jinja2 templates.

**Triggers**:
- Pull requests to `main` branch  
- Pushes to non-main branches

**Features**:
- Tests against multiple Ansible versions (2.16, 2.17) using matrix strategy
- Installs required Ansible collections (community.general, hetzner.hcloud, artis3n.tailscale)
- Performs syntax checks on all main playbooks
- Validates Jinja2 template syntax (`.j2` files)

**Requirements**: None (installs dependencies automatically)

### 3. Security Scan Workflow (`secret-scan.yml`)

**Purpose**: Comprehensive security scanning for secrets and vulnerabilities.

**Triggers**:
- Pull requests to `main` branch
- Pushes to non-main branches
- Weekly scheduled runs (Sundays at 2 AM UTC)

**Features**:
- **TruffleHog OSS**: Scans for leaked secrets across repository history
- **1Password Integration**: Validates 1Password secret references in Ansible variables
- **CodeQL Analysis**: Static security analysis for Python code
- **Hardcoded Secret Detection**: Custom patterns for common secret formats

**Requirements**:
- `OP_SERVICE_ACCOUNT_TOKEN` secret configured in repository settings

## Secret Management with 1Password

### OP_SERVICE_ACCOUNT_TOKEN Configuration

The repository uses 1Password for secure secret management. To enable 1Password secret validation:

1. **Create a 1Password Service Account**:
   - Go to your 1Password account settings
   - Create a new service account with read access to the required vaults
   - Generate a service account token

2. **Configure GitHub Secret**:
   - Go to repository Settings → Secrets and variables → Actions  
   - Add new repository secret named `OP_SERVICE_ACCOUNT_TOKEN`
   - Paste the 1Password service account token as the value

3. **Usage in Ansible**:
   ```yaml
   vars:
     secret_value: "{{ lookup('community.general.onepassword', 'Item Name', field='field', vault='Vault Name') }}"
   ```

### Validated 1Password References

The workflow automatically validates that all 1Password lookups in your Ansible files reference valid items and vaults:

- ✅ Validates item exists in specified vault
- ✅ Checks vault accessibility with service account
- ✅ Reports missing or inaccessible references
- ⚠️ Scans for potentially hardcoded secrets

## Workflow Requirements

### Branch Protection Rules

To enforce these workflows, configure branch protection rules:

1. Go to repository Settings → Branches
2. Add rule for `main` branch
3. Enable "Require status checks to pass before merging"
4. Select required checks:
   - `Ansible Lint`
   - `YAML Lint` 
   - `Ansible Syntax Check`
   - `Secret Detection`
   - `1Password Secret Validation`
   - `CodeQL Analysis`

### File Coverage

The workflows scan these file types:
- `*.yml`, `*.yaml`: Ansible playbooks, roles, vars
- `*.j2`: Jinja2 templates
- Python files: For CodeQL analysis

### Matrix Strategy

Both lint and test workflows use matrix strategy to test against multiple Ansible versions:
- Ansible Core 2.16.x
- Ansible Core 2.17.x

This ensures compatibility across supported Ansible versions.

## Extending the Workflows

### Adding New Ansible Versions

Update the matrix in both `lint.yml` and `test.yml`:

```yaml
strategy:
  matrix:
    ansible-version: ['2.16', '2.17', '2.18']  # Add new version
```

### Adding New Secret Patterns

Extend the secret detection patterns in `secret-scan.yml`:

```python
secret_patterns = [
    (r'api_key\s*:\s*["\'][A-Za-z0-9]{32,}["\']', 'API Key'),
    # Add new patterns here
]
```

### Adding New Collections

Update the collection installation in `test.yml`:

```yaml
- name: Install required Ansible collections
  run: |
    ansible-galaxy collection install community.general hetzner.hcloud
    ansible-galaxy install artis3n.tailscale
    # Add new collections here
```

## Troubleshooting

### Common Issues

1. **1Password Validation Fails**: Ensure `OP_SERVICE_ACCOUNT_TOKEN` is set and has access to the required vaults

2. **Ansible Collection Missing**: Add missing collections to the installation step in workflows

3. **Syntax Check Failures**: These may indicate missing variables or collections - this is expected for playbooks that require external dependencies

4. **Secret Detection False Positives**: Update the patterns to exclude legitimate usage patterns

### Debugging Workflows

- Check the Actions tab for detailed logs
- Workflow runs show individual step outputs
- Matrix builds show results across different Ansible versions
- Secret validation provides detailed feedback on each 1Password reference

## Security Considerations

- Service account tokens should have minimal required permissions
- Regular rotation of `OP_SERVICE_ACCOUNT_TOKEN` is recommended
- TruffleHog scans include full git history - ensure sensitive data was never committed
- CodeQL analysis helps identify security vulnerabilities in Python code