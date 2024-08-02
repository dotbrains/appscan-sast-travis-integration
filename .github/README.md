# HCL AppScan SAST Integration with Travis CI
![yaml](https://img.shields.io/badge/-YAML-black?style=flat-square&logo=yaml&logoColor=red)
![Linux](https://img.shields.io/badge/-Linux-FCC624?style=flat-square&logo=linux&logoColor=black)
![travisci](https://img.shields.io/badge/-TravisCI-4e4847?style=flat-square&logo=travisci&logoColor=e0da53)

This repository contains a sample project that demonstrates how to integrate HCL AppScan SAST with Travis CI.

## Getting Started

To begin using HCL AppScan SAST, sign up for an account on [AppScan on Cloud (ASoC)](https://cloud.appscan.com/) if you don't already have one.

### Steps to Get Started:

1. **Create a New Application**:
   - Log in to your ASoC account and create a new application. This will generate an Application ID that you will use in your CI/CD pipeline.

2. **Obtain API Credentials**:
   - Create a new API key and secret by visiting the API management section in your ASoC account. These credentials are necessary for authenticating API requests during the scan.

## How to Use

### 1. Add the HCL AppScan Environment Variables to Travis CI

To integrate HCL AppScan SAST with Travis CI, you need to add the following environment variables to your Travis CI repository settings:

- `APPSCAN_APP_ID`: The Application ID of the application in ASoC.
- `APPSCAN_API_KEY`: The API key for accessing ASoC.
- `APPSCAN_API_SECRET`: The API secret associated with your ASoC API key.

These environment variables allow your CI/CD pipeline to authenticate and communicate with the HCL AppScan services.

For an example `.env` file, you can refer to the [`.env.example`](/.env.example) file provided in this repository.

### 2. Configure `.travis.yml`

Add the following configuration to your `.travis.yml` file to set up the integration:

```yaml
configs:
  - &APPSCAN_CONFIG
    - APPSCAN_API_KEY=${APPSCAN_API_KEY}
    - APPSCAN_API_SECRET=${APPSCAN_API_SECRET}
    - APPSCAN_APP_ID=${APPSCAN_APP_ID}
    - APPSCAN_NAME=${TRAVIS_BRANCH}-${TRAVIS_COMMIT}

stages:
  - name: Static Code Analysis
    if: type IN (pull_request, push) AND branch IN (development, main, master)

jobs:
  include:
    - stage: Static Code Analysis
      env:
        - *APPSCAN_CONFIG
      addons:
        apt:
          packages:
            - jq
      script: bash appscan/appscan_sca.sh
```

### 3. Add the HCL AppScan SAST Script

Clone this repository and copy the `appscan` directory to the root of your project:

```bash
git clone \
 https://github.com/dotbrains/appscan-sast-travis-integration \
 appscan-SAST
mv appscan-SAST/appscan .
rm -rf appscan-SAST
```

## How Does `appscan_sca.sh` Work?

The provided Bash script integrates HCL AppScan SAST with your CI/CD pipeline by performing static code analysis. Here's how it works:

1. **Download AppScan Client**: The script downloads the AppScan client for Linux from ASoC.
2. **Authenticate**: It authenticates with ASoC using the provided API key and secret.
3. **Scan Configuration**: Based on the CI event type (e.g., pull request), it prepares the scan, specifying configuration options such as the scan type, output directory, and verbosity level.
4. **Submit for Analysis**: The prepared scan is submitted to ASoC for analysis. The script handles uploading the source code and monitoring the scan's progress.
5. **Retrieve Results**: After the scan is complete, the script retrieves the scan results in HTML format and extracts critical information such as the number of issues found at various severity levels.
6. **Generate Report**: It generates a report detailing the issues found, focusing on critical and high-severity issues.
7. **Pass/Fail Criteria**: If the scan finds any critical or high-severity vulnerabilities, the script will fail the CI/CD pipeline, ensuring that security issues are addressed before proceeding. If no issues are found or only low/medium-severity issues exist, the pipeline passes.

## Why Use HCL AppScan for SAST?

* **Comprehensive Security Analysis**: HCL AppScan provides thorough static code analysis, helping to identify vulnerabilities early in the development process.
* **Seamless CI/CD Integration**: The tool is designed to integrate easily with CI/CD pipelines, automating security scans with every commit or pull request.
* **Actionable Insights**: AppScan provides detailed reports and remediation guidance, allowing developers to quickly address and fix identified vulnerabilities.

## License

© Nicholas Adamou.

This software is free to use and may be redistributed under the terms specified in the [LICENSE] file.

[license]: LICENSE
