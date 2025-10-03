# Deploy AWS RDS Database 

`bitovi/github-actions-deploy-rds` builds and deploys an AWS RDS Database, with the option for a proxy.

This action uses our n| `aws_rds_db_performance_insights_retention` | String | Number of days to retain performance insights data. Defaults to `7`. |
| `aws_rds_db_performance_insights_kms_key_id` | String | KMS key ID to use for encrypting performance insights data. | GitHub Actions Commons repository, a library that contains multiple Terraform modules, allowing us to condense all of our tools in one repo, hence continuous improvements are made to it. 
![alt](https://bitovi-gha-pixel-tracker-deployment-main.bitovi-sandbox.com/pixel/bGWmpApZe-xqJr6P-CmOJ)
## Action Summary
This action creates an RDS Database, with the option to add even a proxy. Could be a Postgres, MySQL, MariaDB or MSSQL. 

If you would like to deploy a backend app/service, check out our other actions:
| Action | Purpose |
| ------ | ------- |
| [Deploy Docker to EC2](https://github.com/marketplace/actions/deploy-docker-to-aws-ec2) | Deploys a repo with a Dockerized application to a virtual machine (EC2) on AWS |
| [Deploy React to GitHub Pages](https://github.com/marketplace/actions/deploy-react-to-github-pages) | Builds and deploys a React application to GitHub Pages. |
| [Deploy static site to AWS (S3/CDN/R53)](https://github.com/marketplace/actions/deploy-static-site-to-aws-s3-cdn-r53) | Hosts a static site in AWS S3 with CloudFront |
<br/>

**And more!**, check our [list of actions in the GitHub marketplace](https://github.com/marketplace?category=&type=actions&verification=&query=bitovi)

# Need help or have questions?
This project is supported by [Bitovi, A DevOps consultancy](https://www.bitovi.com/services/devops-consulting).

You can **get help or ask questions** on our:

- [Discord Community](https://discord.gg/zAHn4JBVcX)

Or, you can hire us for training, consulting, or development. [Set up a free consultation](https://www.bitovi.com/services/devops-consulting).

# Basic Use

For basic usage, create `.github/workflows/deploy.yaml` with the following to build on push.
```yaml
on:
  push:
    branches:
      - "main" # change to the branch you wish to deploy from

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
    - id: deploy-rds
      uses: bitovi/github-actions-deploy-rds@v0
      with:
        aws_access_key_id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws_secret_access_key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws_default_region: us-east-1

        aws_rds_db_enable: true
```

# Advanced use
```yaml
on:
  push:
    branches:
      - "main" # change to the branch you wish to deploy from

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - id: deploy
        uses: bitovi/github-actions-deploy-rds@v0
        with:
          aws_access_key_id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws_secret_access_key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws_default_region: us-east-1

          aws_additional_tags: '{\"some\":\"extra\",\"tag\":\"added\"}'

          tf_state_bucket_destroy: true

          aws_rds_db_enable: true
          aws_rds_db_proxy: true
          aws_rds_db_name: some-db-name
          aws_rds_db_user: myrdsuser
          aws_rds_db_ingress_allow_all: true
          aws_rds_db_subnets: subnet-0000000000000,subnet-0000000000000
          aws_rds_db_allocated_storage: 10
          aws_rds_db_max_allocated_storage: 20
          aws_rds_db_instance_class: db.t3.micro
          aws_vpc_id: vpc-0000000000000
          aws_resource_identifier: replaced-this-from
          tf_state_bucket: bitovi-resources
          tf_state_file_name_append: rds-dev-db
```

### Inputs
1. [AWS Specific](#aws-specific)
1. [Action default inputs](#action-default-inputs)
1. [RDS Inputs](#rds-inputs)
1. [DB Proxy Inputs](#db-proxy-inputs)
1. [VPC Inputs](#vpc-inputs)

### Outputs
1. [Action Outputs](#action-outputs)

The following inputs can be used as `step.with` keys
<br/>

#### **AWS Specific**
| Name             | Type    | Description                        |
|------------------|---------|------------------------------------|
| `aws_access_key_id` | String | AWS access key ID |
| `aws_secret_access_key` | String | AWS secret access key |
| `aws_session_token` | String | AWS session token |
| `aws_default_region` | String | AWS default region. Defaults to `us-east-1` |
| `aws_resource_identifier` | String | Set to override the AWS resource identifier for the deployment. Defaults to `${GITHUB_ORG_NAME}-${GITHUB_REPO_NAME}-${GITHUB_BRANCH_NAME}`. |
| `aws_additional_tags` | JSON | Add additional tags to the terraform [default tags](https://www.hashicorp.com/blog/default-tags-in-the-terraform-aws-provider), any tags put here will be added to all provisioned resources.|
<br/>

#### **Action default inputs**
| Name             | Type    | Description                        |
|------------------|---------|------------------------------------|
| `tf_stack_destroy` | Boolean  | Set to `true` to destroy the stack. |
| `tf_state_file_name` | String | Change this to be anything you want to. Careful to be consistent here. A missing file could trigger recreation, or stepping over destruction of non-defined objects. Defaults to `tf-state-aws`. |
| `tf_state_file_name_append` | String | Appends a string to the tf-state-file. Setting this to `unique` will generate `tf-state-aws-unique`. (Can co-exist with `tf_state_file_name`) |
| `tf_state_bucket` | String | AWS S3 bucket name to use for Terraform state. See [note](#s3-buckets-naming) | 
| `tf_state_bucket_destroy` | Boolean | Force purge and deletion of S3 bucket defined. Any file contained there will be destroyed. `tf_stack_destroy` must also be `true`. Default is `false`. |
| `bitops_code_only` | Boolean | If `true`, will run only the generation phase of BitOps, where the Terraform and Ansible code is built. |
| `bitops_code_store` | Boolean | Store BitOps generated code as a GitHub artifact. |
<br/>


#### **RDS Inputs**
| Name             | Type    | Description                        |
|------------------|---------|------------------------------------|
| `aws_rds_db_enable`| Boolean | Toggles the creation of a RDS DB. Defaults to `true`. |
| `aws_rds_db_proxy`| Boolean | Set to `true` to add a RDS DB Proxy. |
| `aws_rds_db_identifier`| String | Database identifier that will appear in the AWS Console. Defaults to `aws_resource_identifier` if none set. |
| `aws_rds_db_name`| String | The name of the database to create when the DB instance is created. If this parameter is not specified, no database is created in the DB instance. |
| `aws_rds_db_user`| String | Username for the database. Defaults to `dbuser`. |
| `aws_rds_db_engine`| String | Which Database engine to use. Defaults to `postgres`. |
| `aws_rds_db_engine_version`| String | Which Database engine version to use. Will use latest if none defined. |
| `aws_rds_db_ca_cert_identifier`| String | Defines the certificate to use with the instance. Defaults to `rds-ca-ecc384-g1`.|
| `aws_rds_db_security_group_name`| String | The name of the database security group. Defaults to `SG for ${aws_resource_identifier} - RDS`. |
| `aws_rds_db_allowed_security_groups` | String | Comma separated list of security groups to add to the DB Security Group. (Allowing incoming traffic.) | 
| `aws_rds_db_ingress_allow_all` | Boolean | Allow incoming traffic from 0.0.0.0/0. Defaults to `true`. |
| `aws_rds_db_publicly_accessible` | Boolean | Allow the database to be publicly accessible from the internet. Defaults to `false`. |
| `aws_rds_db_port`| String | Port where the DB listens to. |
| `aws_rds_db_subnets`| String | Specify which subnets to use as a list of strings.  Example: `i-1234,i-5678,i-9101`. |
| `aws_rds_db_allocated_storage`| String | Storage size. Defaults to `10`. |
| `aws_rds_db_max_allocated_storage`| String | Max storage size. Defaults to `0` to disable auto-scaling. |
| `aws_rds_db_storage_encrypted` | Boolean | Toggle storage encryption. Defaults to false. |
| `aws_rds_db_storage_type` | String | Storage type. Like gp2 / gp3. Defaults to gp2. |
| `aws_rds_db_kms_key_id` | String | The ARN for the KMS encryption key. |
| `aws_rds_db_instance_class`| String | DB instance server type. Defaults to `db.t3.micro`. See [this list](https://aws.amazon.com/rds/instance-types/). |
| `aws_rds_db_final_snapshot` | String | If final snapshot is wanted, add a snapshot name. Leave empty if not. |
| `aws_rds_db_restore_snapshot_identifier` | String | Name of the snapshot to restore the database from. |
| `aws_rds_db_cloudwatch_logs_exports`| String | Set of log types to enable for exporting to CloudWatch logs. Defaults to `postgresql`. Options are MySQL and MariaDB: `audit,error,general,slowquery`. PostgreSQL: `postgresql,upgrade`. MSSQL: `agent,error`. Oracle: `alert,audit,listener,trace`. |
| `aws_rds_db_multi_az` | Boolean| Specifies if the RDS instance is multi-AZ. Defaults to `false`. |
| `aws_rds_db_maintenance_window` | String | The window to perform maintenance in. Eg: `Mon:00:00-Mon:03:00` |
| `aws_rds_db_apply_immediately` | Boolean | Specifies whether any database modifications are applied immediately, or during the next maintenance window. Defaults to `false`.|
| `aws_rds_db_performance_insights_enable` | Boolean | Enables performance insights for the database. Defaults to `false`. |
| `aws_rds_db_performance_insights_retention` | String | KMS key ID to use for encrypting performance insights data. |
| `aws_rds_db_performance_insights_kms_key_id` | String | Number of days to retain performance insights data. Defaults to `7`. |
| `aws_rds_db_monitoring_interval` | String | The interval, in seconds, between points when metrics are collected. Defaults to `0` (disabled). Valid values are `0,1,5,10,15,30,60`. |
| `aws_rds_db_monitoring_role_arn` | String | The ARN of the IAM role that provides access to the Enhanced Monitoring metrics. If empty will use the standard `rds-monitoring-role`. |
| `aws_rds_db_insights_mode` | String | The mode for Performance Insights. Could be `standard` (default) or `advanced`. |
| `aws_rds_db_allow_major_version_upgrade` | Boolean | Indicates that major version upgrades are allowed. Defaults to `false`. |
| `aws_rds_db_auto_minor_version_upgrade` | Boolean | Indicates that minor version upgrades are allowed. Defaults to `true`. |
| `aws_rds_db_backup_retention_period` | String | The number of days to retain backups for. Must be between 0 (disabled) and 35. Defaults to `0`. |
| `aws_rds_db_backup_window` | String | The window during which backups are taken. Eg: `"09:46-10:16"`. |
| `aws_rds_db_copy_tags_to_snapshot` | Boolean | Indicates whether to copy tags to snapshots. Defaults to `false`. |
| `aws_rds_db_additional_tags` | JSON | Add additional tags to the terraform [default tags](https://www.hashicorp.com/blog/default-tags-in-the-terraform-aws-provider), any tags put here will be added to RDS provisioned resources.|
<br/>


#### **DB Proxy Inputs**
| Name             | Type    | Description                        |
|------------------|---------|------------------------------------|
| `aws_db_proxy_name` | String | Name of the database proxy.  Defaults to `aws_resource_identifier` |
| `aws_db_proxy_client_password_auth_type` | String | Overrides auth type. Using `MYSQL_NATIVE_PASSWORD`, `POSTGRES_SCRAM_SHA_256`, and `SQL_SERVER_AUTHENTICATION` depending on the database family. |
| `aws_db_proxy_tls` | Boolean | Make TLS a requirement for connections. Defaults to `true`.|
| `aws_db_proxy_security_group_name` | String | Name for the proxy security group. Defaults to `aws_resource_identifier`. |
| `aws_db_proxy_database_security_group_allow` | Boolean | If true, will add an incoming rule from every security group associated with the DB. |
| `aws_db_proxy_allowed_security_group` | String | Comma separated list for extra allowed security groups.|
| `aws_db_proxy_allow_all_incoming` | Boolean | Allow all incoming traffic to the DB Proxy (0.0.0.0/0 rule). Keep in mind that the proxy is only available from the internal network except manually exposed. | 
| `aws_db_proxy_cloudwatch_enable` | Boolean | Toggle Cloudwatch logs. Will be stored in `/aws/rds/proxy/rds_proxy.name`. |
| `aws_db_proxy_cloudwatch_retention_days` | String | Number of days to retain cloudwatch logs. Defaults to `14`. |
| `aws_db_proxy_additional_tags` | JSON | Add additional tags to the terraform [default tags](https://www.hashicorp.com/blog/default-tags-in-the-terraform-aws-provider), any tags put here will be added to created resources. |
<br/>

#### **VPC Inputs**
| Name             | Type    | Description                        |
|------------------|---------|------------------------------------|
| `aws_vpc_create` | Boolean | Define if a VPC should be created. Defaults to `false`. |
| `aws_vpc_name` | String | Define a name for the VPC. Defaults to `VPC for ${aws_resource_identifier}`. |
| `aws_vpc_cidr_block` | String | Define Base CIDR block which is divided into subnet CIDR blocks. Defaults to `10.0.0.0/16`. |
| `aws_vpc_public_subnets` | String | Comma separated list of public subnets. Defaults to `10.10.110.0/24`|
| `aws_vpc_private_subnets` | String | Comma separated list of private subnets. If no input, no private subnet will be created. Defaults to `<none>`. |
| `aws_vpc_availability_zones` | String | Comma separated list of availability zones. Defaults to `aws_default_region+<random>` value. If a list is defined, the first zone will be the one used for the EC2 instance. |
| `aws_vpc_id` | String | **Existing** AWS VPC ID to use. Accepts `vpc-###` values. |
| `aws_vpc_subnet_id` | String | **Existing** AWS VPC Subnet ID. If none provided, will pick one. (Ideal when there's only one). |
| `aws_vpc_enable_nat_gateway` | Boolean | Adds a NAT gateway for each public subnet. Defaults to `false`. |
| `aws_vpc_single_nat_gateway` | Boolean | Toggles only one NAT gateway for all of the public subnets. Defaults to `false`. |
| `aws_vpc_external_nat_ip_ids` | String | **Existing** comma separated list of IP IDs if reusing. (ElasticIPs). |
| `aws_vpc_additional_tags` | JSON | Add additional tags to the terraform [default tags](https://www.hashicorp.com/blog/default-tags-in-the-terraform-aws-provider), any tags put here will be added to vpc provisioned resources.|
<br/>

#### **Action Outputs**
| Name             | Description                        |
|------------------|------------------------------------|
| `aws_vpc_id` | The selected VPC ID used. |
| `db_endpoint`| RDS Endpoint. |
| `db_secret_details_name`| AWS Secret name containing db credentials. |
| `db_sg_id`| SG ID for the RDS instance. |
| `db_proxy_rds_endpoint`| Database proxy endpoint. |
| `db_proxy_secret_name_rds`| AWS Secret name containing proxy credentials. |
| `db_proxy_sg_id_rds`| SG ID for the RDS Proxy instance. |
<hr/>
<br/>

## Note about resource identifiers

Most resources will contain the tag `${GITHUB_ORG_NAME}-${GITHUB_REPO_NAME}-${GITHUB_BRANCH_NAME}`, some of them, even the resource name after. 
We limit this to a 60 characters string because some AWS resources have a length limit and short it if needed.

We use the kubernetes style for this. For example, kubernetes -> k(# of characters)s -> k8s. And so you might see some compressions are made.

For some specific resources, we have a 32 characters limit. If the identifier length exceeds this number after compression, we remove the middle part and replace it for a hash made up from the string itself. 

### S3 buckets naming

Buckets names can be made of up to 63 characters. If the length allows us to add -tf-state, we will do so. If not, a simple -tf will be added.

## Contributing
We would love for you to contribute to [`bitovi/github-actions-deploy-rds`](https://github.com/bitovi/github-actions-deploy-rds).   [Issues](https://github.com/bitovi/github-actions-deploy-rds/issues) and [Pull Requests](https://github.com/bitovi/github-actions-deploy-rds/pulls) are welcome!

## License
The scripts and documentation in this project are released under the [MIT License](https://github.com/bitovi/github-actions-deploy-rds/blob/main/LICENSE).

# Provided by Bitovi
[Bitovi](https://www.bitovi.com/) is a proud supporter of Open Source software.

# We want to hear from you.
Come chat with us about open source in our Bitovi community [Discord](https://discord.gg/zAHn4JBVcX)!
