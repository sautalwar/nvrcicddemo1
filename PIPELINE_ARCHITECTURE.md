# Model Training Pipeline - Architecture Diagram

## End-to-End Flow

```mermaid
graph TB
    Start[Developer makes changes] --> Branch[Create feature branch]
    Branch --> Commit[Commit changes]
    Commit --> PR[Create Pull Request]
    
    PR --> Validation{PR Validation}
    Validation -->|✅ Pass| Approved[Team approves PR]
    Validation -->|❌ Fail| FixIssues[Fix issues]
    FixIssues --> Commit
    
    Approved --> Merge[Merge to main]
    Merge --> DevDeploy[Deploy to DEV - AUTOMATIC]
    
    DevDeploy --> DevTests{DEV Smoke Tests}
    DevTests -->|✅ Pass| DevSuccess[DEV Success]
    DevTests -->|❌ Fail| DevFix[Fix issues in new PR]
    DevFix --> PR
    
    DevSuccess --> ManualTest[Manual testing in DEV]
    ManualTest --> TestTrigger{Ready for TEST?}
    TestTrigger -->|Yes| TestApproval[Trigger TEST deployment]
    TestTrigger -->|No| DevFix
    
    TestApproval --> TestDeploy[Deploy to TEST - MANUAL]
    TestDeploy --> TestValidation{TEST Validation}
    TestValidation -->|✅ Integration Tests Pass| TestSuccess[TEST Success]
    TestValidation -->|❌ Fail| DevFix
    
    TestSuccess --> ProdTrigger{Ready for PROD?}
    ProdTrigger -->|Yes + Change Ticket| ProdApproval[Trigger PROD deployment]
    ProdTrigger -->|No| TestSuccess
    
    ProdApproval --> Backup[Create PROD Backup]
    Backup --> ProdDeploy[Deploy to PROD - MANUAL]
    ProdDeploy --> ProdValidation{PROD Validation}
    ProdValidation -->|✅ Pass| ProdSuccess[PROD Success]
    ProdValidation -->|❌ Fail| Rollback[Automatic Rollback]
    Rollback --> DevFix
    
    ProdSuccess --> Audit[Log to audit trail]
    Audit --> Notify[Notify stakeholders]
    Notify --> End[Deployment Complete]
    
    style DevDeploy fill:#90EE90
    style TestDeploy fill:#FFD700
    style ProdDeploy fill:#FF6347
    style Backup fill:#1E90FF
    style Rollback fill:#FF0000
    style End fill:#32CD32
```

## Job Dependencies

```mermaid
graph LR
    A[pr-validation] --> B[deploy-dev]
    B -.manual trigger.-> C[deploy-test]
    C -.manual trigger.-> D[deploy-prod]
    D -.on failure.-> E[rollback]
    
    style A fill:#E8F4F8
    style B fill:#90EE90
    style C fill:#FFD700
    style D fill:#FF6347
    style E fill:#FF0000
```

## Environment Progression

```mermaid
graph LR
    DEV[🔧 DEV<br/>Automatic<br/>No approval] --> TEST[🧪 TEST<br/>Manual trigger<br/>1 reviewer]
    TEST --> PROD[🎯 PROD<br/>Manual trigger<br/>2 reviewers<br/>Change ticket<br/>Backup created]
    
    style DEV fill:#90EE90,stroke:#2E8B57,stroke-width:3px
    style TEST fill:#FFD700,stroke:#DAA520,stroke-width:3px
    style PROD fill:#FF6347,stroke:#DC143C,stroke-width:3px
```

## Deployment Decision Tree

```mermaid
flowchart TD
    Start([New code changes]) --> Q1{Is it in a PR?}
    Q1 -->|Yes| PRVal[Run PR validation]
    Q1 -->|No| CreatePR[Create PR first]
    CreatePR --> PRVal
    
    PRVal --> Q2{Validation passed?}
    Q2 -->|No| Fix1[Fix issues]
    Q2 -->|Yes| Q3{PR approved?}
    Fix1 --> PRVal
    
    Q3 -->|No| Wait1[Wait for review]
    Q3 -->|Yes| Merge[Merge to main]
    Wait1 --> Q3
    
    Merge --> DevAuto[Auto-deploy to DEV]
    DevAuto --> Q4{Tested in DEV?}
    Q4 -->|No| TestDev[Test in DEV]
    Q4 -->|Yes| Q5{Deploy to TEST?}
    TestDev --> Q4
    
    Q5 -->|Yes| ManualTest[Manual trigger + approval]
    Q5 -->|No| Done1([Stay in DEV])
    ManualTest --> TestDep[Deploy to TEST]
    
    TestDep --> Q6{Integration tests passed?}
    Q6 -->|No| Fix2[Fix in new PR]
    Q6 -->|Yes| Q7{Deploy to PROD?}
    Fix2 --> Start
    
    Q7 -->|Yes| Q8{Have change ticket?}
    Q7 -->|No| Done2([Stay in TEST])
    Q8 -->|No| GetTicket[Create change ticket]
    Q8 -->|Yes| ProdDep[Deploy to PROD]
    GetTicket --> Q8
    
    ProdDep --> Q9{Deployment successful?}
    Q9 -->|Yes| Success([✅ PROD Success])
    Q9 -->|No| Rollback[Auto-rollback]
    Rollback --> Fix2
    
    style Start fill:#E8F4F8
    style Success fill:#32CD32
    style DevAuto fill:#90EE90
    style TestDep fill:#FFD700
    style ProdDep fill:#FF6347
    style Rollback fill:#FF0000
```

