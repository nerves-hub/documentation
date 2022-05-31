# Configure Certificates

The NervesHub Certificate Authority is used to generate certificates for representing trusted device connections to the NervesHub API. This app needs an initial certificate chain in order to properly operate. Clone the [`nerves_hub_ca`](https://github.com/nerves-hub/nerves_hub_ca) repo and run the following command to generate the required certificates:

```bash
mix nerves_hub_ca.init --path path/to/nerveshub-terraform/ssl/(staging | production)
```

Specify the staging or production directory for whichever environment you are generating certificates.

You may also want to specify the host for which you are generating certificates (e.g. `--host staging.mydomain.com`).
