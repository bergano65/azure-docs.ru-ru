---
title: Примеры шаблонов Resource Manager для параметров диагностики
description: Примеры шаблонов Azure Resource Manager для применения параметров диагностики Azure Monitor к ресурсу Azure.
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 07/17/2020
ms.openlocfilehash: cd58df3936092310e1a26aeedc3ba7599849359c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87024101"
---
# <a name="resource-manager-template-samples-for-diagnostic-settings-in-azure-monitor"></a>Примеры шаблонов Resource Manager для параметров диагностики в Azure Monitor
Эта статья содержит [примеры шаблонов Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) для создания параметров диагностики для ресурса Azure. Каждый пример включает файл шаблона и файл параметров с примерами значений для предоставления в шаблоне.

Чтобы создать параметр диагностики для ресурса Azure, добавьте в шаблон ресурс типа `<resource namespace>/providers/diagnosticSettings`. В этой статье приведены примеры для некоторых типов ресурсов, но один этот же шаблон можно применить и к другим типам ресурсов. Набор допустимых журналов и метрик будет различным для каждого типа ресурсов.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]

## <a name="diagnostic-setting-for-activity-log"></a>Параметр диагностики для журнала действий
В следующем примере создается параметр диагностики для журнала действий путем добавления в шаблон ресурса типа `Microsoft.Insights/diagnosticSettings`.

> [!IMPORTANT]
> Параметры диагностики для журналов действий создаются для подписки, а не для группы ресурсов (как в случае с параметрами для ресурсов Azure). Чтобы развернуть шаблон управления ресурсами, выполните команду `New-AzSubscriptionDeployment` в PowerShell или `az deployment sub create` в Azure CLI.

### <a name="template-file"></a>Файл шаблона

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
          "type": "String"
        },
        "workspaceId": {
          "type": "String"
        },
        "storageAccountId": {
          "type": "String"
        },
        "eventHubAuthorizationRuleId": {
          "type": "String"
        },
        "eventHubName": {
          "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Insights/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[parameters('settingName')]",
            "properties": {
                "workspaceId": "[parameters('workspaceId')]",
                "storageAccountId": "[parameters('storageAccountId')]",
                "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
                "eventHubName": "[parameters('eventHubName')]",
                "logs": [
                    {
                        "category": "Administrative",
                        "enabled": true
                    },
                    {
                        "category": "Security",
                        "enabled": true
                    },
                    {
                        "category": "ServiceHealth",
                        "enabled": true
                    },
                    {
                        "category": "Alert",
                        "enabled": true
                    },
                    {
                        "category": "Recommendation",
                        "enabled": true
                    },
                    {
                        "category": "Policy",
                        "enabled": true
                    },
                    {
                        "category": "Autoscale",
                        "enabled": true
                    },
                    {
                        "category": "ResourceHealth",
                        "enabled": true
                    }
                ]
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Файл параметров

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "settingName": {
        "value": "Send to all locations"
      },
      "workspaceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      },
      "storageAccountId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
      },
      "eventHubAuthorizationRuleId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNameSpace/authorizationrules/RootManageSharedAccessKey"
      },
      "eventHubName": {
        "value": "my-eventhub"
      }
  }
}
```


## <a name="diagnostic-setting-for-azure-key-vault"></a>Параметр диагностики для Azure Key Vault 
В следующем примере создается параметр диагностики для Azure Key Vault путем добавления в шаблон ресурса типа `Microsoft.KeyVault/vaults/providers/diagnosticSettings`.

### <a name="template-file"></a>Файл шаблона

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
            "type": "String"
        },
        "vaultName": {
            "type": "String"
        },
        "workspaceId": {
            "type": "String"
        },
        "storageAccountId": {
            "type": "String"
        },
        "eventHubAuthorizationRuleId": {
            "type": "String"
        },
        "eventHubName": {
            "type": "String"
        }

    },
    "resources": [
        {
          "type": "Microsoft.KeyVault/vaults/providers/diagnosticSettings",
          "apiVersion": "2017-05-01-preview",
          "name": "[concat(parameters('vaultName'), '/Microsoft.Insights/', parameters('settingName'))]",
          "dependsOn": [],
          "properties": {
            "workspaceId": "[parameters('workspaceId')]",
            "storageAccountId": "[parameters('storageAccountId')]",
            "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
            "eventHubName": "[parameters('eventHubName')]",
            "logs": [
              {
                "category": "AuditEvent",
                "enabled": true
              }
            ],
            "metrics": [
              {
                "category": "AllMetrics",
                "enabled": true
              }
            ]
          }
        }
    ]
}
```

