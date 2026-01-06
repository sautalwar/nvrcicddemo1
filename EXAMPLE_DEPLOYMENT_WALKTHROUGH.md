# 🎬 Model Training Pipeline - Real Example Walkthrough

This document walks through a **real-world scenario** of deploying a model training notebook update from Development to Production.

---

## 📖 Scenario

**Task**: Add improved logging and error handling to the `model_training` notebook to better track model performance in production.

**Developer**: Jane Doe (Data Scientist)  
**Reviewer**: John Smith (Senior Data Scientist)  
**Date**: January 5, 2026

---

## Step 1: Local Development (5 minutes)

### Create feature branch

```bash
# Jane creates a new feature branch
cd c:\Users\sautalwar\Downloads\fabric-cicd-demo
git checkout main
git pull origin main
git checkout -b feature/enhanced-model-logging
```

### Make changes to notebook

**File**: `model_training1.Notebook/notebook-content.py`

**Changes**:
```python
# Add at the beginning of the training cell
import logging
from datetime import datetime

# Configure logging
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s'
)
logger = logging.getLogger(__name__)

# Enhanced logging
logger.info("=" * 60)
logger.info("🚀 Model Training Pipeline v2.1")
logger.info(f"📅 Started at: {datetime.now()}")
logger.info(f"🌍 Environment: {os.environ.get('ENVIRONMENT', 'unknown')}")
logger.info("=" * 60)

# ... existing training code ...

# At the end of training
logger.info(f"✅ Model training completed successfully")
logger.info(f"📊 Final accuracy: {accuracy:.4f}")
logger.info(f"📊 Final F1 score: {f1:.4f}")
logger.info(f"⏱️ Training duration: {training_duration:.2f} seconds")
```

### Commit changes

```bash
git add model_training1.Notebook/notebook-content.py
git commit -m "feat: add enhanced logging to model training notebook"
git push origin feature/enhanced-model-logging
```

**Terminal output**:
```
Enumerating objects: 7, done.
Counting objects: 100% (7/7), done.
Delta compression using up to 8 threads
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 1.23 KiB | 1.23 MiB/s, done.
Total 4 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (2/2), completed with 2 local objects.
To github.com:sautalwar/nvrfabricdemo1.git
 * [new branch]      feature/enhanced-model-logging -> feature/enhanced-model-logging
```

---

## Step 2: Create Pull Request (2 minutes)

### Using GitHub UI

1. Navigate to: `https://github.com/sautalwar/nvrfabricdemo1`
2. Click **"Compare & pull request"** (appears automatically)
3. Fill in PR details:

**Title**: `feat: add enhanced logging to model training notebook`

**Description**:
```markdown
## What changed?
- Added structured logging to model training notebook
- Logs training start time, environment, and performance metrics
- Improves production observability

## Why?
- Current model training has minimal logging
- Difficult to troubleshoot issues in production
- Need better visibility into model performance

## Testing
- [x] Tested locally
- [ ] Needs DEV workspace validation
- [ ] Will validate in TEST before PROD

## Checklist
- [x] Code follows team standards
- [x] No hardcoded secrets
- [x] Documentation updated if needed
```

4. Click **"Create pull request"**

### Automatic PR Validation Triggered

**GitHub Actions starts running**: `PR Validation`

**Checks running**:
```
✅ Code formatting check (black)        [12s]
✅ Linting check (flake8)               [8s]
✅ Notebook validation                  [15s]
✅ Security scan (no secrets)           [6s]
✅ Unit tests                           [22s]
✅ Configuration validation             [5s]
```

**Total time**: ~1 minute

**Result**: All checks passed ✅

**PR status**: Ready for review

---

## Step 3: Code Review & Approval (15 minutes)

### Reviewer (John Smith) reviews PR

**Review comments**:
> "Great addition! The logging will definitely help us track model performance. One suggestion: can you also log the number of training samples used?"

### Jane addresses feedback

**Additional change**:
```python
logger.info(f"📊 Training samples: {len(X_train)}")
logger.info(f"📊 Validation samples: {len(X_test)}")
```

