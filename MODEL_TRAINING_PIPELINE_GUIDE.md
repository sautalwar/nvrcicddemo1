# 🚀 Model Training Pipeline - Quick Start Guide

## Overview

This guide explains how to use the automated CI/CD pipeline for deploying the `model_training` notebook from Development → Test → Production.

**Workflow File**: [`.github/workflows/model-training-pipeline.yml`](.github/workflows/model-training-pipeline.yml)

---

## 📋 Pipeline Stages

```
┌─────────────┐
│   PR Open   │ ← Developer creates Pull Request
└──────┬──────┘
       │
       ▼
┌─────────────────┐
│ PR Validation   │ ← Automatic quality checks
│ ✅ Formatting   │
│ ✅ Security     │
│ ✅ Tests        │
└──────┬──────────┘
       │
       ▼
┌─────────────────┐
│  Merge to main  │ ← PR approved and merged
└──────┬──────────┘
       │
       ▼
┌─────────────────┐
│  Deploy to DEV  │ ← **AUTOMATIC** ⚡
│  ✅ Smoke tests │
└──────┬──────────┘
       │
       ▼ (Manual trigger)
┌─────────────────┐
│ Deploy to TEST  │ ← **MANUAL APPROVAL** 👤
│ ✅ Integration  │
│ ✅ Data Quality │
└──────┬──────────┘
       │
       ▼ (Manual trigger + Change ticket)
┌─────────────────┐
│ Deploy to PROD  │ ← **MANUAL APPROVAL + BACKUP** 🔒
│ 📸 Backup       │
│ ✅ Validation   │
│ 🔙 Rollback     │
└─────────────────┘
```

---

## 🎯 Step-by-Step Usage

### **Step 1: Make Changes Locally**

```bash
# Create a feature branch
git checkout -b feature/improve-model-training

# Make your changes to the model_training notebook
# Edit: model_training1.Notebook/notebook-content.py

# Commit changes
git add model_training1.Notebook/
git commit -m "feat: improve model accuracy with hyperparameter tuning"

# Push to GitHub
git push origin feature/improve-model-training
```

---

### **Step 2: Create Pull Request**

1. Go to GitHub repository
2. Click **"Compare & pull request"**
3. Fill in:
   - **Title**: `feat: improve model accuracy with hyperparameter tuning`
   - **Description**: Explain what you changed and why
4. Click **"Create pull request"**

**What happens automatically:**
- ✅ Code formatting validation
- ✅ Notebook structure validation
- ✅ Security scanning (no hardcoded secrets)
- ✅ Unit tests

**Result**: Green checkmark = Ready to merge ✅

---

### **Step 3: Merge to Main → DEV Deployment (AUTOMATIC)**

1. Get PR approval from team member
2. Click **"Merge pull request"**
3. Click **"Confirm merge"**

**What happens automatically:**
- 🚀 Deploys `model_training` notebook to **DEV** workspace
- 🔄 Deploys `customer_analytics_pipeline` to **DEV** workspace  
- 🧪 Runs smoke tests
- 📊 Generates deployment summary

**No manual intervention needed!** ⚡

**View deployment:**
- Go to **Actions** tab in GitHub
- Click on the running workflow
- Monitor progress in real-time

---

### **Step 4: Deploy to TEST (MANUAL)**

After successful DEV deployment and testing:

#### **Option A: Using GitHub CLI**

```bash
gh workflow run model-training-pipeline.yml \
  -f environment=test \
  -f deployment_reason="Promote from DEV after successful testing"
```

#### **Option B: Using GitHub UI**

1. Go to **Actions** tab
2. Click **"Model Training Pipeline - Dev → Test → Prod"**
3. Click **"Run workflow"** button
4. Fill in:
   - **Environment**: `test`
   - **Deployment reason**: `Promote from DEV after successful testing`
5. Click **"Run workflow"**

**What happens:**
- ⏸️ **Waits for manual approval** (if TEST environment protection is enabled)
- 🚀 Deploys to **TEST** workspace
- 🔍 Runs data quality validation
- 🧪 Runs integration tests
- ✅ Validates deployment

**Approval required**: Designated reviewers must approve before deployment proceeds.

---

### **Step 5: Deploy to PROD (MANUAL + CHANGE TICKET)**

After successful TEST deployment:

#### **Using GitHub CLI**

```bash
gh workflow run model-training-pipeline.yml \
  -f environment=prod \
  -f deployment_reason="Production release - Q1 2026 model update" \
  -f change_ticket="CHG-12345"
```

#### **Using GitHub UI**

1. Go to **Actions** tab
2. Click **"Model Training Pipeline - Dev → Test → Prod"**
3. Click **"Run workflow"** button
4. Fill in:
   - **Environment**: `prod`
   - **Deployment reason**: `Production release - Q1 2026 model update`
   - **Change Ticket**: `CHG-12345` ← **REQUIRED for PROD**
5. Click **"Run workflow"**

**What happens:**
- 🎫 Validates change management ticket
- ⏸️ **Waits for approval** from designated PROD approvers
- 📸 **Creates backup** of current PROD state
- 🚀 Deploys to **PRODUCTION** workspace
- ✅ Validates deployment
- 🧪 Runs smoke tests
- 📝 Logs deployment to audit trail
- 📧 Notifies stakeholders

