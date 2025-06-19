# Actions-Autofix 🚀

**Intelligent automated tooling to detect, analyze, and report GitHub Actions workflow failures using AI-powered insights.**

## 📋 Overview

Actions-Autofix is an intelligent GitHub Actions workflow that automatically monitors your repository's CI/CD pipelines and provides instant, AI-powered analysis when workflows fail. Instead of manually digging through logs to understand what went wrong, this tool leverages the power of AI to deliver structured failure analysis directly to your GitHub issues.

## ✨ Key Features

### 🤖 **AI-Powered Failure Analysis**
- Uses Azure OpenAI's GPT-4 1-nano model to analyze workflow failures
- Provides structured technical analysis without proposing fixes
- Identifies root causes, error categories, and impact assessments

### 🔍 **Intelligent Monitoring**
- Monitors all workflows in your repository automatically
- Triggers only on actual failures (not successes or cancellations)
- Supports branch filtering (currently configured for `main` and `develop`)

### 📝 **Smart Issue Management**
- Automatically creates detailed GitHub issues for workflow failures
- Prevents duplicate issues by detecting similar failures
- Adds comments to existing issues for recurring failures
- Includes rich metadata: commit info, workflow links, timestamps

### 📊 **Comprehensive Failure Reporting**
- Fetches and analyzes complete workflow logs
- Extracts failed job details and execution context
- Provides direct links to failed workflow runs and commits
- Includes commit messages for additional context

## 🔧 How It Works

### 1. **Trigger Detection**
The workflow uses the `workflow_run` trigger to monitor all repository workflows:

```yaml
on:
  workflow_run:
    workflows: ["*"]          # Monitor all workflows
    types: [completed]        # Only when workflows complete
    branches: [main, develop] # Only on specified branches
```

### 2. **Failure Filtering**
Only activates when a workflow actually fails:

```yaml
if: ${{ github.event.workflow_run.conclusion == 'failure' }}
```

### 3. **Log Collection**
- Retrieves detailed logs from all failed jobs
- Handles large log files with intelligent truncation
- Collects job metadata (names, timestamps, URLs)

### 4. **AI Analysis**
- Sends failure data to Azure OpenAI GPT-4 1-nano for analysis
- Receives structured technical analysis focusing on:
  - **Root Cause**: Primary reason for failure
  - **Technical Details**: Specific breakdown of what went wrong
  - **Error Categories**: Classification of error types
  - **Impact Assessment**: What functionality is affected
  - **Additional Context**: Other relevant observations

### 5. **Issue Creation/Updates**
- Creates new issues with comprehensive failure reports
- Updates existing issues with new failure instances
- Uses intelligent labeling for easy categorization

## 📁 Repository Structure

```
Actions-Autofix/
├── .github/
│   └── workflows/
│       ├── autofix-on-failure.yml   # Main AI-powered failure analysis workflow
│       └── test-failure.yml         # Test workflow for validation (optional)
├── LICENSE                          # Project license
└── README.md                       # This comprehensive guide
```

## 🚀 Setup Instructions

### Prerequisites
- GitHub repository with Actions enabled
- Issues feature enabled in your repository
- Access to GitHub's AI inference actions (if using GitHub-hosted models)

### Installation

1. **Copy the main workflow file** to your repository at `.github/workflows/autofix-on-failure.yml`

2. **Optional: Add the test workflow** to validate functionality at `.github/workflows/test-failure.yml`

3. **Configure branch monitoring** (optional):
   ```yaml
   branches: [main, develop, "release/*"]  # Customize as needed
   ```

4. **Customize workflow monitoring** (optional):
   ```yaml
   workflows: ["CI", "Tests", "Deploy"]    # Specific workflows only
   # OR
   workflows: ["*"]                        # All workflows (default)
   ```

### Required Permissions

The workflow automatically requests minimal required permissions:
- `issues: write` - To create and update issues
- `contents: read` - To access repository content
- `actions: read` - To fetch workflow logs

## 🧪 Testing & Validation

### Built-in Test Workflow

Actions-Autofix includes a comprehensive test workflow (`test-failure.yml`) that simulates various types of failures to validate the AI analysis system.

#### Available Test Scenarios

