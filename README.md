# github-action-deploy-rds
Deploys an AWS RDS Database instance


#### **ECR Inputs**
| Name             | Type    | Description                        |
|------------------|---------|------------------------------------|
| `aws_ecr_repo_create` | Boolean | Determines whether a repository will be created.' |
| `aws_ecr_repo_type` | String | The type of repository to create. Either `public` or `private`. Defaults to `private`.' |
| `aws_ecr_repo_name` | String | The name of the repository. If none, will use the default resource-identifier.' |
| `aws_ecr_repo_mutable` | Boolean | The tag mutability setting for the repository. Set this to true if `MUTABLE`. Defaults to false, so `IMMUTABLE`' |
| `aws_ecr_repo_encryption_type` | String | The encryption type for the repository. Must be one of: `KMS` or `AES256`. Defaults to `AES256`' |
| `aws_ecr_repo_encryption_key_arn` | String | The ARN of the KMS key to use when encryption_type is `KMS`. If not specified, uses the default AWS managed key for ECR. |
| `aws_ecr_repo_force_destroy` | Boolean | If `true`, will delete the repository even if it contains images. Defaults to `false`' |
| `aws_ecr_repo_image_scan` | Boolean | Indicates whether images are scanned after being pushed to the repository (`true`) (default) or not scanned (`false`)' |
| `aws_ecr_registry_scan_rule` | String | One or multiple blocks specifying scanning rules to determine which repository filters are used and at what frequency. Defaults to `[]`.  |
| `aws_ecr_registry_pull_through_cache_rules` | String | List of pull through cache rules to create. Use map(map(string)). ' |
| `aws_ecr_registry_scan_config` | String | Scanning type to set for the registry. Can be either `ENHANCED` or `BASIC`. Defaults to null.' |
| `aws_ecr_registry_replication_rules_input` | String | The replication rules for a replication configuration. A maximum of 10 are allowed. Defaults to `[]`.' |
| `aws_ecr_repo_policy_attach` | Boolean | Determines whether a repository policy will be attached to the repository. Defaults to `true`.' |
| `aws_ecr_repo_policy_create` | Boolean | Determines whether a repository policy will be created. Defaults to `true`.' |
| `aws_ecr_repo_policy_input` | String | The JSON policy to apply to the repository. If defined overrides the default policy' |
| `aws_ecr_repo_read_arn` | String | The ARNs of the IAM users/roles that have read access to the repository. (Comma separated list)' |
| `aws_ecr_repo_write_arn` | String | The ARNs of the IAM users/roles that have read/write access to the repository. (Comma separated list)' |
| `aws_ecr_repo_read_arn_lambda` | String | The ARNs of the Lambda service roles that have read access to the repository. (Comma separated list)' |
| `aws_ecr_lifecycle_policy_input` | String | The policy document. This is a JSON formatted string. See more details about [Policy Parameters](http://docs.aws.amazon.com/AmazonECR/latest/userguide/LifecyclePolicies.html#lifecycle_policy_parameters) in the official AWS docs' |
| `aws_ecr_public_repo_catalog` | String | Catalog data configuration for the repository. Defaults to `{}`.' |
| `aws_ecr_registry_policy_input` | String | The policy document. This is a JSON formatted string' |
| `aws_ecr_additional_tags ` | JSON | Add additional tags to the terraform [default tags](https://www.hashicorp.com/blog/default-tags-in-the-terraform-aws-provider), any tags put here will be added to aurora provisioned resources.|