## Artifact Deployment Flow

```mermaid
graph TB
    subgraph "Source Code"
        NB[model_training1.Notebook]
        PL[customer_analytics_pipeline.json]
        QC[check_data_quality.py]
    end
    
    subgraph "DEV Workspace"
        NB --> DevNB[Notebook - DEV]
        PL --> DevPL[Pipeline - DEV]
        QC --> DevQC[Quality Checks - DEV]
        DevNB --> DevTest[Smoke Tests]
        DevPL --> DevTest
        DevQC --> DevTest
    end
    
    subgraph "TEST Workspace"
        DevTest -.manual.-> TestNB[Notebook - TEST]
        DevTest -.manual.-> TestPL[Pipeline - TEST]
        DevTest -.manual.-> TestQC[Quality Checks - TEST]
        TestNB --> TestVal[Integration Tests]
        TestPL --> TestVal
        TestQC --> TestVal
    end
    
    subgraph "PROD Workspace"
        TestVal -.manual + ticket.-> ProdBackup[Create Backup]
        ProdBackup --> ProdNB[Notebook - PROD]
        ProdBackup --> ProdPL[Pipeline - PROD]
        ProdBackup --> ProdQC[Quality Checks - PROD]
        ProdNB --> ProdVal[Validation]
        ProdPL --> ProdVal
        ProdQC --> ProdVal
    end
    
    style DevTest fill:#90EE90
    style TestVal fill:#FFD700
    style ProdBackup fill:#1E90FF
    style ProdVal fill:#FF6347
```

## Trigger Types

```mermaid
mindmap
  root((Pipeline Triggers))
    Pull Request
      Branches: main, develop
      Paths: notebooks/, pipelines/
      Action: Validate code
    Push to main
      Automatic DEV deployment
      After PR merge
      Deploys changed files
    Manual - TEST
      workflow_dispatch
      Requires approval
      Integration tests
    Manual - PROD
      workflow_dispatch
      Requires change ticket
      Creates backup
      Requires 2 approvers
```

## Security & Approval Gates

```mermaid
graph TD
    Code[Code Changes] --> Gate1{Security Scan}
    Gate1 -->|✅ Pass| Gate2{Unit Tests}
    Gate1 -->|❌ Fail| Block1[Block merge]
    
    Gate2 -->|✅ Pass| Gate3{Code Review}
    Gate2 -->|❌ Fail| Block1
    
    Gate3 -->|✅ Approved| DEV[Deploy to DEV]
    Gate3 -->|❌ Changes requested| Block1
    
    DEV --> Gate4{DEV Smoke Tests}
    Gate4 -->|✅ Pass| Manual1{Manual Approval<br/>for TEST?}
    Gate4 -->|❌ Fail| Block2[Stay in DEV]
    
    Manual1 -->|✅ Approved| TEST[Deploy to TEST]
    Manual1 -->|❌ Denied| Block2
    
    TEST --> Gate5{Integration Tests}
    Gate5 -->|✅ Pass| Manual2{Manual Approval<br/>for PROD?}
    Gate5 -->|❌ Fail| Block3[Stay in TEST]
    
    Manual2 -->|✅ Approved + Ticket| Backup[Create Backup]
    Manual2 -->|❌ Denied| Block3
    
    Backup --> PROD[Deploy to PROD]
    PROD --> Gate6{PROD Validation}
    Gate6 -->|✅ Pass| Success[✅ Success]
    Gate6 -->|❌ Fail| Rollback[🔙 Rollback]
    
    style Gate1 fill:#FFE4B5
    style Gate2 fill:#FFE4B5
    style Gate3 fill:#FFE4B5
    style Gate4 fill:#90EE90
    style Gate5 fill:#FFD700
    style Gate6 fill:#FF6347
    style Backup fill:#1E90FF
    style Success fill:#32CD32
    style Rollback fill:#FF0000
```
