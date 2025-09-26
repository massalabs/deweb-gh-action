# Massa DeWeb GitHub Action

> By using this tool, you agree with following terms of use:
> - [Community Charter](./COMMUNITY_CHARTER.md)
> - [deweb terms of use](./TERMS_OF_USE_DEWEB.md)

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

## Environment Variables

The following environment variable must be provided for the action to work:

| Name         | Description                                                                 |
|--------------|-----------------------------------------------------------------------------|
| `MASSA_DEWEB_SECRET_KEY` | The private key used to deploy to Massa's DeWeb.  |

In the case of a first deployment, the wallet will be the owner of the deployed website.
In case of an update, the corresponding account should be the owner of the website to update.

Make sure to store the `MASSA_DEWEB_SECRET_KEY` securely as a GitHub secret and reference it in your workflow. For instance `${{ secrets.MASSA_DEWEB_SECRET_KEY }}`.

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
        uses: massalabs/deweb-gh-action@v0.1.0
        with:
          config_file: deweb_cli_config_buildnet.json
          source_folder: dist
        env:
          MASSA_DEWEB_SECRET_KEY: ${{ secrets.MASSA_DEWEB_SECRET_KEY }}

      - name: Get deployed website address
        run: |
          echo "Deployed website address: ${{ steps.deploy.outputs.deployed-website }}"
          
```

## Configuration File

Example of a configuration file (`deweb_cli_config.json`) to deploy a website:

```json
{
  "node_url": "https://buildnet.massa.net/api/v2",
  "metadatas": {
    "TITLE": "The website name - buildnet",
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
  "metadatas": {
    "TITLE": "The website name - buildnet",
    "DESCRIPTION": "Your website description"
  }
}
```

You can customize this file and provide its path using the `config_file` input.

Read the full documentation of DeWeb cli configuration [here](https://docs.massa.net/docs/deweb/cli/config)

## Notes

- This action only supports Linux runners.

## License
License can be found [here](./LICENSE.md)

