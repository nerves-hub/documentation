# Run Migrations

The last thing to do is run initial migrations to configure the DB and seed some starter data.

### Default [VPC](https://aws.amazon.com/vpc/)

A brand new AWS account will have a default VPC that has no name value. Our migration scrips will attempt to search VPCs by name and will throw an error if an unnamed VPC is encountered. Please give any unnamed VPCs some default name value.

### Migrate

It's time to run the migration scrips there are two: one in the NervesHub CA repo and one in the NervesHub WWW repo. In the CA repo run:

```bash
rel/scripts/ecs-migrate.sh nerves-hub-staging nerves-hub-staging nerves-hub-staging-ca nerves-hub-staging-ca-sg
```

In the WWW repo run:

```bash
rel/scripts/ecs-migrate.sh nerves-hub-staging nerves-hub-staging nerves-hub-staging-www nerves-hub-staging-web-sg
```

These scripts may take a few minutes to run and will exit with a 0 status if they are successful.

### Current State

The app should now be totally accessible. The seeded login credentials will be:

```
username: nerveshub
password: nerveshub
```
