
<!-- markdownlint-disable -->
# terraform-aws-rds
<!-- markdownlint-restore -->


<!--
-->

Terraform module to provision AWS [`RDS`](https://aws.amazon.com/rds/) instances

---

## Introduction

The module will create:

* DB instance (MySQL, Postgres, SQL Server, Oracle)
* DB Option Group (will create a new one or you may use an existing)
* DB Parameter Group
* DB Subnet Group
* DB Security Group
* DNS Record in Route53 for the DB endpoint

## Usage


**IMPORTANT:** We do not pin modules to versions in our examples because of the
difficulty of keeping the versions in the documentation in sync with the latest released versions.
We highly recommend that in your code you pin the version to the exact version you are
using so that your infrastructure remains stable, and update versions in a
systematic way so that they do not catch you by surprise.

Also, because of a bug in the Terraform registry ([hashicorp/terraform#21417](https://github.com/hashicorp/terraform/issues/21417)),
the registry shows many of our inputs as required when in fact they are optional.
The table below correctly indicates which inputs are required.


```hcl
module "rds_instance" {
    source = "cloudposse/rds/aws"
    namespace                   = "eg"
    stage                       = "prod"
    name                        = "app"
    dns_zone_id                 = "Z89FN1IW975KPE"
    host_name                   = "db"
    security_group_ids          = ["sg-xxxxxxxx"]
    ca_cert_identifier          = "rds-ca-2019"
    allowed_cidr_blocks         = ["XXX.XXX.XXX.XXX/32"]
    database_name               = "wordpress"
    database_user               = "admin"
    database_password           = "xxxxxxxxxxxx"
    database_port               = 3306
    multi_az                    = true
    storage_type                = "gp2"
    allocated_storage           = 100
    storage_encrypted           = true
    engine                      = "mysql"
    engine_version              = "5.7.17"
    major_engine_version        = "5.7"
    instance_class              = "db.t2.medium"
    db_parameter_group          = "mysql5.7"
    option_group_name           = "mysql-options"
    publicly_accessible         = false
    subnet_ids                  = ["sb-xxxxxxxxx", "sb-xxxxxxxxx"]
    vpc_id                      = "vpc-xxxxxxxx"
    snapshot_identifier         = "rds:production-2015-06-26-06-05"
    auto_minor_version_upgrade  = true
    allow_major_version_upgrade = false
    apply_immediately           = false
    maintenance_window          = "Mon:03:00-Mon:04:00"
    skip_final_snapshot         = false
    copy_tags_to_snapshot       = true
    backup_retention_period     = 7
    backup_window               = "22:00-03:00"

    db_parameter = [
      { name  = "myisam_sort_buffer_size"   value = "1048576" },
      { name  = "sort_buffer_size"          value = "2097152" }
    ]

    db_options = [
      { option_name = "MARIADB_AUDIT_PLUGIN"
          option_settings = [
            { name = "SERVER_AUDIT_EVENTS"           value = "CONNECT" },
            { name = "SERVER_AUDIT_FILE_ROTATIONS"   value = "37" }
          ]
      }
    ]
}
```
### Character Sets

If you wish to create the database in a specific character set you can use one of the following options depending
on your database engine of choice.

For Oracle and Microsoft SQL you can specify charset name as an input variable
to this module. For example, for Microsoft SQL, you could use:
```hcl
module "rds_instance" {
  ...
  charset_name                   = "Korean_Wansung_CI_AS"
  ...
}
```

For `mysql` and `mariadb` engines character set of the database can be defined via `db_parameter`. In this example
the database is created with `utf8mb4` (character set) and utf8mb4_unicode_ci (collation):

```hcl
module "rds_instance" {
  ...
  db_parameter = [
    {
      name = "character_set_server"
      value = "utf8mb4"
      apply_method = "immediate"
    },
    {
      name = "collation_server"
      value = "utf8mb4_unicode_ci"
      apply_method = "immediate"
    }
  ]
  ...
}
```






<!-- markdownlint-disable -->

<!-- markdownlint-restore -->
<!-- markdownlint-disable -->
## Requirements

| Name | Version |
|------|---------|
| <a name="requirement_terraform"></a> [terraform](#requirement\_terraform) | >= 0.13.0 |
| <a name="requirement_aws"></a> [aws](#requirement\_aws) | >= 2.0 |
| <a name="requirement_null"></a> [null](#requirement\_null) | >= 2.0 |
| <a name="requirement_template"></a> [template](#requirement\_template) | >= 2.0 |

## Providers

| Name | Version |
|------|---------|
| <a name="provider_aws"></a> [aws](#provider\_aws) | >= 2.0 |

## Modules

| Name | Source | Version |
|------|--------|---------|
| <a name="module_dns_host_name"></a> [dns\_host\_name](#module\_dns\_host\_name) | cloudposse/route53-cluster-hostname/aws | 0.12.0 |
| <a name="module_final_snapshot_label"></a> [final\_snapshot\_label](#module\_final\_snapshot\_label) | cloudposse/label/null | 0.24.1 |
| <a name="module_this"></a> [this](#module\_this) | cloudposse/label/null | 0.25.0 |

## Resources

| Name | Type |
|------|------|
| [aws_db_instance.default](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/db_instance) | resource |
| [aws_db_option_group.default](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/db_option_group) | resource |
| [aws_db_parameter_group.default](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/db_parameter_group) | resource |
| [aws_db_subnet_group.default](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/db_subnet_group) | resource |
| [aws_security_group.default](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/security_group) | resource |
| [aws_security_group_rule.egress](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/security_group_rule) | resource |
| [aws_security_group_rule.ingress_cidr_blocks](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/security_group_rule) | resource |
| [aws_security_group_rule.ingress_security_groups](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/security_group_rule) | resource |

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| <a name="input_additional_tag_map"></a> [additional\_tag\_map](#input\_additional\_tag\_map) | Additional key-value pairs to add to each map in `tags_as_list_of_maps`. Not added to `tags` or `id`.<br>This is for some rare cases where resources want additional configuration of tags<br>and therefore take a list of maps with tag key, value, and additional configuration. | `map(string)` | `{}` | no |
| <a name="input_allocated_storage"></a> [allocated\_storage](#input\_allocated\_storage) | The allocated storage in GBs | `number` | `null` | no |
| <a name="input_allow_major_version_upgrade"></a> [allow\_major\_version\_upgrade](#input\_allow\_major\_version\_upgrade) | Allow major version upgrade | `bool` | `false` | no |
| <a name="input_allowed_cidr_blocks"></a> [allowed\_cidr\_blocks](#input\_allowed\_cidr\_blocks) | The whitelisted CIDRs which to allow `ingress` traffic to the DB instance | `list(string)` | `[]` | no |
| <a name="input_apply_immediately"></a> [apply\_immediately](#input\_apply\_immediately) | Specifies whether any database modifications are applied immediately, or during the next maintenance window | `bool` | `false` | no |
| <a name="input_associate_security_group_ids"></a> [associate\_security\_group\_ids](#input\_associate\_security\_group\_ids) | The IDs of the existing security groups to associate with the DB instance | `list(string)` | `[]` | no |
| <a name="input_attributes"></a> [attributes](#input\_attributes) | ID element. Additional attributes (e.g. `workers` or `cluster`) to add to `id`,<br>in the order they appear in the list. New attributes are appended to the<br>end of the list. The elements of the list are joined by the `delimiter`<br>and treated as a single ID element. | `list(string)` | `[]` | no |
| <a name="input_auto_minor_version_upgrade"></a> [auto\_minor\_version\_upgrade](#input\_auto\_minor\_version\_upgrade) | Allow automated minor version upgrade (e.g. from Postgres 9.5.3 to Postgres 9.5.4) | `bool` | `true` | no |
| <a name="input_availability_zone"></a> [availability\_zone](#input\_availability\_zone) | The AZ for the RDS instance. Specify one of `subnet_ids`, `db_subnet_group_name` or `availability_zone`. If `availability_zone` is provided, the instance will be placed into the default VPC or EC2 Classic | `string` | `null` | no |
| <a name="input_backup_retention_period"></a> [backup\_retention\_period](#input\_backup\_retention\_period) | Backup retention period in days. Must be > 0 to enable backups | `number` | `0` | no |
| <a name="input_backup_window"></a> [backup\_window](#input\_backup\_window) | When AWS can perform DB snapshots, can't overlap with maintenance window | `string` | `"22:00-03:00"` | no |
| <a name="input_ca_cert_identifier"></a> [ca\_cert\_identifier](#input\_ca\_cert\_identifier) | The identifier of the CA certificate for the DB instance | `string` | `null` | no |
| <a name="input_charset_name"></a> [charset\_name](#input\_charset\_name) | The character set name to use for DB encoding. [Oracle & Microsoft SQL only](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/db_instance#character_set_name). For other engines use `db_parameter` | `string` | `null` | no |
| <a name="input_context"></a> [context](#input\_context) | Single object for setting entire context at once.<br>See description of individual variables for details.<br>Leave string and numeric variables as `null` to use default value.<br>Individual variable settings (non-null) override settings in context object,<br>except for attributes, tags, and additional\_tag\_map, which are merged. | `any` | <pre>{<br>  "additional_tag_map": {},<br>  "attributes": [],<br>  "delimiter": null,<br>  "descriptor_formats": {},<br>  "enabled": true,<br>  "environment": null,<br>  "id_length_limit": null,<br>  "label_key_case": null,<br>  "label_order": [],<br>  "label_value_case": null,<br>  "labels_as_tags": [<br>    "unset"<br>  ],<br>  "name": null,<br>  "namespace": null,<br>  "regex_replace_chars": null,<br>  "stage": null,<br>  "tags": {},<br>  "tenant": null<br>}</pre> | no |
| <a name="input_copy_tags_to_snapshot"></a> [copy\_tags\_to\_snapshot](#input\_copy\_tags\_to\_snapshot) | Copy tags from DB to a snapshot | `bool` | `true` | no |
| <a name="input_database_name"></a> [database\_name](#input\_database\_name) | The name of the database to create when the DB instance is created | `string` | n/a | yes |
| <a name="input_database_password"></a> [database\_password](#input\_database\_password) | (Required unless a snapshot\_identifier or replicate\_source\_db is provided) Password for the master DB user | `string` | `""` | no |
| <a name="input_database_port"></a> [database\_port](#input\_database\_port) | Database port (\_e.g.\_ `3306` for `MySQL`). Used in the DB Security Group to allow access to the DB instance from the provided `security_group_ids` | `number` | n/a | yes |
| <a name="input_database_user"></a> [database\_user](#input\_database\_user) | (Required unless a `snapshot_identifier` or `replicate_source_db` is provided) Username for the master DB user | `string` | `""` | no |
| <a name="input_db_options"></a> [db\_options](#input\_db\_options) | A list of DB options to apply with an option group. Depends on DB engine | <pre>list(object({<br>    db_security_group_memberships  = list(string)<br>    option_name                    = string<br>    port                           = number<br>    version                        = string<br>    vpc_security_group_memberships = list(string)<br><br>    option_settings = list(object({<br>      name  = string<br>      value = string<br>    }))<br>  }))</pre> | `[]` | no |
| <a name="input_db_parameter"></a> [db\_parameter](#input\_db\_parameter) | A list of DB parameters to apply. Note that parameters may differ from a DB family to another | <pre>list(object({<br>    apply_method = string<br>    name         = string<br>    value        = string<br>  }))</pre> | `[]` | no |
| <a name="input_db_parameter_group"></a> [db\_parameter\_group](#input\_db\_parameter\_group) | The DB parameter group family name. The value depends on DB engine used. See [DBParameterGroupFamily](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBParameterGroup.html#API_CreateDBParameterGroup_RequestParameters) for instructions on how to retrieve applicable value. | `string` | n/a | yes |
| <a name="input_db_subnet_group_name"></a> [db\_subnet\_group\_name](#input\_db\_subnet\_group\_name) | Name of DB subnet group. DB instance will be created in the VPC associated with the DB subnet group. Specify one of `subnet_ids`, `db_subnet_group_name` or `availability_zone` | `string` | `null` | no |
| <a name="input_deletion_protection"></a> [deletion\_protection](#input\_deletion\_protection) | Set to true to enable deletion protection on the RDS instance | `bool` | `false` | no |
| <a name="input_delimiter"></a> [delimiter](#input\_delimiter) | Delimiter to be used between ID elements.<br>Defaults to `-` (hyphen). Set to `""` to use no delimiter at all. | `string` | `null` | no |
| <a name="input_descriptor_formats"></a> [descriptor\_formats](#input\_descriptor\_formats) | Describe additional descriptors to be output in the `descriptors` output map.<br>Map of maps. Keys are names of descriptors. Values are maps of the form<br>`{<br>   format = string<br>   labels = list(string)<br>}`<br>(Type is `any` so the map values can later be enhanced to provide additional options.)<br>`format` is a Terraform format string to be passed to the `format()` function.<br>`labels` is a list of labels, in order, to pass to `format()` function.<br>Label values will be normalized before being passed to `format()` so they will be<br>identical to how they appear in `id`.<br>Default is `{}` (`descriptors` output will be empty). | `any` | `{}` | no |
| <a name="input_dns_zone_id"></a> [dns\_zone\_id](#input\_dns\_zone\_id) | The ID of the DNS Zone in Route53 where a new DNS record will be created for the DB host name | `string` | `""` | no |
| <a name="input_enabled"></a> [enabled](#input\_enabled) | Set to false to prevent the module from creating any resources | `bool` | `null` | no |
| <a name="input_enabled_cloudwatch_logs_exports"></a> [enabled\_cloudwatch\_logs\_exports](#input\_enabled\_cloudwatch\_logs\_exports) | List of log types to enable for exporting to CloudWatch logs. If omitted, no logs will be exported. Valid values (depending on engine): alert, audit, error, general, listener, slowquery, trace, postgresql (PostgreSQL), upgrade (PostgreSQL). | `list(string)` | `[]` | no |
| <a name="input_engine"></a> [engine](#input\_engine) | Database engine type | `string` | `null` | no |
| <a name="input_engine_version"></a> [engine\_version](#input\_engine\_version) | Database engine version, depends on engine type | `string` | n/a | yes |
| <a name="input_environment"></a> [environment](#input\_environment) | ID element. Usually used for region e.g. 'uw2', 'us-west-2', OR role 'prod', 'staging', 'dev', 'UAT' | `string` | `null` | no |
| <a name="input_final_snapshot_identifier"></a> [final\_snapshot\_identifier](#input\_final\_snapshot\_identifier) | Final snapshot identifier e.g.: some-db-final-snapshot-2019-06-26-06-05 | `string` | `""` | no |
| <a name="input_host_name"></a> [host\_name](#input\_host\_name) | The DB host name created in Route53 | `string` | `"db"` | no |
| <a name="input_iam_database_authentication_enabled"></a> [iam\_database\_authentication\_enabled](#input\_iam\_database\_authentication\_enabled) | Specifies whether or mappings of AWS Identity and Access Management (IAM) accounts to database accounts is enabled | `bool` | `false` | no |
| <a name="input_id_length_limit"></a> [id\_length\_limit](#input\_id\_length\_limit) | Limit `id` to this many characters (minimum 6).<br>Set to `0` for unlimited length.<br>Set to `null` for keep the existing setting, which defaults to `0`.<br>Does not affect `id_full`. | `number` | `null` | no |
| <a name="input_instance_class"></a> [instance\_class](#input\_instance\_class) | Class of RDS instance | `string` | n/a | yes |
| <a name="input_iops"></a> [iops](#input\_iops) | The amount of provisioned IOPS. Setting this implies a storage\_type of 'io1'. Default is 0 if rds storage type is not 'io1' | `number` | `0` | no |
| <a name="input_kms_key_arn"></a> [kms\_key\_arn](#input\_kms\_key\_arn) | The ARN of the existing KMS key to encrypt storage | `string` | `""` | no |
| <a name="input_label_key_case"></a> [label\_key\_case](#input\_label\_key\_case) | Controls the letter case of the `tags` keys (label names) for tags generated by this module.<br>Does not affect keys of tags passed in via the `tags` input.<br>Possible values: `lower`, `title`, `upper`.<br>Default value: `title`. | `string` | `null` | no |
| <a name="input_label_order"></a> [label\_order](#input\_label\_order) | The order in which the labels (ID elements) appear in the `id`.<br>Defaults to ["namespace", "environment", "stage", "name", "attributes"].<br>You can omit any of the 6 labels ("tenant" is the 6th), but at least one must be present. | `list(string)` | `null` | no |
| <a name="input_label_value_case"></a> [label\_value\_case](#input\_label\_value\_case) | Controls the letter case of ID elements (labels) as included in `id`,<br>set as tag values, and output by this module individually.<br>Does not affect values of tags passed in via the `tags` input.<br>Possible values: `lower`, `title`, `upper` and `none` (no transformation).<br>Set this to `title` and set `delimiter` to `""` to yield Pascal Case IDs.<br>Default value: `lower`. | `string` | `null` | no |
| <a name="input_labels_as_tags"></a> [labels\_as\_tags](#input\_labels\_as\_tags) | Set of labels (ID elements) to include as tags in the `tags` output.<br>Default is to include all labels.<br>Tags with empty values will not be included in the `tags` output.<br>Set to `[]` to suppress all generated tags.<br>**Notes:**<br>  The value of the `name` tag, if included, will be the `id`, not the `name`.<br>  Unlike other `null-label` inputs, the initial setting of `labels_as_tags` cannot be<br>  changed in later chained modules. Attempts to change it will be silently ignored. | `set(string)` | <pre>[<br>  "default"<br>]</pre> | no |
| <a name="input_license_model"></a> [license\_model](#input\_license\_model) | License model for this DB. Optional, but required for some DB Engines. Valid values: license-included \| bring-your-own-license \| general-public-license | `string` | `""` | no |
| <a name="input_maintenance_window"></a> [maintenance\_window](#input\_maintenance\_window) | The window to perform maintenance in. Syntax: 'ddd:hh24:mi-ddd:hh24:mi' UTC | `string` | `"Mon:03:00-Mon:04:00"` | no |
| <a name="input_major_engine_version"></a> [major\_engine\_version](#input\_major\_engine\_version) | Database MAJOR engine version, depends on engine type | `string` | `""` | no |
| <a name="input_max_allocated_storage"></a> [max\_allocated\_storage](#input\_max\_allocated\_storage) | The upper limit to which RDS can automatically scale the storage in GBs | `number` | `0` | no |
| <a name="input_monitoring_interval"></a> [monitoring\_interval](#input\_monitoring\_interval) | The interval, in seconds, between points when Enhanced Monitoring metrics are collected for the DB instance. To disable collecting Enhanced Monitoring metrics, specify 0. Valid Values are 0, 1, 5, 10, 15, 30, 60. | `string` | `"0"` | no |
| <a name="input_monitoring_role_arn"></a> [monitoring\_role\_arn](#input\_monitoring\_role\_arn) | The ARN for the IAM role that permits RDS to send enhanced monitoring metrics to CloudWatch Logs | `string` | `null` | no |
| <a name="input_multi_az"></a> [multi\_az](#input\_multi\_az) | Set to true if multi AZ deployment must be supported | `bool` | `false` | no |
| <a name="input_name"></a> [name](#input\_name) | ID element. Usually the component or solution name, e.g. 'app' or 'jenkins'.<br>This is the only ID element not also included as a `tag`.<br>The "name" tag is set to the full `id` string. There is no tag with the value of the `name` input. | `string` | `null` | no |
| <a name="input_namespace"></a> [namespace](#input\_namespace) | ID element. Usually an abbreviation of your organization name, e.g. 'eg' or 'cp', to help ensure generated IDs are globally unique | `string` | `null` | no |
| <a name="input_option_group_name"></a> [option\_group\_name](#input\_option\_group\_name) | Name of the DB option group to associate | `string` | `""` | no |
| <a name="input_parameter_group_name"></a> [parameter\_group\_name](#input\_parameter\_group\_name) | Name of the DB parameter group to associate | `string` | `""` | no |
| <a name="input_performance_insights_enabled"></a> [performance\_insights\_enabled](#input\_performance\_insights\_enabled) | Specifies whether Performance Insights are enabled. | `bool` | `false` | no |
| <a name="input_performance_insights_kms_key_id"></a> [performance\_insights\_kms\_key\_id](#input\_performance\_insights\_kms\_key\_id) | The ARN for the KMS key to encrypt Performance Insights data. Once KMS key is set, it can never be changed. | `string` | `null` | no |
| <a name="input_performance_insights_retention_period"></a> [performance\_insights\_retention\_period](#input\_performance\_insights\_retention\_period) | The amount of time in days to retain Performance Insights data. Either 7 (7 days) or 731 (2 years). | `number` | `7` | no |
| <a name="input_publicly_accessible"></a> [publicly\_accessible](#input\_publicly\_accessible) | Determines if database can be publicly available (NOT recommended) | `bool` | `false` | no |
| <a name="input_regex_replace_chars"></a> [regex\_replace\_chars](#input\_regex\_replace\_chars) | Terraform regular expression (regex) string.<br>Characters matching the regex will be removed from the ID elements.<br>If not set, `"/[^a-zA-Z0-9-]/"` is used to remove all characters other than hyphens, letters and digits. | `string` | `null` | no |
| <a name="input_replicate_source_db"></a> [replicate\_source\_db](#input\_replicate\_source\_db) | Specifies that this resource is a Replicate database, and to use this value as the source database. This correlates to the `identifier` of another Amazon RDS Database to replicate (if replicating within a single region) or ARN of the Amazon RDS Database to replicate (if replicating cross-region). Note that if you are creating a cross-region replica of an encrypted database you will also need to specify a `kms_key_id`. See [DB Instance Replication](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.Replication.html) and [Working with PostgreSQL and MySQL Read Replicas](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ReadRepl.html) for more information on using Replication. | `string` | `null` | no |
| <a name="input_security_group_ids"></a> [security\_group\_ids](#input\_security\_group\_ids) | The IDs of the security groups from which to allow `ingress` traffic to the DB instance | `list(string)` | `[]` | no |
| <a name="input_skip_final_snapshot"></a> [skip\_final\_snapshot](#input\_skip\_final\_snapshot) | If true (default), no snapshot will be made before deleting DB | `bool` | `true` | no |
| <a name="input_snapshot_identifier"></a> [snapshot\_identifier](#input\_snapshot\_identifier) | Snapshot identifier e.g: rds:production-2019-06-26-06-05. If specified, the module create cluster from the snapshot | `string` | `null` | no |
| <a name="input_stage"></a> [stage](#input\_stage) | ID element. Usually used to indicate role, e.g. 'prod', 'staging', 'source', 'build', 'test', 'deploy', 'release' | `string` | `null` | no |
| <a name="input_storage_encrypted"></a> [storage\_encrypted](#input\_storage\_encrypted) | (Optional) Specifies whether the DB instance is encrypted. The default is false if not specified | `bool` | `true` | no |
| <a name="input_storage_type"></a> [storage\_type](#input\_storage\_type) | One of 'standard' (magnetic), 'gp2' (general purpose SSD), or 'io1' (provisioned IOPS SSD) | `string` | `"standard"` | no |
| <a name="input_subnet_ids"></a> [subnet\_ids](#input\_subnet\_ids) | List of subnet IDs for the DB. DB instance will be created in the VPC associated with the DB subnet group provisioned using the subnet IDs. Specify one of `subnet_ids`, `db_subnet_group_name` or `availability_zone` | `list(string)` | `[]` | no |
| <a name="input_tags"></a> [tags](#input\_tags) | Additional tags (e.g. `{'BusinessUnit': 'XYZ'}`).<br>Neither the tag keys nor the tag values will be modified by this module. | `map(string)` | `{}` | no |
| <a name="input_tenant"></a> [tenant](#input\_tenant) | ID element \_(Rarely used, not included by default)\_. A customer identifier, indicating who this instance of a resource is for | `string` | `null` | no |
| <a name="input_vpc_id"></a> [vpc\_id](#input\_vpc\_id) | VPC ID the DB instance will be created in | `string` | n/a | yes |

## Outputs

| Name | Description |
|------|-------------|
| <a name="output_hostname"></a> [hostname](#output\_hostname) | DNS host name of the instance |
| <a name="output_instance_address"></a> [instance\_address](#output\_instance\_address) | Address of the instance |
| <a name="output_instance_arn"></a> [instance\_arn](#output\_instance\_arn) | ARN of the instance |
| <a name="output_instance_endpoint"></a> [instance\_endpoint](#output\_instance\_endpoint) | DNS Endpoint of the instance |
| <a name="output_instance_id"></a> [instance\_id](#output\_instance\_id) | ID of the instance |
| <a name="output_option_group_id"></a> [option\_group\_id](#output\_option\_group\_id) | ID of the Option Group |
| <a name="output_parameter_group_id"></a> [parameter\_group\_id](#output\_parameter\_group\_id) | ID of the Parameter Group |
| <a name="output_resource_id"></a> [resource\_id](#output\_resource\_id) | The RDS Resource ID of this instance. |
| <a name="output_security_group_id"></a> [security\_group\_id](#output\_security\_group\_id) | ID of the Security Group |
| <a name="output_subnet_group_id"></a> [subnet\_group\_id](#output\_subnet\_group\_id) | ID of the created Subnet Group |
<!-- markdownlint-restore -->

## Copyright

Copyright © 2017-2021 [Cloud Posse, LLC](https://cpco.io/copyright)
