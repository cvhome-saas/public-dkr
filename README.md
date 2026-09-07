# public-dkr

This repository uses GitHub Actions to pull Docker images from Docker Hub and push them to AWS ECR Public.

## Setup Instructions

1. **Create an AWS ECR Public Registry:**
   - Go to the AWS ECR console.
   - Create a public registry and choose an alias (e.g., `myregistry`).
   - Note down your registry alias.

2. **Let GitHub assume the org's publish role (OIDC, no static keys):**
   - The workflow assumes the role in the **organisation-level** Actions secret `AWS_TEMPLATE_PUBLISH_ROLE_ARN`
     (shared with cvhome-platform's bootstrap publish). Its trust policy must accept this repository too, and
     its permissions must include ECR Public. Trust policy shape (the GitHub OIDC provider exists once per
     account: `token.actions.githubusercontent.com`, audience `sts.amazonaws.com`):
     ```json
     {"Version": "2012-10-17", "Statement": [{"Effect": "Allow",
       "Principal": {"Federated": "arn:aws:iam::<account>:oidc-provider/token.actions.githubusercontent.com"},
       "Action": "sts:AssumeRoleWithWebIdentity",
       "Condition": {"StringEquals": {"token.actions.githubusercontent.com:aud": "sts.amazonaws.com"},
                     "StringLike": {"token.actions.githubusercontent.com:sub": ["repo:cvhome-saas/public-dkr:*", "repo:cvhome-saas/cvhome-platform:*"]}}}]}
     ```
   - Attach a policy allowing `ecr-public:GetAuthorizationToken`, `sts:GetServiceBearerToken`,
     `ecr-public:DescribeRegistries`, `ecr-public:CreateRepository`, `ecr-public:BatchCheckLayerAvailability`,
     `ecr-public:InitiateLayerUpload`, `ecr-public:UploadLayerPart`, `ecr-public:CompleteLayerUpload`,
     `ecr-public:PutImage`, `ecr-public:DescribeRepositories` (ECR Public lives in us-east-1).
   - Nothing to add per repo: the org secret is visible here. Remove this repo's `AWS_ACCESS_KEY_ID` /
     `AWS_SECRET_ACCESS_KEY` if they exist; nothing reads them any more.

3. **Run the Workflow:**
   - The workflow automatically detects your ECR public registry alias.
   - Push to the `main` branch or use the manual trigger in the Actions tab.
   - The workflow will pull the specified images and push them to your ECR Public registry.

4. **Add More Images:**
   - To add more images, edit the `matrix.include` section in the workflow file.
   - Add entries like:
     ```yaml
     - image: ubuntu
       tag: 20.04
     ```

## Current Images

- node:20.15.0-alpine
- postgres:15-alpine