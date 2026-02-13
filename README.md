# Serverless Image Processing Pipeline (AWS + Terraform)

## Overview

This project implements a multi-stage, event-driven serverless image
processing pipeline using AWS services and Infrastructure as Code
(Terraform).

The system allows users to upload images via a secure API endpoint.
Uploaded images are processed asynchronously (converted to grayscale),
and the results are stored separately. The architecture is fully
serverless, scalable, and cost-efficient.

------------------------------------------------------------------------

## Architecture

Client\
↓\
API Gateway\
↓\
Upload Lambda\
↓\
S3 (uploads bucket)\
↓\
SQS (processing queue)\
↓\
Process Lambda\
↓\
S3 (processed bucket)\
↓\
SQS (results queue)\
↓\
Log Lambda\
↓\
CloudWatch Logs

------------------------------------------------------------------------

## Services Used

-   AWS Lambda -- Serverless compute for all processing stages\
-   Amazon S3 -- Object storage for uploaded and processed images\
-   Amazon SQS -- Message queues for asynchronous orchestration\
-   Amazon API Gateway -- Secure public API endpoint\
-   AWS IAM -- Least-privilege access control\
-   AWS Secrets Manager -- Secure API key storage\
-   Terraform -- Infrastructure as Code provisioning

------------------------------------------------------------------------

## Infrastructure as Code

All infrastructure is defined using Terraform inside the `terraform/`
directory.

### Key Resources Provisioned

-   2 S3 Buckets:
    -   \*-uploads
    -   \*-processed
-   Lifecycle rule: Deletes upload objects older than 7 days
-   2 SQS Queues:
    -   Image processing queue
    -   Processing results queue
-   3 Lambda functions:
    -   upload-image
    -   process-image
    -   log-notification
-   API Gateway with:
    -   API Key required
    -   Rate limiting (\~20 requests per minute)
-   IAM Role with least-privilege permissions
-   Secret stored in Secrets Manager

------------------------------------------------------------------------

## Security Design

-   No hardcoded credentials
-   Dedicated IAM role for Lambda functions
-   Least privilege policy design
-   API Key required for all uploads
-   Secrets stored securely in Secrets Manager
-   Rate limiting configured to prevent abuse

------------------------------------------------------------------------

## Deployment Instructions

### Prerequisites

-   AWS CLI installed and configured
-   Terraform installed
-   Proper AWS IAM permissions

### Initialize Terraform

terraform init

### Review Plan

terraform plan

### Deploy Infrastructure

terraform apply

After deployment, Terraform will output the API Gateway Invoke URL and
other resource identifiers.

------------------------------------------------------------------------

## Testing the API

Example using curl:

curl -X POST
https://`<api-id>`{=html}.execute-api.`<region>`{=html}.amazonaws.com/prod/v1/images/upload
-H "x-api-key: YOUR_API_KEY" -F "file=@image.jpg"

Expected response:

{ "message": "Upload accepted", "upload_id": "unique-id" }

------------------------------------------------------------------------

## Cleanup

To destroy all infrastructure:

terraform destroy

Always run destroy after testing to avoid unnecessary charges.

------------------------------------------------------------------------

## Conclusion

This project demonstrates:

-   Practical serverless system design
-   Secure API management
-   Event-driven architecture implementation
-   Infrastructure automation with Terraform
-   Production-style cloud engineering practices