**Safety features:**
- ✅ Automatic backup before deployment
- ✅ Rollback plan ready
- ✅ Audit trail in [`DEPLOYMENT_LOG.md`](DEPLOYMENT_LOG.md)

---

## 🔙 Emergency Rollback (PROD Only)

If a production deployment fails or needs to be rolled back:

```bash
# Manual rollback using the backup ID from deployment summary
python scripts/rollback_deployment.py --backup-id backup-20260105-143022
```

**Automatic rollback**: The workflow automatically triggers rollback if PROD deployment fails.

---

## 🔐 Required GitHub Secrets

Ensure these secrets are configured in GitHub repository settings:

| Secret Name | Description | Example |
|-------------|-------------|---------|
| `AZURE_CLIENT_ID` | Azure AD App Client ID | `12345678-1234-1234-1234-123456789abc` |
| `AZURE_TENANT_ID` | Azure AD Tenant ID | `87654321-4321-4321-4321-cba987654321` |
| `AZURE_SUBSCRIPTION_ID` | Azure Subscription ID | `abcd1234-5678-90ef-ghij-klmnopqrstuv` |
| `FABRIC_DEV_WORKSPACE_ID` | Fabric DEV workspace ID | `11111111-1111-1111-1111-111111111111` |
| `FABRIC_TEST_WORKSPACE_ID` | Fabric TEST workspace ID | `22222222-2222-2222-2222-222222222222` |
| `FABRIC_PROD_WORKSPACE_ID` | Fabric PROD workspace ID | `33333333-3333-3333-3333-333333333333` |

**Setup instructions**: See [Step 2: Configure GitHub Secrets](#step-2-configure-github-secrets-coming-next)

---

## 🛡️ Environment Protection Rules

Configure protection rules for TEST and PROD environments:

### **TEST Environment**
- ✅ Required reviewers: 1 team member
- ✅ Wait timer: 0 minutes (deploy immediately after approval)

### **PROD Environment**
- ✅ Required reviewers: 2 senior team members
- ✅ Wait timer: 5 minutes (cooling-off period)
- ✅ Deployment branches: `main` only

**Setup instructions**: See [Step 3: Configure Environment Protection](#step-3-configure-environment-protection-coming-next)

---

## 📊 Monitoring Deployments

### **View Active Deployments**

1. Go to **Actions** tab in GitHub
2. Click on running workflow
3. Monitor real-time logs for each job

### **View Deployment History**

1. Go to **Actions** tab
2. Filter by **"Model Training Pipeline"**
3. See all past deployments with status

### **View Audit Trail**

Check [`DEPLOYMENT_LOG.md`](DEPLOYMENT_LOG.md) for complete production deployment history.

---

## 🚨 Troubleshooting

### **PR Validation Failed**

**Error**: "Code formatting issues"
```bash
# Fix locally
black scripts/ *.py --line-length 120

# Commit and push
git add .
git commit -m "fix: apply black formatting"
git push
```

**Error**: "Hardcoded secrets detected"
- Remove any passwords, API keys, or tokens from code
- Use GitHub Secrets or Azure Key Vault instead

### **DEV Deployment Failed**

**Error**: "Authentication failed"
- Verify Azure credentials are configured correctly
- Check `AZURE_CLIENT_ID`, `AZURE_TENANT_ID`, `AZURE_SUBSCRIPTION_ID` secrets

**Error**: "Workspace not found"
- Verify `FABRIC_DEV_WORKSPACE_ID` is correct
- Ensure service principal has access to the workspace

### **TEST/PROD Deployment Pending**

**Status**: "Waiting for approval"
- Contact designated reviewers to approve the deployment
- Reviewers can see pending deployments in **Environments** section

---

## 📞 Getting Help

- **Workflow issues**: Check [CI_CD_PIPELINE_OVERVIEW.md](CI_CD_PIPELINE_OVERVIEW.md)
- **Deployment scripts**: See `scripts/` folder documentation
- **Fabric API issues**: Check Fabric API documentation

---

## ✅ Checklist for Your First Deployment

- [ ] Secrets configured in GitHub
- [ ] Environment protection rules set up (TEST, PROD)
- [ ] Feature branch created
- [ ] Changes committed to `model_training1.Notebook/`
- [ ] Pull request created
- [ ] PR validation passed (all green checkmarks)
- [ ] PR approved by team member
- [ ] PR merged to `main`
- [ ] DEV deployment successful (automatic)
- [ ] Tested in DEV workspace
- [ ] Promoted to TEST (manual trigger)
- [ ] TEST integration tests passed
- [ ] Change ticket created (for PROD)
- [ ] Promoted to PROD (manual trigger with ticket)
- [ ] PROD deployment successful
- [ ] Stakeholders notified

---

## 🎉 Success Indicators

You'll know the pipeline is working when:

1. ✅ **PR validation** shows green checkmarks
2. ✅ **DEV deployment** completes automatically after merge
3. ✅ **TEST deployment** requires manual approval
4. ✅ **PROD deployment** requires change ticket + approval
5. ✅ **Backup created** before every PROD deployment
6. ✅ **Audit trail** updated in DEPLOYMENT_LOG.md

---

**Last Updated**: January 5, 2026  
**Workflow Version**: 1.0  
**Maintained by**: NVR Data Science Team
