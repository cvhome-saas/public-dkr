# public-dkr

This repository uses GitHub Actions to pull Docker images from Docker Hub and push them to AWS ECR Public.

## Setup Instructions

1. **Create an AWS ECR Public Registry:**
   - Go to the AWS ECR console.
   - Create a public registry and choose an alias (e.g., `myregistry`).
   - Note down your registry alias.

2. **Set up AWS Credentials in GitHub Secrets:**
   - In your GitHub repository, go to Settings > Secrets and variables > Actions.
   - Add the following secrets:
     - `AWS_ACCESS_KEY_ID`: Your AWS access key ID.
     - `AWS_SECRET_ACCESS_KEY`: Your AWS secret access key.
   - Ensure the AWS user has permissions for ECR Public operations.

3. **Update the Workflow:**
   - In `.github/workflows/push-images.yml`, replace `YOUR_REGISTRY_ALIAS` with your actual ECR public registry alias.

4. **Add More Images:**
   - To add more images, edit the `matrix.include` section in the workflow file.
   - Add entries like:
     ```yaml
     - image: ubuntu
       tag: 20.04
     ```

5. **Run the Workflow:**
   - Push to the `main` branch or use the manual trigger in the Actions tab.
   - The workflow will pull the specified images and push them to your ECR Public registry.

## Current Images

- node:20.15.0-alpine
- postgres:15-alpine