### <a name="parameter-file"></a>Файл параметров

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "settingName": {
          "value": "Send to all locations"
      },
      "vaultName": {
        "value": "MyVault"
      },
      "workspaceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      },
      "storageAccountId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
      },
      "eventHubAuthorizationRuleId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNameSpace/authorizationrules/RootManageSharedAccessKey"
      },
      "eventHubName": {
        "value": "my-eventhub"
      }
  }
}
```

## <a name="diagnostic-setting-for-azure-sql-database"></a>Параметр диагностики для базы данных SQL Azure
В следующем примере создается параметр диагностики для Базы данных SQL Azure путем добавления в шаблон ресурса типа `microsoft.sql/servers/databases/providers/diagnosticSettings`.

### <a name="template-file"></a>Файл шаблона

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
            "type": "String"
        },        
        "serverName": {
            "type": "String"
        },
        "dbName": {
            "type": "String"
        },
        "workspaceId": {
            "type": "String"
        },
        "storageAccountId": {
            "type": "String"
        },
        "eventHubAuthorizationRuleId": {
            "type": "String"
        },
        "eventHubName": {
            "type": "String"
        }

    },
    "resources": [
        {
          "type": "microsoft.sql/servers/databases/providers/diagnosticSettings",
          "apiVersion": "2017-05-01-preview",
          "name": "[concat(parameters('serverName'),'/',parameters('dbName'),'/microsoft.insights', parameters('settingName'))]",
          "dependsOn": [],
          "properties": {
            "workspaceId": "[parameters('workspaceId')]",
            "storageAccountId": "[parameters('storageAccountId')]",
            "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
            "eventHubName": "[parameters('eventHubName')]",
            "logs": [
              {
                "category": "SQLInsights",
                "enabled": true
              },
              {
                "category": "AutomaticTuning",
                "enabled": true
              },
              {
                "category": "QueryStoreRuntimeStatistics",
                "enabled": true
              },
              {
                "category": "QueryStoreWaitStatistics",
                "enabled": true
              },
              {
                "category": "Errors",
                "enabled": true
              },
              {
                "category": "DatabaseWaitStatistics",
                "enabled": true
              },
              {
                "category": "Timeouts",
                "enabled": true
              },
              {
                "category": "Blocks",
                "enabled": true
              },
              {
                "category": "Deadlocks",
                "enabled": true
              }
            ],
            "metrics": [
              {
                "category": "Basic",
                "enabled": true
              },
              {
                "category": "InstanceAndAppAdvanced",
                "enabled": true
              },
              {
                "category": "WorkloadManagement",
                "enabled": true
              }
            ]
          }
        }
    ]
}
```

### <a name="parameter-file"></a>Файл параметров

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "settingName": {
          "value": "Send to all locations"
      },
      "serverName": {
        "value": "MySqlServer"
      },
      "dbName": {
        "value": "MySqlDb"
      },
      "workspaceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      },
      "storageAccountId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
      },
      "eventHubAuthorizationRuleId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNameSpace/authorizationrules/RootManageSharedAccessKey"
      },
      "eventHubName": {
        "value": "my-eventhub"
      }
  }
}
```

## <a name="diagnostic-setting-for-recovery-services-vault"></a>Параметр диагностики для хранилища Служб восстановления
В следующем примере создается параметр диагностики для Служб восстановления Azure. Для этого в шаблон добавляется ресурс типа `microsoft.recoveryservices/vaults/providers/diagnosticSettings`. В этом примере указывается режим сбора, как описано в разделе [Журналы ресурсов Azure](../platform/resource-logs.md#send-to-log-analytics-workspace). Укажите для свойства `logAnalyticsDestinationType` значение `Dedicated` или `AzureDiagnostics`.

### <a name="template-file"></a>Файл шаблона

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "recoveryServicesName": {
            "type": "String"
        },
        "settingName": {
            "type": "String"
        },
        "workspaceId": {
            "type": "String"
        },
        "storageAccountId": {
            "type": "String"
        },
        "eventHubAuthorizationRuleId": {
            "type": "String"
        },
        "eventHubName": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "microsoft.recoveryservices/vaults/providers/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[concat(parameters('recoveryServicesName'), '/Microsoft.Insights/', parameters('settingName'))]",
            "dependsOn": [],
            "properties": {
                "workspaceId": "[parameters('workspaceId')]",
                "storageAccountId": "[parameters('storageAccountId')]",
                "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
                "eventHubName": "[parameters('eventHubName')]",
                "metrics": [],
                "logs": [
                    {
                        "category": "AzureBackupReport",
                        "enabled": false
                    },
                    {
                        "category": "CoreAzureBackup",
                        "enabled": true
                    },
                    {
                        "category": "AddonAzureBackupJobs",
                        "enabled": true
                    },
                    {
                        "category": "AddonAzureBackupAlerts",
                        "enabled": true
                    },
                    {
                        "category": "AddonAzureBackupPolicy",
                        "enabled": true
                    },
                    {
                        "category": "AddonAzureBackupStorage",
                        "enabled": true
                    },
                    {
                        "category": "AddonAzureBackupProtectedInstance",
                        "enabled": true
                    },
                    {
                        "category": "AzureSiteRecoveryJobs",
                        "enabled": false
                    },
                    {
                        "category": "AzureSiteRecoveryEvents",
                        "enabled": false
                    },
                    {
                        "category": "AzureSiteRecoveryReplicatedItems",
                        "enabled": false
                    },
                    {
                        "category": "AzureSiteRecoveryReplicationStats",
                        "enabled": false
                    },
                    {
                        "category": "AzureSiteRecoveryRecoveryPoints",
                        "enabled": false
                    },
                    {
                        "category": "AzureSiteRecoveryReplicationDataUploadRate",
                        "enabled": false
                    },
                    {
                        "category": "AzureSiteRecoveryProtectedDiskDataChurn",
                        "enabled": false
                    }
                ],
                "logAnalyticsDestinationType": "Dedicated"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Файл параметров

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "settingName": {
          "value": "Send to all locations"
      },
      "recoveryServicesName": {
        "value": "my-vault"
      },
      "workspaceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      },
      "storageAccountId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
      },
      "eventHubAuthorizationRuleId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNameSpace/authorizationrules/RootManageSharedAccessKey"
      },
      "eventHubName": {
        "value": "my-eventhub"
      }
  }
}
```

## <a name="next-steps"></a>Дальнейшие действия

* [Другие примеры шаблонов для Azure Monitor](resource-manager-samples.md).
* [Дополнительные сведения о параметрах диагностики](../platform/diagnostic-settings.md).
