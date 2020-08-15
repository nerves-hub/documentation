# Requirements

### Repos

The follow git repos will be needed to deploy the infrastructure and run initial database migrations:

- [NervesHub Terraform](https://github.com/nerves-hub/terraform)
- [NervesHub Web](https://github.com/nerves-hub/nerves_hub_web)
- [NervesHub CA](https://github.com/nerves-hub/nerves_hub_ca)

### Docker Images

The infrastructure uses [ECS](https://aws.amazon.com/ecs/) to deploy docker images of the four critical parts of the application. You can create your own docker images or use the ones available in Docker Hub:

- [api](https://hub.docker.com/layers/nerveshub/nerves_hub_api/latest/images/sha256-1bed6ac9343da9b4cebbdc509baa2f5090329397d38acd8892e17b33bde694bb)
- [ca](https://hub.docker.com/layers/nerveshub/nerves_hub_ca/latest/images/sha256-17408ca7c852921d53d66e8e5b08e233d4d1f6e65b7e25947d98880c94c6df03)
- [device](https://hub.docker.com/layers/nerveshub/nerves_hub_device/latest/images/sha256-011e4168da8ea3654f69dc39e33a4b42456656b6e505c7cc51dca145831231fa)
- [www](https://hub.docker.com/layers/nerveshub/nerves_hub_www/latest/images/sha256-fad8af8ec5dc4a76c756eb856baff5fd2f262634d5378c70bd5c9da14e132d37)

### Tooling

You will need the following tools installed locally to be able to execute the commands to build and deploy the app.

[AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html)
[jq](https://stedolan.github.io/jq/download/)
[Terraform v0.12.24](https://releases.hashicorp.com/terraform/0.12.24/)
