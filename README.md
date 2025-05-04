# netbox-ansible-eda

## System Requirements
- OpenJDK 17 JDK (`sudo apt install openjdk-17-jdk -y`)

## Netbox Inventory Plugin Fix
If you're using Netbox in a subdirectory and encounter path-related errors with the inventory plugin, you'll need to apply a fix to the plugin. This is a known issue when Netbox is not installed at the root of the web server.

1. Locate the inventory plugin file:
```bash
find ~/.ansible/collections/ansible_collections/netbox/netbox/plugins/inventory/ -name "nb_inventory.py"
```

2. Edit the file and add the following code just before `self.api_version = version.parse(netbox_api_version)`:
```python
prefix = "^/" + re.escape(openapi["servers"][0]["url"])
openapi["paths"] = {re.sub(prefix, "/", k): v for k, v in openapi["paths"].items()}
```

This fix handles cases where the OpenAPI paths include the full server URL prefix, which can cause issues with API endpoint matching.