**Commit**:
```bash
git add model_training1.Notebook/notebook-content.py
git commit -m "feat: add training sample count to logs"
git push origin feature/enhanced-model-logging
```

### PR re-validation (automatic)

All checks pass again ✅

### John approves PR

**Review**: "Approved ✅ - LGTM! Great improvement to observability."

### Jane merges PR

Click **"Merge pull request"** → **"Confirm merge"**

**Merge commit**:
```
Merge pull request #47 from sautalwar/feature/enhanced-model-logging

feat: add enhanced logging to model training notebook
```

---

## Step 4: Automatic DEV Deployment (3 minutes)

### Workflow triggered automatically

**Trigger**: Push to `main` branch  
**Workflow**: `Model Training Pipeline - Dev → Test → Prod`  
**Job**: `deploy-dev`

### Deployment steps

```
📥 Checkout code                        [8s]
🐍 Set up Python 3.10                   [12s]
📦 Install dependencies                 [45s]
🔐 Authenticate to Azure                [6s]
🎫 Get Fabric Access Token              [3s]
🚀 Deploy notebook to DEV               [32s]
🔄 Deploy pipeline to DEV               [18s]
✅ Run smoke tests                      [25s]
📊 Generate deployment summary          [2s]
```

**Total time**: ~2.5 minutes

**Deployment ID**: `dev-20260105-093045`

### Deployment Summary (GitHub Actions UI)

```markdown
## 🚀 DEV Deployment Complete

| Item | Details |
|------|---------|
| Environment | `DEV` |
| Workspace | `NVR-Dev` |
| Deployment ID | `dev-20260105-093045` |
| Deployed At | 2026-01-05 09:30:45 UTC |
| Artifacts | Notebook + Pipeline |
| Smoke Tests | ✅ Passed |

✨ Ready for promotion to TEST

To deploy to TEST, run:
```
gh workflow run model-training-pipeline.yml -f environment=test -f deployment_reason='Promote from DEV'
```
```

### Slack notification (configured in workflow)

```
🚀 DEV Deployment Successful
Repository: nvrfabricdemo1
Environment: DEV
Deployed by: @janedoe
Deployment ID: dev-20260105-093045
Status: ✅ Success

View details: https://github.com/sautalwar/nvrfabricdemo1/actions/runs/12345678
```

---

## Step 5: Manual Testing in DEV (30 minutes)

### Jane tests in DEV workspace

1. Opens Fabric workspace: `NVR-Dev`
2. Navigates to `model_training1` notebook
3. Runs notebook manually
4. Verifies enhanced logging appears in output:

**Expected output**:
```
============================================================
🚀 Model Training Pipeline v2.1
📅 Started at: 2026-01-05 09:45:23.123456
🌍 Environment: dev
============================================================
✅ Libraries imported successfully
📂 Loading data from: Files/silver/customer_features
📊 Training samples: 8000
📊 Validation samples: 2000
🔧 Training Random Forest model...
✅ Model training completed successfully
📊 Final accuracy: 0.8742
📊 Final F1 score: 0.8456
⏱️ Training duration: 12.34 seconds
```

### ✅ Testing successful

Jane confirms:
- [x] Logging works as expected
- [x] All metrics are captured
- [x] No errors or warnings
- [x] Ready for TEST promotion

---

## Step 6: Deploy to TEST (Manual) (10 minutes)

### Jane triggers TEST deployment

**Using GitHub CLI**:
```bash
gh workflow run model-training-pipeline.yml \
  -f environment=test \
  -f deployment_reason="Enhanced logging validated in DEV - ready for integration testing"
```

**Or using GitHub UI**:
1. Go to **Actions** tab
2. Click **"Model Training Pipeline - Dev → Test → Prod"**
3. Click **"Run workflow"**
4. Environment: `test`
5. Deployment reason: `Enhanced logging validated in DEV - ready for integration testing`
6. Click **"Run workflow"**

### Workflow awaits approval

**Status**: ⏸️ **Waiting for required reviewers**

**Notification sent to**: John Smith (designated TEST approver)

