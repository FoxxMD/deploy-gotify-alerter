# Deploy a Gotify [Alerter](https://komo.do/docs/resources#alerter)

Part of the [*Komodo Hub* collection.](https://github.com/komodo-hub/komodo-hub)

Deploys an [Alerter](https://komo.do/docs/resources#alerter) that pushes to [Gotify](https://gotify.net/). Docker image built from [foxxmd/komodo-utilities](https://github.com/FoxxMD/komodo-utilities).

## Requirements

* A running Gotify instance ([installation docs](https://gotify.net/docs/install))
* A Gotify [App token](https://gotify.net/docs/pushmsg)

## Deploying

Use **either** of the methods below.

## Komodo Resource Sync

<details>

Create a [Resource Sync](https://komo.do/docs/sync-resources) with the TOML configuration below to

* create the [Stack](https://komo.do/docs/resources#stack) to run deploy-gotify-Alerter
* add the Gotify App Token [Variable](https://komo.do/docs/variables) and
* setup the [Alerter](https://komo.do/docs/resources#alerter) + configuration

Steps:

* Open Komodo Dashboard -> Syncs -> **New Resource Sync**
* Choose Mode -> UI Defined
  * Toggle the following to active:
    * Managed
    * Include Sync Resources
    * Include Sync Variables

Add the below configuration to **Resource File** field and then modify variables for your environment (GOTIFY_URL, GOTIFY_APP_TOKEN, endpoint.params.url, etc...)

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
  GOTIFY_URL = https://gotify.CHANGEME.com

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

  # Prefixes messages with a checkmark when the Alert is in the 'Resolved' state
  #INDICATE_RESOLVED=true

  # Filter if an alert is pushed based on its Resolved status
  # * leave unset to push all alerts
  # * otherwise, alerts will only be pushed if Alert is one of the comma-separated states set here
  #ALLOW_RESOLVED_TYPE=resolved,unresolved

  ## Delay alerts with below types for X milliseconds 
  ## and cancel pushing alert if it is resolved within that time
  #UNRESOLVED_TIMEOUT_TYPES=ServerCpu,ServerMem
  #UNRESOLVED_TIMEOUT=2000
"""

[[variable]]
name = "GOTIFY_APP_TOKEN"
value = "CHANGE_ME"
is_secret = true

[[alerter]]
name = "gotify"
[alerter.config]
enabled = true
endpoint.type = "Custom"
endpoint.params.url = "http://gotify-alerter-ip:7000"
```

**Save** the sync and then **Execute Sync** to create the Alerter.

</details>

## Manual setup

<details>

Create a new [**Stack**](https://komo.do/docs/resources#stack) with the following for `compose.yaml` file.

```yaml
services:
  komodo-gotify:
    image: foxxmd/komodo-gotify-alerter:latest
    environment:
      - GOTIFY_URL=${GOTIFY_URL}
      - GOTIFY_APP_TOKEN=${GOTIFY_API_KEY}
      - UNRESOLVED_TIMEOUT_TYPES=ServerCpu,ServerMem
      - UNRESOLVED_TIMEOUT=20000

    ports:
      - "7000:7000"
```

Add the following to the Stack -> Config -> Environment section:

```
## Required

# Your Gotify instance URL
GOTIFY_URL = https://gotify.CHANGEME.com

# App Token created for Komodo
GOTIFY_APP_TOKEN = [[GOTIFY_APP_TOKEN]]

## Optional

# Set the Gotify Priority level based on Komodo alert severity
#GOTIFY_OK_PRIORITY=3
#GOTIFY_WARNING_PRIORITY=5
#GOTIFY_CRITICAL_PRIORITY=8

# Set whether to include Komodo Severity Level in notification title
#LEVEL_IN_TITLE=true

# Prefixes messages with a checkmark when the Alert is in the 'Resolved' state
#INDICATE_RESOLVED=true

# Filter if an alert is pushed based on its Resolved status
# * leave unset to push all alerts
# * otherwise, alerts will only be pushed if Alert is one of the comma-separated states set here
#ALLOW_RESOLVED_TYPE=resolved,unresolved

## Delay alerts with below types for X milliseconds 
## and cancel pushing alert if it is resolved within that time
#UNRESOLVED_TIMEOUT_TYPES=ServerCpu,ServerMem
#UNRESOLVED_TIMEOUT=2000
```

Make sure to replace placeholder values. `[[GOTIFY_APP_TOKEN]]` is a Komodo [Variable](https://komo.do/docs/variables).

After deploying the Stack create a new [Alerter](https://komo.do/docs/resources#alerter)

* **Endpoint:** `Custom`
  * In the Endpoint field set the IP:PORT of the `komodo-gotify-alerter` stack you created IE `http://192.168.YOUR.IP:7000`
* Optionally, set any **Alert Types** you may need

**Save** the Alerter and then **Test Alerter** to make sure everything is ready to use.

</details>