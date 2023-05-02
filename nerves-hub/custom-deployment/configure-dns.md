# Configure DNS

First, you will need to manually add a hosted zone to [Route 53](https://aws.amazon.com/route53/) for the domain at which you wish to run nerves hub. You do not need to have the domain registered in Route 53, but you will need to be able to point the domain to the Route 53 nameservers.

If you plan on running both staging and production environments, you will need to configure a second hosted zone with a staging subdomain to the production domain e.g. `staging.domain.com`. Then you will need to add an NS record to the hosted zone for the production domain with the nameservers that were generated for the staging hosted zone.

**NOTE:** If using a custom subdomain, you **MUST** use a name that is less than 7 characters long, otherwise it will result in an AWS resource name character limit error for parts of the Terraform.

Additionally, you'll need to add a `www` CNAME record to each hosted zone pointing to it's primary domain (for certificate validation in the next step).

Then, use [Certificate Manager](https://aws.amazon.com/certificate-manager/) to request public certificates for the `www` subdomain of each of your newly created hosted zones. e.g. `www.domain.com`, `www.staging.domain.com`. Follow the Certificate Manager instructions to validate via DNS or email and _wait_ (up to 30 minutes) for the certificates to be issued.

Once the certificates have been issued, you should remove the `www` CNAME records you created previously (Terraform will make new ones shortly).
