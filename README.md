# Massa DeWeb GitHub Action

This GitHub Action allows you to deploy static websites to decentralized storage using the Massa DeWeb CLI. It simplifies the deployment process by integrating directly into your GitHub workflows.

## Features

- Deploy static websites to Massa's DeWeb.
- Supports custom configuration files and RPC URLs.
- Automatically installs the required CLI tools.

## Inputs

| Name           | Description                                      | Required | Default                  |
|----------------|--------------------------------------------------|----------|--------------------------|
| `config_file`  | Path to the DeWeb CLI configuration file.        | No       | `deweb_cli_config.json`  |
| `source_folder`| Path to the directory containing website files.  | No      | `dist`                   |
| `rpc_url`      | Massa JSON RPC URL.                              | No       |  `https://buildnet.massa.net/api/v2`  |

## Outputs

| Name              | Description                                   |
|-------------------|-----------------------------------------------|
| `deployed-website`| The address of the deployed or updated website.|

## Usage

Below is an example of how to use this action in your GitHub workflow:

```yaml
name: Deploy to Massa DeWeb

on:
  push:

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Deploy to Massa DeWeb
        id: deploy
        uses: ./  # Use the composite action in the current repository
        with:
          config_file: deweb_cli_config_buildnet.json
          source_folder: dist
        env:
          SECRET_KEY: ${{ secrets.SECRET_KEY }}

      - name: Get deployed address
        run: echo "Deployed address: ${{ steps.deploy.outputs.deployed-website }}"
          
```

## Prerequisites

- A valid `SECRET_KEY` stored as a GitHub secret.
- (Optional) A custom `rpc_url` stored as a GitHub secret if not set in config file.

## Configuration File

The minimum configuration file (`deweb_cli_config.json`) to deploy a website should look like this:

```json
{
  "node_url": "https://buildnet.massa.net/api/v2",
  "chunk_size": 64000,
  "metadatas": {
    "DESCRIPTION": "Your website description"
  }
}
```

After the first deployment, get the address of the deployed website from the action output and set it in the config json under `address`field.
This will allow to update your website at every workflow execution.
Files and metadata will automatically added/updated/deleted

```json
{
  "address": "DEPLOYED_WEBSITE_ADDRESS",
  "node_url": "https://buildnet.massa.net/api/v2",
  "chunk_size": 64000,
  "metadatas": {
    "DESCRIPTION": "Your website description"
  }
}
```

You can customize this file and provide its path using the `config_file` input.

Read the full documentation of DeWeb cli configuration [here](https://docs.massa.net/docs/deweb/cli/config)

## Notes

- This action only supports Linux runners.
- Ensure that the required dependencies (e.g., `npm`) are installed or will be installed by the action.
