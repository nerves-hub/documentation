# Create tfvars

Before you can deploy the architecture with Terraform you will need to set the variables that Terraform will use to generate the AWS services. First, You will need to clone and change directory to the the [NervesHub Terraform repo](https://github.com/nerves-hub/terraform). From there you can copy the example tfvars file:

```bash
 cp terraform.tfvars.example terraform.tfvars
```

If you haven't done so yet, you will need to configure your AWS credentials so that the AWS CLI tool can access AWS. Please note the associated profile name. Finally, fill out the variables in the newly created `terraform.tfvars` with the data that will best support your instance of NervesHub. Below is a brief description of required or important variables and suggested values:

* `aws_region` = the region to which you want the app deployed e.g. `us-east-1`
* `aws_profile` = the profile from your AWS credentials file that will grant Terraform access to AWS
* `bucket` = an [S3 bucket](https://aws.amazon.com/s3/) for storing the Terraform state files
* `dynamodb_table` = a [dynamodb table](https://aws.amazon.com/dynamodb/) for locking the Terraform state
* `key` = name for the Terraform state file e.g. `terraform.tfstate`
* `operators` = a list of IAM users
* `db_username` = name for db user
* `db_password` = securely generated random string password for the DB
* `domain` = the domain matching the production hosted zone created in the [configure DNS step](configure-dns.md)
* `web_secret_key_base` = securely generated random string \(e.g. `mix phx.gen.secret`\)
* `web_smtp_username` = the Access Key ID saved from the [configure SES step](configure-ses.md)
* `web_smtp_password` = the Secret Access Key from the [configure SES step](configure-ses.md)
* `web_from_email` = the email address from which email will be sent on behalf of the app. [Configured in SES step](configure-ses.md)
* `bucket_prefix` = the prefix for all S3 buckets that will be generated
* `erl_cookie` = securely generated random string \(e.g. `mix phx.gen.secret`\)
* `www_live_view_signing_salt` = securely generated random string \(e.g. `mix phx.gen.secret 32`\)
* `ca_image` = docker repo for nerves\_hub\_ca e.g. `nerveshub/nerves_hub_ca:latest`
* `www_image` = docker repo for nerves\_hub\_www e.g. `nerveshub/nerves_hub_www:latest`
* `device_image` = docker repo for nerves\_hub\_device e.g. `nerveshub/nerves_hub_device:latest`
* `api_image` = docker repo for nerves\_hub\_api e.g. `nerveshub/nerves_hub_api:latest`
* `whitelist` = a list of IP addresses that can access the site \(optional, usually used for a staging environment\)

