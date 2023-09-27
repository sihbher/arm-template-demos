This Azure Resource Manager (ARM) template is used to deploy Azure resources, including a storage account, an App Service Plan, and a web app. Let's break down what each part of the template does:

1. **Schema and Content Version**:
   - `$schema`: Specifies the schema version for the ARM template.
   - `contentVersion`: Specifies the content version of the template.

2. **Parameters**:
   - This section defines the input parameters that can be provided when deploying the template. These parameters include:
     - `storagePrefix`: A string parameter for specifying a prefix for the storage account name.
     - `storageSKU`: A string parameter with predefined allowed values for specifying the SKU (performance and redundancy level) of the storage account.
     - `location`: A string parameter with a default value of the location where the resources will be deployed.
     - `appServicePlanName`: A string parameter with a default value for the name of the App Service Plan.
     - `webAppName`: A string parameter with a minimum length and a metadata description for the web app name.
     - `linuxFxVersion`: A string parameter with a default value and metadata description for the runtime stack of the web app.
     - `resourceTags`: An object parameter with default key-value pairs for tagging the resources.

3. **Variables**:
   - This section defines variables used within the template. The variables include:
     - `uniqueStorageName`: A variable that combines the `storagePrefix` parameter with a unique string derived from the resource group's ID to create a unique storage account name.
     - `webAppPortalName`: A variable that combines the `webAppName` parameter with a unique string derived from the resource group's ID to create a unique web app name.

4. **Resources**:
   - This section defines the Azure resources to be deployed. It includes:
     - A storage account (`Microsoft.Storage/storageAccounts`) with the specified properties like location, tags, SKU, and supporting HTTPS traffic only.
     - An App Service Plan (`Microsoft.Web/serverfarms`) with properties like location, tags, and SKU.
     - A web app (`Microsoft.Web/sites`) that depends on the App Service Plan. It specifies the server farm (App Service Plan) it belongs to and the Linux runtime stack version.

5. **Outputs**:
   - This section defines the output values that can be retrieved after the deployment. In this case, it provides the primary endpoints of the deployed storage account.

When you deploy this ARM template, you'll need to provide values for the parameters, and it will create the specified resources in your Azure subscription based on the defined configuration. The template is designed to create a web app running on a Linux-based App Service Plan and to associate it with a storage account, allowing you to host web content and store data in Azure.



# Option 1 - Deploy using parameters json file:

Run this powershell commands:

```Powershell
$templateFile = "template.json"
$parameterFile="parameters.json"
New-AzResourceGroup `
  -Name myResourceGroupDev `
  -Location "East US"
New-AzResourceGroupDeployment `
  -Name devenvironment `
  -ResourceGroupName myResourceGroupDev `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile

```

# Option 2 - Deploy using github actions

Follow this article: [Deploy ARM templates by using GitHub Actions](https://learn.microsoft.com/en-us/azure/azure-resource-manager/templates/deploy-github-actions?tabs=userlevel)

1. Create a Service Principal using Azure CLI

```bash
az ad sp create-for-rbac --name "myDeploymentSP" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name> \
                            --sdk-auth
```

az ad sp create-for-rbac --name "myDeploymentSP" --role contributor \
                            --scopes /subscriptions/c3ec59d4-595f-4848-a4c1-4d6b9ecb2890/resourceGroups/<group-name> \
                            --sdk-auth

2. Create the required secrets
-  AZURE_LOCATION
-  AZURE_RG
-  AZURE_SUBSCRIPTION
-  AZURE_CREDENTIALS. Which should be in this format: 



```JSON
{
    "clientId": "<Set your cliendID>",
    "clientSecret": "<Set your client secret>",
    "subscriptionId": "<Set your subscription ID>",
    "tenantId": "<Set your tenant ID>"
  }
``````