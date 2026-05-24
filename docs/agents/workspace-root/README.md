# Lexai Project

## Project Structure
*   `client-front`: React Frontend (Vite/Bun).
*   `gateway`: Spring Boot API Gateway.
*   `file-service`: Spring Boot File Service.
*   `infra`: Terraform Infrastructure code.

## Deployment Guide (AWS Dev Environment)

### 1. Prerequisites
*   AWS CLI configurd with appropriate permissions.
*   Terraform installed.
*   Maven & Java 21 (for local builds).
*   Bun (for frontend builds).

### 2. Infrastructure Setup (Terraform)
Navigate to the dev environment configuration:
```bash
cd infra/envs/dev
```

Initialize Terraform:
```bash
terraform init
```

Apply the infrastructure. **Crucial**: You must provide the sensitive secrets during this step.
You can do this by creating a `terraform.tfvars` file (DO NOT COMMIT THIS FILE) or passing them via command line/environment variables.

**Required Secrets:**
*   `anthropic_api_key`: Your Anthropic API Key.
*   `jwt_secret`: A secure random string for JWT signing.
*   `google_client_id`: Google OAuth Client ID.
*   `google_client_secret`: Google OAuth Client Secret.

**Example `terraform.tfvars`:**
```hcl
anthropic_api_key    = "sk-ant-..."
jwt_secret           = "very-secure-secret..."
google_client_id     = "..."
google_client_secret = "..."
```

Run Apply:
```bash
terraform apply
```

### 3. CI/CD (GitHub Actions)
The project uses GitHub Actions for deployment to the `dev` environment.
*   **Workflow**: `.github/workflows/deploy-dev.yml`
*   **Trigger**: Push to `dev` branch.

**Required GitHub Repository Secrets:**
*   `AWS_ACCESS_KEY_ID`: AWS Access Key for deployment user.
*   `AWS_SECRET_ACCESS_KEY`: AWS Secret Key.
*   `AWS_ACCOUNT_ID`: Your AWS Account ID.

### 4. Architecture Notes
*   **Frontend**: Deployed to an S3 bucket (`<env>-lexai-frontend`) and served via CloudFront.
*   **Backend**: Deployed as ECS Fargate tasks using AWS Copilot/Terraform patterns.
*   **Service Discovery**: Internal communication between Gateway and File Service uses AWS Cloud Map (`dev.lexai.local`).
*   **Secrets**: Stored in AWS Secrets Manager (`dev/lexai/gateway-secrets`) and injected into containers at runtime.
