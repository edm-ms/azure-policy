# Enforce a Route Table on every subnet

This policy appends a regional route table for every subnet. The route table may target anything (NVA, Azure Firewall, etc.).

## Try on Portal

[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FNetwork%2Fappend-route-table-per-region%2Fazurepolicy.json)

[![Deploy to Azure Gov](https://docs.microsoft.com/azure/azure-policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FNetwork%2Fappend-route-table-per-region%2Fazurepolicy.json)


## Try with PowerShell

````powershell
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'append-route-table-per-region' -DisplayName 'Append route table on every subnet' -description 'This policy appends a specific Route Table on every subnet.' -Policy 'https://raw.githubusercontent.com/edm-ms/azure-policy/master/samples/Network/append-route-table-per-region/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/edm-ms/azure-policy/master/samples/Network/append-route-table-per-region/azurepolicy.parameters.json' -Mode All


# Set the scope to a resource group; may also be a subscription or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the Policy Parameter (JSON format)
$policyparam = '{ "eastus": { "id": "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Network/routeTables/YourTable" } }'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'append-route-table-per-region-assignment' -DisplayName 'Append a regional route table on all subnets' -Scope $scope.ResourceId -PolicyDefinition $definition -PolicyParameter $policyparam
````

## Try with CLI

````cli
# Create the Policy Definition (Subscription scope)
definition=$(az policy definition create --name 'enforce-route-table-on-subnet' --display-name 'Route table on every subnet' --description 'This policy enforces a specific Route Table on every subnet.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/append-route-table-per-region/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/append-route-table-per-region/azurepolicy.parameters.json' --mode All)

# Set the scope to a resource group; may also be a subscription or management group
scope=$(az group show --name 'YourResourceGroup')

# Set the Policy Parameter (JSON format)
policyparam='{ "eastus": { "id": "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Network/routeTables/YourTable" } }'

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'append-route-table-per-region-assignment' --display-name 'Append a regional route table on all subnets' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r` --params "$policyparam")
```