# Glossary

| Term | Definition |
|------|-----------|
| Endpoint | A hosted HTTPS URL that serves real-time predictions from a deployed model |
| Endpoint Configuration | Specifies the model, instance type, and variant settings for an endpoint |
| Execution Role | IAM role assumed by SageMaker to pull container images, read model artifacts, and write logs |
| Inference Pipeline | A SageMaker model composed of 2-15 containers chained in sequence (e.g., preprocessing + prediction + postprocessing) |
| Model | A SageMaker resource that references a container image and optionally model artifacts in S3 |
| Model Artifacts | Serialized model weights/files stored in S3, loaded into the container at startup |
| Network Isolation | A mode where the model container has no inbound or outbound network access |
| Production Variant | A specific model deployment configuration within an endpoint (instance type, count, traffic weight) |
| Target Tracking | An autoscaling policy type that adjusts capacity to maintain a target metric value |
| Variant Weight | The fraction of inference traffic routed to a given production variant (used for A/B testing) |
