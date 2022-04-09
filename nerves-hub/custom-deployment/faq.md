# FAQ

### How can you change device and firmware limits?

This functionality isn't available in the web user interface and must be done via IEx prompt:

```
%{id: org_id} = NervesHubWebCore.Repo.get_by(NervesHubWebCore.Accounts.Org, name: "<org-name-here>")

params = %{devices: 20_000, firmware_per_product: 20, org_id: org_id}

NervesHubWebCore.Accounts.create_org_limit(params)
```
