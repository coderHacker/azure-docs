---
title: Azure Key Vault solution in Log Analytics | Microsoft Docs
description: You can use the Azure Key Vault solution in Log Analytics to review Azure Key Vault logs.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''

ms.assetid: 5e25e6d6-dd20-4528-9820-6e2958a40dae
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: richrund

---
# Azure Key Vault Analytics (Preview) solution in Log Analytics

You can use the Azure Key Vault solution in Log Analytics to review Azure Key Vault AuditEvent logs.

> [!NOTE]
> Azure Key Vault Analytics is a [preview solution](log-analytics-add-solutions.md#preview-management-solutions-and-features).
> 
> 

To use the solution, you need to enable logging of Azure Key Vault diagnostics and direct the diagnostics to a Log Analytics workspace. It is not necessary to write the logs to Azure Blob storage.

## Install and configure the solution
Use the following instructions to install and configure the Azure Key Vault solution:

1. Use `Set-AzureRmDiagnosticSetting` to enable diagnostics logging for the Key Vault resources to monitor: 
2. Enable the Azure Key Vault solution by using the process described in [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md). 

The following PowerShell script provides an example of how to enable diagnostic logging for Key Vault:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'

Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```
 
 

## Review Azure Key Vault data collection details
Azure Key Vault solution collects diagnostics logs directly from the Key Vault.
It is not necessary to write the logs to Azure Blob storage and no agent is required for data collection.

The following table shows data collection methods and other details about how data is collected for Azure Key Vault.

| Platform | Direct agent | Systems Center Operations Manager agent | Azure | Operations Manager required? | Operations Manager agent data sent via management group | Collection frequency |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |![No](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![No](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![Yes](./media/log-analytics-azure-keyvault/oms-bullet-green.png) |![No](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![No](./media/log-analytics-azure-keyvault/oms-bullet-red.png) | on arrival |

## Use Azure Key Vault
After you install the solution, you can view the summary of request statuses over time for your monitored Key Vaults by using the **Azure Key Vault** tile on the **Overview** page of Log Analytics.

![image of Azure Key Vault tile](./media/log-analytics-azure-keyvault/log-analytics-keyvault-tile.png)

After you click the **Overview** tile, you can view summaries of your logs and then drill in to details for the following categories:

* Volume of all key vault operations over time
* Failed operation volumes over time
* Average operational latency by operation
* Quality of service for operations with the number of operations that take more than 1000 ms and a list of operations that take more than 1000 ms

![image of Azure Key Vault dashboard](./media/log-analytics-azure-keyvault/log-analytics-keyvault01.png)

![image of Azure Key Vault dashboard](./media/log-analytics-azure-keyvault/log-analytics-keyvault02.png)

### To view details for any operation
1. On the **Overview** page, click the **Azure Key Vault** tile.
2. On the **Azure Key Vault** dashboard, review the summary information in one of the blades, and then click one to view detailed information about it in the log search page.
   
    On any of the log search pages, you can view results by time, detailed results, and your log search history. You can also filter by facets to narrow the results.

## Log Analytics records
The Azure Key Vault solution analyzes records that have a type of **KeyVaults** that are collected from [AuditEvent logs](../key-vault/key-vault-logging.md) in Azure Diagnostics.  Properties for these records are in the following table:  

| Property | Description |
|:--- |:--- |
| Type |*AzureDiagnostics* |
| SourceSystem |*Azure* |
| CallerIpAddress |IP address of the client who made the request |
| Category | *AuditEvent* |
| CorrelationId |An optional GUID that the client can pass to correlate client-side logs with service-side (Key Vault) logs. |
| DurationMs |Time it took to service the REST API request, in milliseconds. This does not include network latency, so the time that you measure on the client side might not match this time. |
| httpStatusCode_d |HTTP status code returned by the request (for example, *200*) |
| id_s |Unique ID of the request |
| identity_claim_appid_g | GUID for the application id |
| OperationName |Name of the operation, as documented in [Azure Key Vault Logging](../key-vault/key-vault-logging.md) |
| OperationVersion |REST API version requested by the client (for example *2015-06-01*) |
| requestUri_s |Uri of the request |
| Resource |Name of the key vault |
| ResourceGroup |Resource group of the key vault |
| ResourceId |Azure Resource Manager Resource ID. For Key Vault logs, this is the Key Vault resource ID. |
| ResourceProvider |*MICROSOFT.KEYVAULT* |
| ResourceType | *VAULTS* |
| ResultSignature |HTTP status (for example, *OK*) |
| ResultType |Result of REST API request (for example, *Success*) |
| SubscriptionId |Azure subscription ID of the subscription containing the Key Vault |

## Next steps
* Use [Log searches in Log Analytics](log-analytics-log-searches.md) to view detailed Azure Key Vault data.

