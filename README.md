- aws configure

- Setup giá trị cho các key sau trên aws secret manager:
  REGION
  S3_BUCKET
  KEY_NAME
  AMI_ID: AMI ID
  AVAILABILITY_ZONe
  GITHUB_TOKEN
  ALLOWED_SSH_IP

- Hoặc setup thông qua lệnh AWS CLI
  $secrets = @{
  "REGION": "ap-southeast-1"
  "S3_BUCKET": "cloudformation-bucket-22520969"
  "KEY_NAME": "lab2"
  "AMI_ID": "ami-05c261f9eb9de6a80"
  "AVAILABILITY_ZONE": "ap-southeast-1a"
  "GITHUB_TOKEN": ""
  }

foreach ($name in $secrets.Keys) {
    aws secretsmanager create-secret `
        --name $name `
        --secret-string $secrets[$name]
}

Sau đó chạy lệnh để khởi động pipeline

aws cloudformation create-stack `--stack-name NT548-lab2`
--template-body file://codepipeline.yml `--capabilities CAPABILITY_IAM CAPABILITY_NAMED_IAM`
--parameters ParameterKey=GitHubOwner,ParameterValue=<Your GitHub account's name>
