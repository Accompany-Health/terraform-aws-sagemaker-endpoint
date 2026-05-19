# Dependencies

## Terraform Providers

| Provider | Version Constraint | Purpose |
|----------|-------------------|---------|
| [hashicorp/aws](https://registry.terraform.io/providers/hashicorp/aws/latest) | ~> 5.0 | All AWS resources (SageMaker, IAM, AutoScaling) |
| [hashicorp/random](https://registry.terraform.io/providers/hashicorp/random/latest) | >= 3.6.0 | Random suffix for resource naming |

## AWS Services Used

| Service | Resources | Purpose |
|---------|-----------|---------|
| SageMaker | Model, EndpointConfiguration, Endpoint | ML model hosting and inference |
| IAM | Role, Policy Attachment | Execution role for SageMaker (optional) |
| Application Auto Scaling | Target, Policy | Endpoint instance scaling (optional) |
| KMS | (referenced by ARN) | Encryption of endpoint storage volumes (optional) |

## Internal Dependencies

This module has no internal Accompany Health dependencies. It is a standalone library module consumed by other Terraform configurations.

## Consumers

This module is consumed by Terraform configurations that deploy SageMaker endpoints, typically in:

- `Accompany-Health/environments` — production and dev endpoint deployments
- Any repo that provisions ML inference infrastructure via Terraform

## Upstream Requirements

- A container image in ECR (or a SageMaker model package ARN)
- Optionally, model artifacts in S3
- Optionally, a pre-existing IAM execution role
