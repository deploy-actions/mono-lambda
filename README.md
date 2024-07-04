# AWS Mono Lambda Deploy Action ƛ

GitHub Action to deploy AWS Lambda by CloudFormation

## About

Provision AWS Lambda, as well as IAM Role, CloudFront, Route53 RecordSet, and more. If you want to delete all of those resources, remove the cloudformation stack that was created.

## Simple Example of Usage

```yml
- name: Configure AWS credentials 🔑
  uses: aws-actions/configure-aws-credentials@main
  with:
    role-to-assume: ${{ vars.AWS_ROLE_ARN }}
    aws-region: ${{ vars.AWS_REGION }}

- name: Deploy Lambda Function ƛ
  id: lambda
  uses: deploy-actions/mono-lambda@v1
  with:
    host-mode: CUSTOM_DOMAIN
    image-uri: ECR_IMAGE_URI
    function-name: LAMBDA_FUNCTION_NAME
    certificate-arn: ACM_CERTI_ARN
    hosted-zone-id: Route53 Hosted Zone Id
    domains: |
      www.example.com
      api.example.com
      doc.example.com
    environment: |
      NODE_ENV=production
      DATABASE_URL=postgresql://admin@localhost:5432/simple-api
      test=test

- run: echo "${{ steps.lambda.outputs.host }}" // www.example.com
```

## Common Inputs

| Name                    | Description                                              | Mandatory | Default      |
| ----------------------- | -------------------------------------------------------- | --------- | ------------ |
| function-name           | Name of Lambda Function                                  | ✅        |              |
| image-uri               | Docker Image URI                                         | ✅        |              |
| architecture            | Architecture for the Lambda function (x86_64 or arm64)   |           | x86_64       |
| memory-size             | Memory size for the Lambda function (in MB)              |           | 1024         |
| ephemeral-storage-size  | Ephemeral storage size for the Lambda function (in MB)   |           | 512          |
| timeout                 | Timeout for the Lambda function (in seconds)             |           | 10           |
| provisioned-concurrency | Provisioned concurrency for the Lambda function          |           | 0            |
| environment             | Lambda Function Environment["Variables"]                 |           |              |
| host-mode               | Method of Hosting Lambda Endpoint (NONE or FUNCTION_URL) |           | function-url |

## Additional Inputs for CUSTOM_DOMAIN mode

| Name            | Description                    | Mandatory | Default |
| --------------- | ------------------------------ | --------- | ------- |
| host-mode       | must be set `CUSTOM_DOMAIN`    | ✅        |         |
| hosted-zone-id  | route53 hosting zone id        | ✅        |         |
| certificate-arn | acm certificate arn            | ✅        |         |
| domains         | custom domain list joined "\n" | ✅        |         |

## Outputs

- host is exists when host-mode set `FUNCTION_URL` or `CUSTOM_DOMAIN`
  | Name | Description | Optional |
  | ---- | -------------------- | -------- |
  | host | Mono Lambda Endpoint | ✅ |
