# Examples

## Setup

vi Dockerfile
```
FROM alpine:3.15.3

WORKDIR /usr/src/app/

ENV AWS_REGION=eu-central-1

RUN apk --update add --no-cache --virtual run-dependencies \
        terraform \
        aws-cli \
        curl


RUN curl -L https://github.com/cycloidio/terracognita/releases/latest/download/terracognita-linux-amd64.tar.gz -o terracognita-linux-amd64.tar.gz \
        && tar -xf terracognita-linux-amd64.tar.gz \
        && rm terracognita-linux-amd64.tar.gz \
        && chmod u+x terracognita-linux-amd64 \
        && mv terracognita-linux-amd64 /usr/local/bin/terracognita
```

Build Docker image
```
docker build -t terracognita:0.1 .
```



Run in Docker
```
docker run -it --rm terracognita:0.1 sh
```

## Usage

Get help
```
terracognita

Reads from Providers and generates a Terraform configuration, all the flags can be used also with ENV (ex: --aws-access-key == AWS_ACCESS_KEY)

Usage:
  terracognita [command]

Available Commands:
  aws         Terracognita reads from AWS and generates hcl resources and/or terraform state
  azurerm     Terracognita reads from Azure and generates hcl resources and/or terraform state
  google      Terracognita reads from GCP and generates hcl resources and/or terraform state
  help        Help about any command
  version     Prints the current build version

Flags:
  -d, --debug                     Activate the debug mode wich includes TF logs via TF_LOG=TRACE|DEBUG|INFO|WARN|ERROR configuration https://www.terraform.io/docs/internals/debugging.html
  -e, --exclude strings           List of resources to not import, this names are the ones on TF (ex: aws_instance). If not set then means that none the resources will be excluded
      --hcl string                HCL output file or directory. If it's a directory it'll be emptied before importing
  -h, --help                      help for terracognita
  -i, --include strings           List of resources to import, this names are the ones on TF (ex: aws_instance). If not set then means that all the resources will be imported
      --interpolate               Activate the interpolation for the HCL and the dependencies building for the State file (default true)
      --log-file string           Write the logs with -v to this destination (default "/root/.cache/terracognita/terracognita.log")
      --module string             Generates the output in module format into the directory specified. With this flag (--module) the --hcl is ignored and will be generated inside of the module
      --module-variables string   Path to a file containing the list of attributes to use as variables when building the module. The format is a JSON/YAML, more information on https://github.com/cycloidio/terracognita#modules
      --target strings            List of resources to import via ID, those IDs are the ones documented on Terraform that are needed to Import. The format is 'aws_instance.ID'
      --tfstate string            TFState output file
  -v, --verbose                   Activate the verbose mode

Use "terracognita [command] --help" for more information about a command.
```


List all possible ressources for AWS, GCP or Azure
```
terracognita aws resources

aws_instance
aws_alb
aws_alb_listener
aws_alb_listener_certificate
aws_alb_listener_rule
aws_alb_target_group
aws_alb_target_group_attachment
aws_api_gateway_deployment
aws_api_gateway_resource
aws_api_gateway_rest_api
aws_api_gateway_stage
aws_athena_workgroup
aws_autoscaling_group
aws_autoscaling_policy
aws_autoscaling_schedule
aws_batch_job_definition
aws_cloudfront_distribution
aws_cloudfront_origin_access_identity
aws_cloudfront_public_key
aws_cloudwatch_metric_alarm
aws_dax_cluster
aws_db_instance
aws_db_parameter_group
aws_db_subnet_group
aws_directory_service_directory
aws_dms_replication_instance
aws_dx_gateway
aws_dynamodb_global_table
aws_dynamodb_table
aws_ebs_volume
aws_ecs_cluster
aws_ecs_service
aws_efs_file_system
aws_eip
aws_eks_cluster
aws_elasticache_cluster
aws_elasticache_replication_group
aws_elastic_beanstalk_application
aws_elasticsearch_domain
aws_elasticsearch_domain_policy
aws_elb
aws_emr_cluster
aws_fsx_lustre_file_system
aws_glue_catalog_database
aws_glue_catalog_table
aws_iam_access_key
aws_iam_account_alias
aws_iam_account_password_policy
aws_iam_group
aws_iam_group_membership
aws_iam_group_policy
aws_iam_group_policy_attachment
aws_iam_instance_profile
aws_iam_openid_connect_provider
aws_iam_policy
aws_iam_role
aws_iam_role_policy
aws_iam_role_policy_attachment
aws_iam_saml_provider
aws_iam_server_certificate
aws_iam_user
aws_iam_user_group_membership
aws_iam_user_policy
aws_iam_user_policy_attachment
aws_iam_user_ssh_key
aws_internet_gateway
aws_key_pair
aws_kinesis_stream
aws_lambda_function
aws_launch_configuration
aws_launch_template
aws_lb
aws_lb_cookie_stickiness_policy
aws_lb_listener
aws_lb_listener_certificate
aws_lb_listener_rule
aws_lb_target_group
aws_lb_target_group_attachment
aws_lightsail_instance
aws_media_store_container
aws_mq_broker
aws_nat_gateway
aws_neptune_cluster
aws_rds_cluster
aws_rds_global_cluster
aws_redshift_cluster
aws_route53_delegation_set
aws_route53_health_check
aws_route53_query_log
aws_route53_record
aws_route53_resolver_endpoint
aws_route53_resolver_rule_association
aws_route53_zone
aws_route53_zone_association
aws_s3_bucket
aws_security_group
aws_ses_active_receipt_rule_set
aws_ses_configuration_set
aws_ses_domain_dkim
aws_ses_domain_identity
aws_ses_domain_mail_from
aws_ses_identity_notification_topic
aws_ses_receipt_filter
aws_ses_receipt_rule
aws_ses_receipt_rule_set
aws_ses_template
aws_sqs_queue
aws_storagegateway_gateway
aws_subnet
aws_volume_attachment
aws_vpc
aws_vpc_peering_connection
aws_vpn_gateway
```


Configure AWS credentials
```
export AWS_ACCESS_KEY_ID="XXXXX"
export AWS_SECRET_ACCESS_KEY="XXXXX"
export AWS_SESSION_TOKEN="XXXXX"
```



Create Terraform and State file only for: aws_iam_user
```
terracognita aws --tfstate resources.tfstate --hcl resources.tf --aws-default-region eu-central-1 --include aws_iam_user
```

Creates 2 files
- resources.tf
- resources.tfstate


vi resources.tf
```
provider "aws" {
  region = var.region
}


terraform {
  required_providers {
    aws = {
      source = "hashicorp/aws"
    }

  }

  required_version = ">= 1.0"
}

variable "region" {
  default = "eu-central-1"
}

resource "aws_ses_domain_dkim" "demo_test_example_de" {
  domain = "demo.test@example.de"
}

```


Create Terraform code and state for all ressources
```
terracognita aws --tfstate resources.tfstate --hcl resources.tf --aws-default-region eu-central-1 
```


