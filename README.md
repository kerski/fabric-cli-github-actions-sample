# Fabric CLI CI/CD Demo

This repository contains a **demo CI/CD pipeline** for Microsoft Fabric using the Fabric CLI and GitHub Actions. This example demonstrates how to automatically deploy Power BI reports and semantic models to Microsoft Fabric workspaces when changes are detected in your repository.

## Table of Contents

1. [Features](#features)
2. [Prerequesites](#prerequisites)
3. [Setup Instructions](#setup-instructions)
4. [Workflow Overview](#workflow-overview)
5. [Triggering the Pipeline](#triggering-the-pipeline)
6. [Monitoring and Troubleshooting](#monitoring-and-troubleshooting)
7. [Customization](#customization)
8. [Security Best Practices](#security-best-practices)
9. [Support and Contributing](#support-and-contributing)

## Features

- 🚀 **Automated Deployment**: Automatically deploy semantic models and reports when changes are pushed
- 🔍 **Smart Change Detection**: Only deploy items that have actually changed
- 🔄 **Data Refresh**: Automatically trigger data refreshes after deployment
- 📊 **Deployment Summary**: Example reporting of deployment status and results
- 🛡️ **Error Handling**: Basic error handling with retry mechanisms
- 📈 **Artifact Management**: Upload logs and artifacts for troubleshooting

## Prerequisites

Before setting up this CI/CD pipeline, ensure you have the following:

### 1. GitHub Repository Access
- You have a GitHub repository with administrative rights
- You can configure GitHub Actions workflows
- You can manage repository secrets and variables

### 2. Microsoft Fabric/Power BI Environment
- You have an Azure/Microsoft 365 tenant with Power BI Premium or Fabric capacity
- You have a premium-backed capacity workspace connected to your repository
-- Your Power BI tenant has **[XMLA Read/Write Enabled](https://learn.microsoft.com/en-us/power-bi/enterprise/service-premium-connect-tools#enable-xmla-read-write)**

### 3. Service Principal
You need a service principal with the following requirements:
- A service principal with appropriate permissions:
  - Dataset.ReadWrite.All
  - Workspace.Read.All
  - Report.ReadWrite.All
- **[Power BI tenant must allow service principals to use Fabric APIs](https://learn.microsoft.com/en-us/power-bi/enterprise/service-premium-service-principal?wt.mc_id=MVP_369005#enable-service-principals)**
- Service principal must have at least **Member** role in the target workspace

### 4. Required Permissions
- **Fabric Workspace**: Member role or higher
- **Power BI Admin**: XMLA Read/Write enabled
- **GitHub Repository**: Admin rights to configure secrets and actions

## Setup Instructions

### Step 1: Configure GitHub Secrets

Navigate to your GitHub repository settings and add the following secrets:

#### Required Secrets
- `CLIENT_ID`: Your service principal's Application (Client) ID
- `CLIENT_SECRET`: Your service principal's client secret
- `TENANT_ID`: Your Azure/Microsoft 365 tenant ID (GUID format)

#### Required Variables
- `WORKSPACE_NAME`: The name of your Fabric workspace (alphanumeric, underscores, and hyphens only)

### Step 2: Enable GitHub Actions

1. Go to your repository **Settings** → **Actions** → **General**
2. Ensure **Actions permissions** are set to allow GitHub Actions
3. Configure **Workflow permissions** to have appropriate access

### Step 3: Repository Structure

Organize your Power BI project files in the following structure:

```
your-repo/
├── .github/
│   └── workflows/
│       └── pbip-ci-cd.yml
├── YourSemanticModel.Dataset/
│   ├── definition.pbidt
│   ├── model.bim
│   └── ...
├── YourReport.Report/
│   ├── definition.pbir
│   ├── report.json
│   └── ...
└── README.md
```

### Step 4: Configure Workspace Git Integration

1. Open your Fabric workspace in the browser
2. Go to **Workspace settings** → **Git integration**
3. Connect your workspace to this GitHub repository
4. Configure the appropriate branch (typically `main`)
5. Complete the initial sync

### Step 5: Test the Pipeline

1. Make a change to a semantic model or report file
2. Commit and push the changes to the `main` branch
3. Navigate to **Actions** tab in your GitHub repository
4. Monitor the workflow execution

## Workflow Overview

The demo CI/CD pipeline consists of several jobs that run sequentially:

### 1. Validate and Plan (`validate-and-plan`)
- ✅ Validates environment variables and authentication
- 🔍 Identifies changed files and determines what needs deployment
- 📋 Plans the deployment strategy

### 2. Deploy Semantic Models (`deploy-semantic-models`)
- 📊 Deploys changed semantic models to the workspace
- 🆔 Captures semantic model IDs for downstream jobs
- ⚠️ Runs only if semantic model changes are detected

### 3. Deploy Reports (`deploy-reports`)
- 📈 Deploys changed reports to the workspace
- 🔗 Automatically updates report connections to reference deployed semantic models
- ⚠️ Runs only if report changes are detected

### 4. Refresh and Validate (`refresh-and-validate`)
- 🔄 Triggers data refresh for deployed semantic models
- 🧪 **Example placeholder** for data quality validation tests
- 📊 Monitors refresh status

### 5. Deployment Summary (`deployment-summary`)
- 📋 Generates example deployment summary
- 📊 Reports on job status and deployed items
- 🕒 Always runs to provide visibility

## Triggering the Pipeline

The pipeline runs automatically on:

- **Push to main branch**: Deploys changes to production workspace
- **Pull requests**: Validates changes without deployment
- **Manual trigger**: Can be triggered manually from GitHub Actions UI

## Monitoring and Troubleshooting

### Viewing Deployment Status
1. Go to the **Actions** tab in your GitHub repository
2. Click on the most recent workflow run
3. Review the job status and deployment summary

### Accessing Logs
- Each job uploads artifacts containing logs if failures occur
- Download artifacts from the workflow run page
- Check the deployment summary for high-level status

### Common Issues

**Authentication Failed**
- Verify `CLIENT_ID`, `CLIENT_SECRET`, and `TENANT_ID` are correct
- Ensure service principal has proper permissions
- Check if tenant allows service principal API access

**Workspace Not Found**
- Verify `WORKSPACE_NAME` matches exactly (case-sensitive)
- Ensure the workspace exists and you have access
- Check workspace naming conventions (alphanumeric, _, - only)

**Import Failures**
- Verify file structure matches Power BI project format
- Check for corrupted or missing files
- Review semantic model and report dependencies

## Customization

### Modifying Timeouts
Adjust timeout values in the workflow file based on your deployment needs:
- Validation: 10 minutes (fast operations)
- Semantic Models: 30 minutes (medium complexity)
- Reports: 20 minutes (faster than semantic models)
- Data Refresh: 45 minutes (can be slow for large datasets)

### Adding Data Quality Validation
The pipeline includes a **placeholder example** for data quality validation. You can implement:
- Great Expectations tests
- dbt tests
- Custom validation scripts
- Performance benchmarks

*More detailed examples and guidance on data quality validation will be added in future updates to this demo.*

### Environment-Specific Deployments
Extend this example pipeline to support multiple environments:
- Add environment-specific workspace variables
- Create separate workflow files for different environments
- Implement approval processes for production deployments

## Security Best Practices

- 🔐 Store all sensitive information in GitHub Secrets
- 🛡️ Use service principals for automated authentication
- 🔄 Regularly rotate client secrets
- 📊 Monitor deployment logs for security events
- 🚫 Never commit credentials to the repository

## Support and Contributing

This is an **example/demo pipeline**. For production use:
1. Thoroughly test in your environment
2. Customize based on your specific requirements
3. Add additional error handling and monitoring
4. Implement proper security practices

*Note: Some content in this repository was generated with AI assistance and then subsequently reviewed and revised.*

For additional insights, tutorials, and best practices related to Microsoft Fabric and Power BI testing and monitoring, please visit [blog.kerski.tech](https://blog.kerski.tech).

For issues and questions:
1. Check the troubleshooting section above
2. Review workflow logs and artifacts
3. Consult Microsoft Fabric documentation
4. Open an issue in this repository for demo-specific questions

## License

This project is licensed under the MIT License - see the LICENSE file for details.
