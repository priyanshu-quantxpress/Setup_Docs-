This documentation provides a comprehensive, end-to-end guide for setting up, deploying, running, and maintaining the **Bull8 UAT environment** on an offline Linux server. The goal of this guide is to establish a clear, repeatable, and production-aligned deployment process that can be followed consistently across teams and environments without relying on internet connectivity during installation or execution.

### Intended Audience

This guide is written to support **developers**, **DevOps engineers**, and **system administrators**, providing a structured and practical reference that follows industry-standard deployment practices. It focuses not only on installation steps but also on operational clarity — explaining how components interact, how services should be initialized, and how to validate each stage of the deployment.

### What This Guide Covers

This documentation covers the complete lifecycle of deployment, organized in a step-by-step format so that the entire environment can be recreated reliably from scratch whenever required.

| Area | Description |
|------|-------------|
| Environment Preparation | System prerequisites and initial server configuration |
| Offline Dependency Handling | Installing packages and libraries without internet access |
| Service Initialization | Starting and managing required background services |
| Docker-Based Deployment | Building, transferring, and running application containers |
| Configuration Management | Managing environment variables, config files, and secrets |
| Verification Procedures | Validating each stage of the deployment |
| Troubleshooting | Common issues and resolution steps |