| Failure Type | Description | What It Tests |
|--------------|-------------|---------------|
| `test_failure` | Jest test failures in authentication & database modules | Unit test failures, assertion errors |
| `build_failure` | Python syntax errors, import errors, undefined variables | Compilation and build-time issues |
| `dependency_failure` | Missing npm packages, unavailable system commands | Dependency resolution problems |
| `environment_failure` | Missing env vars, config files, external services | Configuration and deployment issues |
| `timeout_failure` | Long-running process exceeding time limits | Performance and timeout problems |
| `script_error` | Permission denied, command not found, runtime errors | Script execution and system errors |

#### Running Tests

**Manual Testing:**
1. Navigate to Actions tab in your repository
2. Select "Test Failure Workflow"
3. Click "Run workflow"
4. Choose failure type from dropdown
5. Monitor both the test failure and subsequent AI analysis

**Automatic Testing:**
- Triggers on pushes to `main`/`develop` branches
- Runs default test failure scenario
- Perfect for CI/CD validation

#### Expected AI Analysis Examples

The AI will provide different analyses based on failure types:

**Test Failures:**
```
Root Cause: Authentication test failed due to incorrect password validation 
logic returning false for valid credentials, combined with missing database 
environment configuration causing connection timeouts.

Technical Details: Jest test suite failed with 2 assertion errors...
```

**Build Failures:**
```
Root Cause: Python import error due to missing 'nonexistent_module' 
dependency, followed by syntax errors in conditional statements missing 
colons and undefined variable references.

Technical Details: ModuleNotFoundError on line 3, SyntaxError on line 12...
```

**Environment Issues:**
```
Root Cause: Missing critical environment variables (REQUIRED_SECRET, 
DATABASE_URL, API_KEY) preventing application startup and configuration 
file access errors.

Technical Details: Environment variable validation failed, exit code 1...
```

## 📊 What You Get

### Real-World Issue Output

When a workflow fails, you'll get a detailed issue like this:

```markdown
🚨 Workflow Failure: CI on main

## Workflow Failure Report

### 📋 Failure Details
- **Workflow Name:** CI
- **Branch:** main
- **Commit SHA:** abc123...
- **Run Number:** 42
- **Triggered by:** developer-name
- **Event:** push

### 🔗 Links
- [View Failed Workflow Run](https://github.com/...)
- [View Commit](https://github.com/...)

### 📝 Commit Message
```
Fix user authentication bug
```

## 🔍 Failure Analysis

### Root Cause
Test failure in user authentication module due to missing environment variable

### Technical Details
The test suite failed because the TEST_DATABASE_URL environment variable was not set...

### Error Categories
- Environment Configuration
- Test Failure

### Impact Assessment
User authentication functionality testing is blocked...

### 🔧 Next Steps
1. Review the AI analysis above for failure details
2. Check the workflow logs for additional context
3. Implement necessary fixes based on the root cause
4. Re-run the workflow after fixes are applied
```

## 🏷️ Issue Labels

Issues are automatically tagged with:
- `bug` - Indicates a workflow failure
- `workflow-failure` - Specific failure type
- `automated` - Auto-generated issue
- `workflow:CI` - Dynamic label with workflow name
- `branch:main` - Dynamic label with branch name

## 🔧 Advanced Configuration

### Workflow Targeting
```yaml
# Monitor specific workflows only
workflows: ["CI", "Tests", "Security-Scan"]

# Monitor all workflows (default)
workflows: ["*"]

# Monitor workflows matching patterns
workflows: ["*-test", "*-build", "deploy-*"]
```

### Branch Management
```yaml
# Include specific branches
branches: [main, develop, "release/*", "hotfix/*"]

# Exclude branches (use branches-ignore instead)
branches-ignore: ["feature/*", "dependabot/*", "renovate/*"]
```

### AI Model Options
```yaml
# Fast and cost-effective (default - Azure OpenAI)
model: azure-openai/gpt-4-1-nano

# Alternative OpenAI models
model: openai/gpt-4o-mini
model: openai/gpt-4

# Alternative providers (check actions/ai-inference docs for available models)
model: anthropic/claude-3-sonnet
```

### Custom Failure Thresholds
```yaml
# Only analyze failures on specific events
on:
  workflow_run:
    workflows: ["*"]
    types: [completed]
    branches: [main]
    # Only trigger for push events, not PRs
    # Filter in job conditions: if: github.event.workflow_run.event == 'push'
```

## 📈 Performance & Costs

### Resource Usage
- **Compute**: ~2-3 minutes per analysis
- **API Calls**: 1 AI inference call per failure
- **Storage**: Issues created in your repository

