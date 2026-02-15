**In this exercise, you add a parameter to define the Azure storage account name during deployment. Then, you add a parameter to define which storage-account SKUs are allowed, and define which one to use for this deployment. You also add usefulness to the Azure Resource Manager template (ARM template) by adding an output that you can use later in the deployment process.**





##### **Create parameters for the ARM template:-**

Here, you make your ARM template more flexible by adding parameters that can be set at runtime. Create a parameter for the storageName value.



1. In the azuredeploy.json file in Visual Studio Code, update "parameters":{},, so it looks like:



"parameters": {

&nbsp; "storageName": {

&nbsp;   "type": "string",

&nbsp;   "minLength": 3,

&nbsp;   "maxLength": 24,

&nbsp;   "metadata": {

&nbsp;     "description": "The name of the Azure storage resource"

&nbsp;   }

&nbsp; }

},



2\. To format the JSON file correctly, press Alt+Shift+F.



3\. Use the new parameter in the resources block in both the name and displayName values. The entire file looks like this code example:



{

&nbsp; "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",

&nbsp; "contentVersion": "1.0.0.0",

&nbsp; "parameters": {

&nbsp;   "storageName": {

&nbsp;     "type": "string",

&nbsp;     "minLength": 3,

&nbsp;     "maxLength": 24,

&nbsp;     "metadata": {

&nbsp;       "description": "The name of the Azure storage resource"

&nbsp;     }

&nbsp;   }

&nbsp; },

&nbsp; "functions": \[],

&nbsp; "variables": {},

&nbsp; "resources": \[

&nbsp;   {

&nbsp;     "type": "Microsoft.Storage/storageAccounts",

&nbsp;     "apiVersion": "2025-01-01",

&nbsp;     "name": "\[parameters('storageName')]",

&nbsp;     "tags": {

&nbsp;       "displayName": "\[parameters('storageName')]"

&nbsp;     },

&nbsp;     "location": "\[resourceGroup().location]",

&nbsp;     "kind": "StorageV2",

&nbsp;     "sku": {

&nbsp;       "name": "Standard\_LRS"

&nbsp;     }

&nbsp;   }

&nbsp; ],

&nbsp; "outputs": {}

}





4\. Save the file.





##### 

##### **Deploy the parameterized ARM template:-**

1. Here, you change the name of the deployment to better reflect what this deployment does and fill in a value for the new parameter.



2\. Run the following Azure PowerShell commands in the terminal. This script is identical to the one you used earlier, except the deployment name has been changed. Enter a unique value for the storageName parameter. 



3\. It must be globally unique across Azure, contain 3 to 24 characters, and include only lowercase letters, numbers, and hyphens. 



$templateFile="azuredeploy.json"

$today=Get-Date -Format "MM-dd-yyyy"

$deploymentName="addnameparameter-"+"$today"

New-AzResourceGroupDeployment -Name $deploymentName -TemplateFile $templateFile -storageName {your-unique-name}







##### **Check your deployment:-**

1. When the deployment finishes, go back to the Azure portal in your browser. Go to your resource group, and see that there are now 3 Succeeded deployments. Select this link.
   
2. Notice that all three deployments are in the list.
   
3. Explore the addnameparameter deployment as you did previously.







##### **Add another parameter that limits allowed values:-**

1. Add a new parameter named storageSKU to the parameters section of the azuredeploy.json file.



// This is the allowed values for an Azure storage account

"storageSKU": {

&nbsp;  "type": "string",

&nbsp;  "defaultValue": "Standard\_LRS",

&nbsp;  "allowedValues": \[

&nbsp;    "Standard\_LRS",

&nbsp;    "Standard\_GRS",

&nbsp;    "Standard\_RAGRS",

&nbsp;    "Standard\_ZRS",

&nbsp;    "Premium\_LRS",

&nbsp;    "Premium\_ZRS",

&nbsp;    "Standard\_GZRS",

&nbsp;    "Standard\_RAGZRS"

&nbsp;  ]

&nbsp;}



