# NVR Data Science CI/CD Workflows

This directory contains GitHub Actions workflows for the NVR Data Science team's Fabric CI/CD automation.

## 🚀 Available Workflows

### 1. **PR Validation** (`pr-validation.yml`)
**Trigger:** Pull request to `main` or `develop`

**Purpose:** Automated code quality and validation checks

**What it does:**
- ✅ Python code formatting (Black)
- ✅ Linting (Flake8)
- ✅ Notebook validation
- ✅ Pipeline JSON schema validation
- ✅ Unit tests
- ✅ Configuration file validation
- ✅ Secret scanning

**Example:**
```bash
# Triggered automatically when creating a PR
```

---

### 2. **Deploy to Fabric** (`deploy-fabric.yml`)
**Trigger:** 
- Push to `develop` → Deploys to DEV
- Push to `main` → Deploys to TEST
- Manual trigger → Deploy to any environment

**Purpose:** Automated deployment to Microsoft Fabric workspaces

**What it does:**
- 🔄 Backs up current workspace
- 🚀 Deploys semantic models, reports, notebooks, pipelines
- ✅ Validates deployment
- 🧪 Runs smoke tests
- 🔗 Runs integration tests (TEST only)
- 🔙 Auto-rollback on failure

**Manual Trigger:**
```bash
# Go to Actions → Deploy to Fabric → Run workflow
# Select environment: dev/test/prod
```

**Environments:**
- **DEV** - Development environment for testing
- **TEST** - Testing environment for validation
- **PROD** - Production environment (requires approval)

---

### 3. **Data Quality Monitoring** (`data-quality-check.yml`)
**Trigger:** 
- Scheduled: Weekdays at 6 AM UTC
- Manual trigger

**Purpose:** Automated data quality checks and monitoring

**What it does:**
- 📊 Row count validation
- 🔍 Null value percentage checks
- ⏰ Data freshness verification
- 📈 Schema validation
- 🚨 Alerts on quality issues
- 📑 Generates quality reports

**Manual Trigger:**
```bash
# Go to Actions → Data Quality Monitoring → Run workflow
# Select environment to check
```

---

### 4. **Scheduled Model Training** (`scheduled-model-training.yml`)
**Trigger:** 
- Scheduled: Every Sunday at 2 AM UTC
- Manual trigger

**Purpose:** Automated ML model training and deployment

**What it does:**
- 📥 Executes data ingestion notebook
- 🤖 Runs model training notebook
- ✅ Validates model performance
- 🚀 Deploys model to Fabric
- 📝 Updates model registry
- 📊 Generates performance reports

**Manual Trigger:**
```bash
# Go to Actions → Scheduled Model Training → Run workflow
# Check "Force retrain" to bypass schedule
```

---

## 🔐 Required Secrets

Configure these secrets in GitHub Settings → Secrets and variables → Actions:

### Azure Authentication
- `AZURE_CLIENT_ID` - Service principal client ID
- `AZURE_CLIENT_SECRET` - Service principal secret
- `AZURE_TENANT_ID` - Azure tenant ID

### Fabric Workspaces
- `FABRIC_WORKSPACE_ID_DEV` - DEV workspace ID
- `FABRIC_WORKSPACE_ID_TEST` - TEST workspace ID
- `FABRIC_WORKSPACE_ID_PROD` - PROD workspace ID
- `FABRIC_TOKEN` - Fabric authentication token

### Notifications (Optional)
- `TEAMS_WEBHOOK_URL` - Microsoft Teams webhook for notifications
- `SLACK_WEBHOOK_URL` - Slack webhook for notifications

---

## 📋 Environment Setup

Each environment uses a corresponding config file:
- **DEV:** `config/dev.yml`
- **TEST:** `config/test.yml`
- **PROD:** `config/prod.yml`

---

## 🎯 Workflow Best Practices

### For Developers:
1. **Always create PRs** for changes → Triggers validation
2. **Review PR validation** results before merging
3. **Merge to develop** first → Auto-deploys to DEV
4. **Test in DEV**, then merge to main → Auto-deploys to TEST
5. **Manual production deploys** require approval

### For Data Scientists:
1. **Validate notebooks locally** before committing
2. **Use workflow_dispatch** to manually trigger training
3. **Check data quality reports** regularly
4. **Review model performance** before production deployment

---

## 📊 Monitoring & Logs

- **View workflow runs:** GitHub Actions tab
- **Download artifacts:** Available for 30-90 days
- **Check summaries:** Each workflow generates a summary report
- **Deployment log:** See `DEPLOYMENT_LOG.md` in root

---

## 🛠️ Troubleshooting

### Workflow fails with authentication error
- Check that Azure secrets are correctly configured
- Verify service principal has necessary permissions

### Deployment validation fails
- Review the validation script output
- Check workspace connectivity
- Verify item IDs match configuration

### Data quality checks fail
- Review the quality report artifact
- Check data source connectivity
- Verify expected thresholds are realistic

---

## 📞 Support

For issues or questions about these workflows, contact:
- **Platform Team:** [platform-team@nvr.com]
- **Data Science Lead:** [data-science-lead@nvr.com]

---

*Last updated: January 4, 2026*
