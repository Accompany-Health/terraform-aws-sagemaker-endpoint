# Architecture

## Overview

This Terraform module provisions a complete AWS SageMaker real-time inference stack: model, endpoint configuration, endpoint, and optional autoscaling. It is designed to be consumed by other Terraform configurations that need to deploy ML models behind SageMaker endpoints.

## Resource Graph

```
var.containers
var.sg_role_arn (optional)
        │
        ▼
┌──────────────────────────┐
│  aws_iam_role (optional) │──── Only created if sg_role_arn is null
│  aws_iam_policy_attach   │
└──────────┬───────────────┘
           │
           ▼
┌──────────────────────────┐
│  aws_sagemaker_model     │──── Single container or inference pipeline (up to 15)
└──────────┬───────────────┘
           │
           ▼
┌──────────────────────────────────┐
│  aws_sagemaker_endpoint_config   │──── Production variant, KMS encryption
└──────────┬───────────────────────┘
           │
           ▼
┌──────────────────────────┐
│  aws_sagemaker_endpoint  │──── The live inference endpoint
└──────────┬───────────────┘
           │
           ▼ (optional, when autoscaling_config is set)
┌──────────────────────────────────┐
│  aws_appautoscaling_target       │
│  aws_appautoscaling_policy       │──── Target-tracking on InvocationsPerInstance
└──────────────────────────────────┘
```

## Key Design Decisions

- **Conditional IAM role**: If consumers already have a SageMaker execution role, they pass `sg_role_arn` and no IAM resources are created. Otherwise the module creates a role with `AmazonSageMakerFullAccess`.
- **Single vs. pipeline models**: When `containers` has one element, it uses `primary_container`. When it has multiple, it uses the `container` block to create an inference pipeline.
- **Random suffix**: A 4-character random suffix is appended to model and config names to avoid naming collisions during blue/green deployments.
- **Autoscaling opt-in**: Autoscaling resources are only created when `autoscaling_config` is non-null, using target-tracking on `SageMakerVariantInvocationsPerInstance`.

## File Layout

| File | Responsibility |
|------|---------------|
| `main.tf` | Core resources: model, endpoint config, endpoint, autoscaling |
| `iam.tf` | Conditional IAM role and policy attachment |
| `data.tf` | Data sources (partition, IAM policy document) and locals |
| `variables.tf` | All input variables |
| `outputs.tf` | Module outputs (endpoint name, config name, role ARN) |
| `providers.tf` | Required providers and version constraints |
| `tests/` | Terraform test files |
