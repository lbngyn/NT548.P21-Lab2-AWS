# NT548 Lab 2 Pipeline Deployment Guide on AWS

## Step 1: Configure AWS CLI

First, configure the AWS CLI to connect to your AWS account:

```bash
aws configure
```

You will be prompted to provide the following:

- **AWS Access Key ID**
- **AWS Secret Access Key**
- **Default region name**: `ap-southeast-1`
- **Default output format**: `json` (or leave blank)

---

## Step 2: Create Required Secrets

### List of Secret Keys

| Secret Key          | Description                                                                         |
| ------------------- | ----------------------------------------------------------------------------------- |
| `REGION`            | AWS deployment region (e.g., `ap-southeast-1`)                                      |
| `S3_BUCKET`         | Name of the S3 bucket for storing CodePipeline artifacts                            |
| `KEY_NAME`          | Name of the EC2 SSH key pair                                                        |
| `AMI_ID`            | ID of the Amazon Machine Image used to launch EC2 instances                         |
| `AVAILABILITY_ZONE` | AWS availability zone for EC2 and related resources (e.g., `ap-southeast-1a`)       |
| `GITHUB_TOKEN`      | GitHub Personal Access Token to access private repositories                         |
| `ALLOWED_SSH_IP`    | IP address range allowed for SSH access to EC2 (e.g., `0.0.0.0/0` or a specific IP) |

---

### Creating Secrets using PowerShell + AWS CLI

Create a dictionary of secret key-value pairs:

```powershell
$secrets = @{
  "REGION" = "ap-southeast-1"
  "S3_BUCKET" = "cloudformation-bucket-22520969"
  "KEY_NAME" = "lab2"
  "AMI_ID" = "ami-05c261f9eb9de6a80"
  "AVAILABILITY_ZONE" = "ap-southeast-1a"
  "GITHUB_TOKEN" = "ghp_xxx"         # Replace with your actual GitHub token
  "ALLOWED_SSH_IP" = "123.4.567.890/32" # Replace with your public IP or a valid IP range
}

foreach ($name in $secrets.Keys) {
    aws secretsmanager create-secret `
        --name $name `
        --secret-string $secrets[$name]
}
```

---

## Step 3: Deploy CodePipeline using CloudFormation

Run the following command to create the CodePipeline CloudFormation stack:

```bash
aws cloudformation create-stack \
  --stack-name NT548-lab2-codepipeline \
  --template-body file://codepipeline.yml \
  --capabilities CAPABILITY_IAM CAPABILITY_NAMED_IAM \
  --parameters ParameterKey=GitHubOwner,ParameterValue=lbngyn
```

- Replace `lbngyn` with your actual GitHub username or organization.

---

## Important Notes

- Make sure **all required secrets** are created in AWS Secrets Manager **before** stack deployment.
- The `stack-name` used for the CodePipeline must be **different** from the stack used to deploy infrastructure (currently named `NT548-lab2`).
- Ensure your GitHub token has the necessary permissions (`repo`, `workflow`) for pipeline access.
