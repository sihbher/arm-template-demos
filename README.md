# arm-template-demos

This repo shows some examples on how to deploy some arm templates to Azure


the only workflow that will run after push is **Deploy App Service ARM**


This GitHub Actions workflow is designed to automate the deployment of an Azure Resource Manager (ARM) template for an Azure App Service. Let's break down what each part of the workflow does:

```yaml
on: [workflow_dispatch, push]
name: Deploy App Service ARM
```

- The `on` section specifies the events that trigger this workflow. In this case, it triggers when there's a manual workflow dispatch (`workflow_dispatch`) or when there's a code push (`push`) to the repository. The name of the workflow is "Deploy App Service ARM."

```yaml
jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
```

- This workflow defines a single job named `build-and-deploy` that runs on an Ubuntu-based runner (`ubuntu-latest`). This job will execute the deployment steps.

```yaml
env:
  ResourceGroupName: ${{ secrets.AZURE_RG }}
  ResourceGroupLocation: ${{ secrets.AZURE_LOCATION }}
```

- Environment variables are defined here to store the Azure resource group name and location. These values are populated from GitHub Secrets (`secrets.AZURE_RG` and `secrets.AZURE_LOCATION`).

```yaml
steps:
  # Checkout code
  - uses: actions/checkout@main
```

- The workflow starts by checking out the code from the repository using the `actions/checkout` action.

```yaml
  # Log into Azure
  - uses: azure/login@v1
    with:
      creds: ${{ secrets.AZURE_CREDENTIALS }}
```

- This step logs into an Azure account using Azure CLI. The Azure credentials are provided through the `secrets.AZURE_CREDENTIALS` secret.

```yaml
  # Create resource group if not exists
  - uses: Azure/CLI@v1
    with:
      inlineScript: |
        #!/bin/bash
        az group create --name ${{ env.ResourceGroupName }} --location ${{ env.ResourceGroupLocation }}
        echo "Azure resource group created"
```

- This step uses the Azure CLI to create an Azure resource group if it doesn't already exist. It uses the `env.ResourceGroupName` and `env.ResourceGroupLocation` environment variables.

```yaml
  # Deploy ARM template
  - name: Run ARM deploy
    uses: azure/arm-deploy@v1
    with:
      subscriptionId: ${{ secrets.AZURE_SUBSCRIPTION }}
      resourceGroupName: ${{ secrets.AZURE_RG }}
      template: ./DeployAppService/template.json
      parameters: ./DeployAppService/parameters.json storageSKU=Standard_LRS
```

- This step deploys an ARM template using the `azure/arm-deploy` action. It specifies the Azure subscription, resource group, template file (`template.json`), and parameters file (`parameters.json`). Additionally, it sets the `storageSKU` parameter to "Standard_LRS."

```yaml
  # output containerName variable from template
  - run: echo ${{ steps.deploy.outputs.containerName }}
```

- Finally, this step outputs the value of a variable called `containerName` from the ARM template (if it's defined). However, it seems there might be a mistake in this step as it references `steps.deploy.outputs.containerName`, but the step that deploys the ARM template is named `Run ARM deploy`. It should reference the correct step name to retrieve an output variable from that step.

In summary, this GitHub Actions workflow automates the deployment of an Azure ARM template to create or update Azure resources in a specified resource group. It uses Azure CLI and Azure credentials stored in GitHub Secrets for authentication and configuration. Additionally, it deploys the ARM template with specific parameters, but there's a potential issue with the output variable retrieval step that needs to be corrected for it to work properly.