2\. The first line is a comment. ARM templates support // and /\* \*/ comments.



3\. Update resources to use the storageSKU parameter. If you take advantage of IntelliSense in Visual Studio Code, it makes this step easier.



"sku": {

&nbsp;    "name": "\[parameters('storageSKU')]"

&nbsp;  }



4\. The entire file looks like this code example:



{

&nbsp; "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",

&nbsp; "contentVersion": "1.0.0.0",

&nbsp; "parameters": {

&nbsp;   "storageName": {

&nbsp;     "type": "string",

&nbsp;     "minLength": 3,

&nbsp;     "maxLength": 24,

&nbsp;     "metadata": {

&nbsp;       "description": "The name of the Azure storage resource"

&nbsp;     }

&nbsp;   },

&nbsp;   "storageSKU": {

&nbsp;     "type": "string",

&nbsp;     "defaultValue": "Standard\_LRS",

&nbsp;     "allowedValues": \[

&nbsp;       "Standard\_LRS",

&nbsp;       "Standard\_GRS",

&nbsp;       "Standard\_RAGRS",

&nbsp;       "Standard\_ZRS",

&nbsp;       "Premium\_LRS",

&nbsp;       "Premium\_ZRS",

&nbsp;       "Standard\_GZRS",

&nbsp;       "Standard\_RAGZRS"

&nbsp;     ]

&nbsp;   }

&nbsp; },

&nbsp; "functions": \[],

&nbsp; "variables": {},

&nbsp; "resources": \[

&nbsp;   {

&nbsp;     "type": "Microsoft.Storage/storageAccounts",

&nbsp;     "apiVersion": "2025-01-01",

&nbsp;     "name": "\[parameters('storageName')]",

&nbsp;     "tags": {

&nbsp;       "displayName": "\[parameters('storageName')]"

&nbsp;     },

&nbsp;     "location": "\[resourceGroup().location]",

&nbsp;     "kind": "StorageV2",

&nbsp;     "sku": {

&nbsp;       "name": "\[parameters('storageSKU')]"

&nbsp;     }

&nbsp;   }

&nbsp; ],

&nbsp; "outputs": {}

}



5\. Save the file.







##### **Deploy the ARM template:-**

1. Deploy the template by running the following commands. Fill in a unique name for the storageName parameter. 



$today=Get-Date -Format "MM-dd-yyyy"

$deploymentName="addSkuParameter-"+"$today"

New-AzResourceGroupDeployment -Name $deploymentName -TemplateFile $templateFile -storageName {your-unique-name} -storageSKU Standard\_GRS



2\. Allow this deployment to finish. This deployment succeeds as expected. Your list of allowed values, prevents your template's users from passing in parameter values that don't work for the resource. 



3\. Let's see what happens when you provide an invalid SKU.



4\. Run the following commands to deploy the template with a parameter that isn't allowed. Here, you changed the storageSKU parameter to Basic. Fill in a unique name for the storageName parameter. 



$today=Get-Date -Format "MM-dd-yyyy"

$deploymentName="addSkuParameter-"+"$today"

New-AzResourceGroupDeployment -Name $deploymentName -TemplateFile $templateFile -storageName {your-unique-name} -storageSKU Basic



5\. This deployment fails. Notice the error.







##### **Add output to the ARM template:-**

1. Here, you add to the outputs section of the ARM template to output the endpoints for the storage account resource.
   
2. In the azuredeploy.json file in Visual Studio Code, update "outputs":{}, so it looks like:



"outputs": {

&nbsp; "storageEndpoint": {

&nbsp;   "type": "object",

&nbsp;   "value": "\[reference(parameters('storageName')).primaryEndpoints]"

&nbsp; }

}



3\. Save the file.



4\. In the Azure portal, go to your addOutputs deployment. You can find your output there as well.

