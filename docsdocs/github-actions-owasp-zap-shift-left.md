# Implementing Shift-Left Security Scanning in GitHub Actions with OWASP ZAP

Shift-left security moves vulnerability detection earlier in the SDLC. This guide shows how to integrate OWASP ZAP (free, open-source DAST tool) into GitHub Actions CI/CD pipelines to scan web apps automatically on every push/pull request.

Target searches: GitHub Actions security scanning, OWASP ZAP GitHub Actions, shift-left security CI/CD.

## Why Shift-Left with OWASP ZAP?
- Catches issues before production (reduce breach risk).  
- Free vs. paid SAST/DAST tools.  
- GitHub-native: no extra SaaS needed.  
- Complements SCA (e.g., Dependabot) and SAST (e.g., CodeQL).

## Prerequisites
- GitHub repo with a web app (Node.js, Python Flask/Django, etc.).  
- Docker installed locally (for testing ZAP).  
- Basic GitHub Actions YAML knowledge.

## Step-by-Step: Add OWASP ZAP to GitHub Actions

1. **Create Workflow File**  
   In `.github/workflows/security-scan.yml`:

   ```yaml
   name: Shift-Left Security Scan with OWASP ZAP

   on:
     push:
       branches: [ main ]
     pull_request:
       branches: [ main ]

   jobs:
     zap-scan:
       runs-on: ubuntu-latest
       steps:
         - name: Checkout code
           uses: actions/checkout@v4

         - name: Set up Docker Buildx
           uses: docker/setup-buildx-action@v3

         - name: Run OWASP ZAP Baseline Scan
           uses: zaproxy/action-baseline@v0.12.0
           with:
             target: 'http://your-app-url:8080/'  # Change to your app's URL or use containerized app
             format: markdown
             fail_on_alert: true  # Fail build on high-risk findings

         - name: Upload ZAP Report
           if: always()
           uses: actions/upload-artifact@v4
           with:
             name: zap-report
             path: report.md
