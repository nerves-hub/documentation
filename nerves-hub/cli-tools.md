---
description: >-
  Introducing the NervesHub command-line tools aka. the CLI.
---

# CLI tools

## Installation

The easiest way to install is via [Homebrew](https://brew.sh/).

```
brew install nerves-hub/tap/nh
```

The second easiest is via curl:ing a shell script.

```
curl --proto '=https' --tlsv1.2 -fsSL https://raw.githubusercontent.com/nerves-hub/nerves_hub_cli/master/install.sh | sh
```

More details and alternative installation methods are available in [the repo](https://github.com/nerves-hub/nerves_hub_cli).

## Setup

To set your NervesHub instance to use with the CLI, use this command, replacing the specific URL with your instance:

```sh
nh config set uri "https://manage.nervescloud.com/"
```

You need to be authorized with the NervesHub instance, you get that via:

```sh
nh user auth
```

If you want to pass fewer arguments on later calls we can recommend setting up some environment variables in your shell.
Usually that means `~/.bashrc` or `~/.zshrc`. The most commonly needed is the org:

```sh
NERVES_HUB_ORG="my-org-name-here"
```

## Try it out

You can start with the help function which gives you a lot of your options:

```sh
nh
```

To list devices for example:

```sh
nh device list --org my-org --product my-product
```

This should get you started with the CLI.

