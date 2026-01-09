# GitHub Environments Setup Guide

## ⚠️ Important: Environment Configuration

The workflow has **commented out** environment references until you create them in GitHub.

## 🔧 Steps to Enable Environments:

### 1. Go to GitHub Repository Settings

1. Navigate to your repository: https://github.com/sautalwar/nvrcicddemo1
2. Click **Settings** (top right)
3. Click **Environments** (left sidebar)

### 2. Create Three Environments

Create each environment with the following settings:

#### **Development Environment**
```
Name: development
Protection rules: None (auto-deploy)
```

#### **Test Environment**
```
Name: test
Protection rules:
  ✅ Required reviewers: [Add yourself or team members]
  Wait timer: 0 minutes
```

#### **Production Environment**
```
Name: production
Protection rules:
  ✅ Required reviewers: [Add manager/lead]
  ✅ Wait timer: 5 minutes
  ✅ Prevent self-review
  ✅ Required branches: main
```

### 3. Uncomment Environment Lines in Workflow

After creating the environments, uncomment these lines in `.github/workflows/model-training-pipeline.yml`:

**Line ~157** (Deploy to DEV):
```yaml
    runs-on: ubuntu-latest
    environment:       # ← Uncomment this line
      name: development  # ← Uncomment this line
```

**Line ~273** (Deploy to TEST):
```yaml
    runs-on: ubuntu-latest
    environment:       # ← Uncomment this line
      name: test        # ← Uncomment this line
```

**Line ~387** (Deploy to PROD):
```yaml
    runs-on: ubuntu-latest
    environment:       # ← Uncomment this line
      name: production  # ← Uncomment this line
```

**Line ~547** (Rollback):
```yaml
    runs-on: ubuntu-latest
    environment: production  # ← Uncomment this line
```

### 4. Add Environment Secrets (Optional)

If you need environment-specific secrets:

1. Go to **Settings → Environments → [Environment Name]**
2. Click **Add Secret**
3. Add environment-specific values

## 📋 Quick Fix Script

Run this after creating environments in GitHub UI:

```powershell
# Uncomment environment lines in workflow
$file = ".github\workflows\model-training-pipeline.yml"
$content = Get-Content $file -Raw
$content = $content -replace '# environment:', 'environment:'
$content = $content -replace '#   name: development', '  name: development'
$content = $content -replace '#   name: test', '  name: test'  
$content = $content -replace '#   name: production', '  name: production'
$content = $content -replace '# environment: production', 'environment: production'
$content | Set-Content $file

git add .
git commit -m "feat: Enable GitHub environment protection"
git push
```

## ✅ Verify Setup

1. **Go to Actions tab**
2. **Run workflow manually**: Actions → Model Training Pipeline → Run workflow
3. **Select environment**: test
4. **Check**: You should see "Waiting for approval" 
5. **Approve**: Click "Review deployments" → Approve

## 🎯 Why This Matters

**Without environments:**
- ✅ Workflow will still run
- ❌ No approval gates
- ❌ No environment protection
- ❌ Anyone can deploy to prod

**With environments:**
- ✅ Approval required for test/prod
- ✅ Deployment history tracked
- ✅ Environment-specific secrets
- ✅ Compliance and audit trail

## 📚 More Info

- [GitHub Environments Docs](https://docs.github.com/en/actions/deployment/targeting-different-environments/using-environments-for-deployment)
- [Deployment Protection Rules](https://docs.github.com/en/actions/deployment/targeting-different-environments/using-environments-for-deployment#deployment-protection-rules)

---

**Status**: Workflow is functional but environments are disabled for now. Follow steps above to enable protection rules.