**Email notification**:
```
Subject: Approval required for TEST deployment

A deployment to TEST environment is waiting for your approval.

Repository: nvrfabricdemo1
Environment: test
Requested by: @janedoe
Reason: Enhanced logging validated in DEV - ready for integration testing

Review and approve: https://github.com/sautalwar/nvrfabricdemo1/actions/runs/12345679
```

### John approves deployment (5 minutes later)

1. Clicks link in email
2. Reviews deployment details
3. Clicks **"Review pending deployments"**
4. Selects `test` environment
5. Clicks **"Approve and deploy"**

### Deployment proceeds

```
🚀 Deploy to TEST workspace              [28s]
🔍 Data quality validation              [42s]
🧪 Run integration tests                [95s]
✅ Validate deployment                  [15s]
📊 Generate deployment summary          [3s]
```

**Total time**: ~3 minutes (after approval)

**Deployment ID**: `test-20260105-100234`

### TEST Deployment Summary

```markdown
## 🧪 TEST Deployment Complete

| Item | Details |
|------|---------|
| Environment | `TEST` |
| Workspace | `NVR-Test` |
| Deployment ID | `test-20260105-100234` |
| Reason | Enhanced logging validated in DEV - ready for integration testing |
| Data Quality | ✅ Validated |
| Integration Tests | ✅ passed |
| Deployment Validation | ✅ Passed |

✨ Ready for PRODUCTION deployment

To deploy to PRODUCTION, run:
```
gh workflow run model-training-pipeline.yml -f environment=prod -f deployment_reason='Production Release' -f change_ticket='CHG-12345'
```
```

---

## Step 7: UAT in TEST (1 day)

### Stakeholder testing (24 hours)

**Testers**:
- Senior Data Scientist (John Smith) ✅
- Data Science Manager (Sarah Johnson) ✅  
- ML Ops Engineer (Mike Chen) ✅

**Test results**:
- [x] Notebook executes successfully
- [x] Logging provides valuable insights
- [x] Pipeline integration works
- [x] Performance metrics accurate
- [x] No regression issues

**Approval**: ✅ **Ready for production**

---

## Step 8: Deploy to PROD (Manual) (20 minutes)

### Create change management ticket

**Ticket System**: ServiceNow  
**Ticket ID**: `CHG-20260106-001`  
**Title**: Deploy enhanced model training logging to PROD  
**Scheduled**: 2026-01-06 14:00 UTC (off-peak hours)  
**Approvers**: Director of Data Science (approved ✅)

### Jane triggers PROD deployment

```bash
gh workflow run model-training-pipeline.yml \
  -f environment=prod \
  -f deployment_reason="Production release - Enhanced logging and observability" \
  -f change_ticket="CHG-20260106-001"
```

### Workflow awaits TWO approvals

**Status**: ⏸️ **Waiting for required reviewers (0/2)**

**Designated approvers**:
1. John Smith (Senior Data Scientist)
2. Sarah Johnson (Data Science Manager)

### Approvals received

**John Smith** approves (5 mins later)  
**Sarah Johnson** approves (10 mins later)

**Status**: ✅ **All required approvals received**

### 5-minute cooling-off period

**Status**: ⏸️ **Waiting for timer (5 minutes)**

This allows time to cancel if needed.

### Deployment proceeds

```
🎫 Validate change ticket               [5s]
📸 Backup current PROD state            [68s]
🚀 Deploy to PRODUCTION                 [35s]
✅ Validate PROD deployment             [18s]
🧪 Run smoke tests in PROD              [32s]
📝 Log deployment to audit trail        [4s]
📊 Generate PROD deployment summary     [3s]
📧 Notify stakeholders                  [2s]
```

**Total time**: ~3 minutes (after cooling-off period)

**Deployment ID**: `prod-20260106-140645`  
**Backup ID**: `backup-20260106-140545`

### PROD Deployment Summary

