# NGINX for Azure Deployment Action

This action syncs NGINX configuration files in the repository to an NGINX deployment in Azure. It enables continuous deployment scenarios where the configuration of the NGINX deployment is automatically updated when changes are made through GitHub workflows.

## Usage example

The following example updates the App Configuration instance each time a change is made to `appsettings.json` in the `master` branch.

```yaml
# File: .github/workflows/nginxForAzureDeploy.yml

name: Sync configuration to NGINX for Azure 
on:
  push:
    branches:
      - main
    paths:
      - config/**

permissions:
      id-token: write
      contents: read

env:
  AZURE_TENANT_ID: '<Tenant ID>'
  AZURE_CLIENT_ID: '<Client ID >'
  AZURE_SUBSCRIPTION_ID: '<The ID of the subscription of the NGINX deployment>'
  AZURE_RESOURCE_GROUP_NAME: '<The name of the resource group of the NGINX deployment>'
  NGINX_DEPLOYMENT_NAME: '<The name of the NGINX deployment to deploy configuration>'
  NGINX_CONFIG_FILE: '<The relative path of the configuration file in the repository>'

jobs:
  Deploy-NGINX-Configuration:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout repository'
      uses: actions/checkout@v2

    - name: 'Run Azure Login with OIDC'
      uses: azure/login@v1
      with:
        client-id: ${{ env.AZURE_CLIENT_ID }}
        tenant-id: ${{ env.AZURE_TENANT_ID }}
        subscription-id: ${{ env.AZURE_SUBSCRIPTION_ID }}

    - name: 'Sync NGINX configuration to NGINX on Azure instance'
      uses: bangbingsyb/nginx-config-sync-action@v1
      with:
        subscription-id: ${{ env.AZURE_SUBSCRIPTION_ID }}
        resource-group-name: ${{ env.AZURE_RESOURCE_GROUP_NAME }}
        nginx-deployment-name: ${{ env.NGINX_DEPLOYMENT_NAME }}
        nginx-config-file-path: ${{ env.NGINX_CONFIG_FILE }}
