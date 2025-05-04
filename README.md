# netbox-ansible-eda

A project that demonstrates the integration between Netbox and Ansible Event-Driven Automation (EDA). This setup allows you to automatically trigger Ansible playbooks based on events in your Netbox instance.

## Prerequisites

- Python 3.8 or higher
- Ansible 2.9 or higher
- Netbox instance with API access
- Ubuntu/Debian-based system (for apt commands)

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

4. Install Python dependencies:
```bash
pip install -r requirements.txt
```

5. Install Ansible EDA collection:
```bash
ansible-galaxy collection install ansible.eda
```

6. Set up Netbox environment variables:
```bash
export NETBOX_API=<your-netbox-url>
export NETBOX_TOKEN=<your-netbox-token>
```

## Usage

1. Test the Netbox inventory configuration:
```bash
ansible-inventory -i netbox_inv.yml --list
```
This command will verify that your Netbox inventory plugin is working correctly and show you the hosts and groups that will be available to your playbooks.

2. Start the EDA rulebook:
```bash
ansible-rulebook -r netbox-webhooks.yml -i netbox_inv.yml --verbose
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

## Troubleshooting

### Common Issues

1. **Inventory Plugin Errors**
   - Ensure your Netbox API token has the correct permissions
   - Verify the API endpoint URL is correct
   - Check if the Netbox inventory plugin fix has been applied

2. **EDA Connection Issues**
   - Verify the environment variables are set correctly
   - Check the EDA logs for detailed error messages
   - Ensure the Netbox API is accessible from your machine

3. **Python Package Issues**
   - Make sure you're in the virtual environment
   - Try upgrading pip: `pip install --upgrade pip`
   - Reinstall requirements: `pip install -r requirements.txt --force-reinstall`

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request