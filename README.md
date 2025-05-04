# netbox-ansible-eda

## Getting Started

1. Clone the repository:
```bash
git clone https://github.com/your-org/netbox-ansible-eda.git
cd netbox-ansible-eda
```

2. Create and activate a Python virtual environment:
```bash
python3 -m venv venv
source venv/bin/activate
```

3. Update system packages and install OpenJDK:
```bash
sudo apt update
sudo apt install openjdk-17-jdk -y
```

4. Install Ansible EDA collection:
```bash
ansible-galaxy collection install ansible.eda
```

5. Install Python dependencies:
```bash
pip install -r requirements.txt
```

6. Set up Netbox environment variables:
```bash
export NETBOX_API=<your-netbox-url>
export NETBOX_TOKEN=<your-netbox-token>
```

## Netbox Inventory Plugin Fix
If you're using Netbox in a subdirectory and encounter path-related errors with the inventory plugin, you'll need to apply a fix to the plugin. This is a known issue when Netbox is not installed at the root of the web server.

1. Locate the inventory plugin file in your virtual environment:
```bash
find ./venv/lib/python*/site-packages/ansible_collections/netbox/netbox/plugins/inventory/ -name "nb_inventory.py"
```

2. Edit the file and add the following code just before `self.api_version = version.parse(netbox_api_version)`:
```python
prefix = "^/" + re.escape(openapi["servers"][0]["url"])
openapi["paths"] = {re.sub(prefix, "/", k): v for k, v in openapi["paths"].items()}
```

This fix handles cases where the OpenAPI paths include the full server URL prefix, which can cause issues with API endpoint matching.