```markdown
## 🎯 PRODUCTION Deployment Complete

| Item | Details |
|------|---------|
| Environment | `PRODUCTION` 🚀 |
| Workspace | `NVR-Production` |
| Deployment ID | `prod-20260106-140645` |
| Backup ID | `backup-20260106-140545` |
| Change Ticket | CHG-20260106-001 |
| Reason | Production release - Enhanced logging and observability |
| Deployed By | @janedoe |
| Backup Created | ✅ Yes |
| Deployment Validation | ✅ Passed |
| Smoke Tests | ✅ Passed |

✨ PRODUCTION deployment successful!

### 🔙 Rollback Instructions (if needed)
```bash
python scripts/rollback_deployment.py --backup-id backup-20260106-140545
```
```

### Audit trail updated

**File**: `DEPLOYMENT_LOG.md`

```markdown
## 🎯 Production Deployment - 2026-01-06 14:06:45 UTC

- **Deployment ID**: `prod-20260106-140645`
- **Backup ID**: `backup-20260106-140545`
- **Change Ticket**: CHG-20260106-001
- **Reason**: Production release - Enhanced logging and observability
- **Deployed By**: @janedoe
- **Commit**: `abc123def456`
- **Artifacts**: model_training notebook, customer_analytics_pipeline
- **Status**: ✅ Success
```

### Stakeholder notification

**Email sent to**:
- Data Science Team (all members)
- Data Engineering Team
- ML Ops Team
- Director of Data Science

**Email**:
```
Subject: ✅ Production Deployment Successful - Enhanced Model Logging

The model training notebook has been successfully deployed to PRODUCTION.

What changed:
- Enhanced logging with structured format
- Training metrics now tracked in logs
- Improved observability for production monitoring

Deployment Details:
- Environment: PRODUCTION
- Deployment ID: prod-20260106-140645
- Change Ticket: CHG-20260106-001
- Deployed At: 2026-01-06 14:06:45 UTC
- Deployed By: Jane Doe
- Status: ✅ Success

The new logging will help us better track model performance and troubleshoot issues.

Rollback plan ready if needed (backup ID: backup-20260106-140545)

Questions? Contact the Data Science team.
```

---

## Summary Timeline

| Stage | Duration | Type | Status |
|-------|----------|------|--------|
| Local Development | 5 min | Manual | ✅ |
| Create PR | 2 min | Manual | ✅ |
| PR Validation | 1 min | Automatic | ✅ |
| Code Review | 15 min | Manual | ✅ |
| Merge to main | 1 min | Manual | ✅ |
| **DEV Deployment** | **3 min** | **Automatic** | ✅ |
| Manual Testing | 30 min | Manual | ✅ |
| Trigger TEST | 2 min | Manual | ✅ |
| **TEST Approval** | **5 min** | **Manual** | ✅ |
| **TEST Deployment** | **3 min** | **Automatic** | ✅ |
| UAT in TEST | 1 day | Manual | ✅ |
| Create Change Ticket | 10 min | Manual | ✅ |
| Trigger PROD | 2 min | Manual | ✅ |
| **PROD Approval (2)** | **10 min** | **Manual** | ✅ |
| **Cooling-off Period** | **5 min** | **Automatic** | ✅ |
| **PROD Deployment** | **3 min** | **Automatic** | ✅ |

**Total time from code to PROD**: ~2 days (mostly waiting for approvals and testing)  
**Active development time**: ~1 hour  
**Automated deployment time**: ~9 minutes total  
**Manual approval time**: ~35 minutes

---

## Key Takeaways

✅ **Automation saved significant time**:
- No manual file uploads to Fabric
- Automatic quality checks caught issues early
- Consistent deployment process across all environments

✅ **Safety measures protected PROD**:
- Multiple approval gates
- Automatic backup before PROD deployment
- Rollback plan ready if needed
- Complete audit trail

✅ **Visibility improved**:
- Real-time deployment status in GitHub Actions
- Automatic notifications to stakeholders
- Deployment history tracked in audit log

✅ **Team productivity increased**:
- Developer focused on code, not deployment mechanics
- Reviewers had clear visibility into changes
- Stakeholders got timely notifications

---

**Next**: Learn how to set up [GitHub Secrets](MODEL_TRAINING_PIPELINE_GUIDE.md#required-github-secrets) and [Environment Protection Rules](MODEL_TRAINING_PIPELINE_GUIDE.md#environment-protection-rules)
