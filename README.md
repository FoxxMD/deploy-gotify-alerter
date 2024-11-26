# Deploy a Gotify [Alerter](https://komo.do/docs/resources#alerter)

Part of the [*Komodo Hub* collection.](https://github.com/komodo-hub/komodo-hub)

Deploys an [Alerter](https://komo.do/docs/resources#alerter) that pushes to [Gotify](https://gotify.net/). Docker image built from [foxxmd/komodo-utilities](https://github.com/FoxxMD/komodo-utilities).

## Requirements

* A running Gotify instance ([installation docs](https://gotify.net/docs/install))
* A Gotify [App token](https://gotify.net/docs/pushmsg)

## Komodo Resource TOML

```toml
[[stack]]
name = "gotify-alerter"
[stack.config]
repo = "foxxmd/deploy-gotify-alerter"
file_paths = [
  "compose.yaml",
]
environment = """
  ## Required

  # Your Gotify instance URL
  GOTIFY_URL = https://gotify.example.com

  # App Token created for Komodo
  GOTIFY_APP_TOKEN = [[GOTIFY_APP_TOKEN]]

  ## Optional
  # Need to add to `compose.yaml` as well

  # Set the Gotify Priority level based on Komodo alert severity
  #GOTIFY_OK_PRIORITY=3
  #GOTIFY_WARNING_PRIORITY=5
  #GOTIFY_CRITICAL_PRIORITY=8

  # Set whether to include Komodo Severity Level in notification title
  #LEVEL_IN_TITLE=true
"""

[[variable]]
name = "GOTIFY_APP_TOKEN"
value = "AmgdtI-dyUgkiIn"
is_secret = true

[[alerter]]
name = "gotify"
[alerter.config]
enabled = true
endpoint.type = "Custom"
endpoint.params.url = "http://gotify-alerter-ip:7000"
```