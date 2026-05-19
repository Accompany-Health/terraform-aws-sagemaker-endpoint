# Local Development

## Prerequisites

- [Terraform](https://developer.hashicorp.com/terraform/install) >= 1.0.7
- AWS credentials configured (via `aws sso login --profile <profile>` or environment variables)
- [TFLint](https://github.com/terraform-linters/tflint) (optional, for linting)
- [pre-commit](https://pre-commit.com/) (optional, for git hooks)

## Setup

```bash
terraform init
```

## Common Workflows

| Workflow | Command |
|---------|---------|
| Initialize | `terraform init` |
| Validate syntax | `terraform validate` |
| Plan changes | `terraform plan` |
| Run tests | `terraform test` |
| Lint | `tflint --config .config/.tflint.hcl` |
| Format | `terraform fmt` |
| Generate docs | `terraform-docs markdown . --config .config/.terraform-docs.yaml` |

## Running Tests

The module includes Terraform native test files in `tests/`:

```bash
terraform test
```

Tests require valid AWS credentials since they validate against the AWS provider schema.

## Pre-commit Hooks

This repo includes a `.pre-commit-config.yaml`. To enable:

```bash
pre-commit install
```

Hooks run `terraform fmt`, `terraform validate`, `tflint`, and doc generation on each commit.

## Using as a Module

To test consumption locally, create a `examples/` directory with a root module that references this module via a relative path:

```hcl
module "endpoint" {
  source = "../"

  endpoint_name = "my-test-endpoint"
  containers = [{
    image_uri = "123456789012.dkr.ecr.us-east-1.amazonaws.com/my-model:latest"
  }]
}
```

Then run `terraform init && terraform plan` from the example directory.