### Cost Optimization Tips
1. **Use `azure-openai/gpt-4-1-nano`** for most scenarios (fast, cost-effective, default)
2. **Filter branches** to avoid analyzing feature branch failures
3. **Monitor specific workflows** instead of using `["*"]` if you have many workflows
4. **Set up branch protection** to prevent excessive triggering

### Scaling Considerations
- **Large repositories**: Consider workflow filtering
- **High-frequency failures**: Duplicate prevention reduces noise
- **Multiple teams**: Use labels for team-specific filtering
```

## 🚀 Quick Start Guide

### 1. **Installation**
```bash
# Clone or download the workflow files
mkdir -p .github/workflows
# Copy autofix-on-failure.yml to .github/workflows/
# Optionally copy test-failure.yml for validation
```

### 2. **First Test**
```bash
# Test the system with the included test workflow
git add .github/workflows/
git commit -m "Add Actions-Autofix system"
git push origin main

# Manually trigger test failure (GitHub UI):
# Actions → Test Failure Workflow → Run workflow → Choose failure type
```

### 3. **Monitor Results**
- **Step 1**: Watch test workflow fail (expected)
- **Step 2**: Watch Actions-Autofix workflow trigger
- **Step 3**: Check Issues tab for AI-generated analysis
- **Step 4**: Review the detailed failure analysis

### 4. **Customize for Your Needs**
```yaml
# Edit .github/workflows/autofix-on-failure.yml
workflows: ["Your-CI", "Your-Tests"]  # Monitor specific workflows
branches: [main, staging, production] # Monitor specific branches
```

## 🔒 Security & Privacy

- **Minimal Permissions**: Only requests necessary GitHub permissions
- **Log Privacy**: Workflow logs may contain sensitive information; ensure your repository visibility aligns with your security requirements
- **AI Processing**: Workflow logs are sent to AI models for analysis; review your organization's AI usage policies

## 🤝 Contributing

This is an open-source project. Feel free to:
- Report issues or bugs
- Suggest new features
- Submit pull requests
- Improve documentation

## 📄 License

This project is licensed under the terms specified in the LICENSE file.

## 🆘 Troubleshooting

### Common Issues

**Issue**: Actions-Autofix workflow doesn't trigger
- ✅ Verify the triggering workflow actually failed
- ✅ Check branch filters match your failed workflow's branch
- ✅ Ensure workflow name patterns are correct
- ✅ Confirm repository has Issues enabled

**Issue**: AI analysis is empty or generic
- ✅ Check if workflow logs were retrieved successfully
- ✅ Verify AI inference action has proper permissions
- ✅ Try a different AI model (azure-openai/gpt-4-1-nano vs openai/gpt-4o-mini)
- ✅ Check log truncation - very large logs may be cut off

**Issue**: Duplicate issues being created
- ✅ The system should prevent this automatically
- ✅ Check if existing issue labels match the search criteria
- ✅ Verify issue titles contain workflow and branch names

**Issue**: Missing workflow logs in analysis
- ✅ Confirm the workflow has `actions: read` permission
- ✅ Check if the failed jobs completed before log retrieval
- ✅ Verify repository visibility settings allow log access

### Debug Steps

1. **Check Actions-Autofix workflow logs**:
   - Go to Actions tab → Autofix on Workflow Failure
   - Review "Get workflow logs" step output
   - Check "Analyze failure with AI" step for errors

2. **Validate test workflow**:
   ```bash
   # Trigger test failure manually
   # Actions → Test Failure Workflow → Run workflow
   # Choose 'test_failure' → Run workflow
   ```

3. **Review permissions**:
   ```yaml
   permissions:
     issues: write    # Required for creating/updating issues
     contents: read   # Required for repository access
     actions: read    # Required for fetching workflow logs
   ```

### Getting Help

If you're still experiencing issues:
1. **Enable debug logging** in your workflow
2. **Check the Actions-Autofix workflow logs** for error details
3. **Verify AI inference action compatibility** with your GitHub setup
4. **Test with the included test-failure workflow** to isolate issues
5. **Review GitHub's AI inference action documentation**

## 📊 Monitoring & Analytics

### Key Metrics to Track
- **Failure detection rate**: How many failures are caught
- **Analysis quality**: How helpful are the AI insights
- **Resolution time**: Time from failure to fix implementation
- **False positives**: Unnecessary issues created

### Issue Management
```bash
# Find all autofix issues
# Filter by label: workflow-failure, automated

# Review analysis quality
# Look for issues with detailed root cause analysis

# Track resolution patterns
# Identify common failure types for process improvement
```

---

**Built with ❤️ for the GitHub Actions community**
