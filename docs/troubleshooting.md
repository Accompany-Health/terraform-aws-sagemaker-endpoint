# Troubleshooting

## Common Issues

### Endpoint stuck in "Creating" or "Failed" state

**Symptoms**: `terraform apply` hangs or eventually errors with endpoint creation failure.

**Causes**:
- Container image does not exist or is inaccessible from the execution role
- Model artifacts in S3 are missing or the role lacks `s3:GetObject` permission
- Instance type is unavailable in the target region
- Container fails health check (ping endpoint not responding on port 8080)

**Resolution**:
1. Check CloudWatch logs: `/aws/sagemaker/Endpoints/<endpoint-name>`
2. Verify the container image URI is correct and the ECR repo policy allows SageMaker access
3. Confirm model artifact S3 path exists and the execution role can read it
4. Try a different instance type if seeing capacity errors

### Name collision on apply

**Symptoms**: `EntityAlreadyExists` or naming conflict errors.

**Cause**: A previous `terraform destroy` did not fully clean up, or another deployment uses the same `name_prefix`.

**Resolution**: Use a unique `name_prefix` per deployment, or manually delete the orphaned SageMaker resources in the console before re-applying.

### IAM role creation fails

**Symptoms**: Error about role already existing or insufficient permissions to create roles.

**Cause**: If `sg_role_arn` is null, the module creates an IAM role. If a role with the same prefix already exists, or the caller lacks `iam:CreateRole`, this fails.

**Resolution**: Either pass an existing role via `sg_role_arn`, or ensure the Terraform execution identity has IAM admin permissions.

### Autoscaling not triggering

**Symptoms**: Endpoint stays at `initial_instance_count` despite high invocation volume.

**Causes**:
- `autoscaling_config` not set (resources not created)
- `target_value` set too high
- Cooldown periods too long

**Resolution**:
1. Confirm autoscaling resources exist: `terraform state list | grep autoscaling`
2. Check CloudWatch metric `SageMakerVariantInvocationsPerInstance` to see actual values
3. Adjust `target_value` and cooldown periods

### KMS encryption errors

**Symptoms**: `AccessDeniedException` related to KMS during endpoint creation.

**Cause**: The execution role does not have `kms:Decrypt` and `kms:GenerateDataKey` on the specified KMS key.

**Resolution**: Add KMS permissions to the execution role, or use a KMS key policy that grants access to SageMaker.
