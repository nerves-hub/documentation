# Run Terraform

Now everything should be properly configure and it is time to run the terraform script to stand up the app.

## Create Initial Infrastructure

You can initialize the setup by running `setup.sh`

Answer `yes` to create the initial infrastructure.

```text
Do you want to perform these actions in workspace "base"?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes
```

Answer yes to migrate the state to the remote

```text
Do you want to migrate all workspaces to "s3"?
  Both the existing "local" backend and the newly configured "s3" backend
  support workspaces. When migrating between backends, Terraform will copy
  all workspaces (with the same names). THIS WILL OVERWRITE any conflicting
  states in the destination.

  Terraform initialization doesn't currently migrate only select workspaces.
  If you want to migrate a select number of workspaces, you must manually
  pull and push those states.

  If you answer "yes", Terraform will migrate all states. If you answer
  "no", Terraform will abort.

  Enter a value: yes
```

## Run Application Stack

And now the application stack can be run with the `staging.sh` script. Answer `yes` to any further prompts to grant permission to create resources. After this task completes, the app should be visible at the specified domain.

## Debugging

If the build fails due to services being created out of order and missing dependencies just run the `staging.sh` script again.

