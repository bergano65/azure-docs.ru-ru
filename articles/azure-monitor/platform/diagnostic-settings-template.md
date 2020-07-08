---
title: Создание параметра диагностики в Azure с помощью шаблона Resource Manager
description: Создайте параметры диагностики с помощью шаблона диспетчер ресурсов, чтобы пересылать журналы платформы Azure в журналы Azure Monitor, службу хранилища Azure или концентраторы событий Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: a2569ca3f998030680bd7dbd872d71ccd372a25d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77672435"
---
# <a name="create-diagnostic-setting-in-azure-using-a-resource-manager-template"></a>Создание параметра диагностики в Azure с помощью шаблона диспетчер ресурсов
[Параметры диагностики](diagnostic-settings.md) в Azure Monitor укажите, куда следует отправить [журналы платформы](platform-logs-overview.md) , собираемые ресурсами Azure и платформой Azure, от которых они зависят. В этой статье приводятся подробные сведения и примеры использования [шаблона Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) для создания и настройки параметров диагностики для получения журналов платформы в разные места назначения.

> [!NOTE]
> Так как вы не можете [создать параметр диагностики](diagnostic-settings.md) для журнала действий Azure с помощью POWERSHELL или CLI, например параметров диагностики для других ресурсов Azure, создайте шаблон диспетчер ресурсов для журнала действий, используя сведения в этой статье, а затем разверните шаблон с помощью POWERSHELL или CLI.

## <a name="deployment-methods"></a>Методы развертывания
Вы можете развернуть шаблоны диспетчер ресурсов с помощью любого допустимого метода, включая PowerShell и CLI. Параметры диагностики для журнала действий должны быть развернуты в подписке с помощью `az deployment create` для CLI или `New-AzDeployment` PowerShell. Параметры диагностики для журналов ресурсов должны быть развернуты в группе ресурсов с помощью `az group deployment create` for CLI или `New-AzResourceGroupDeployment` PowerShell.

Дополнительные сведения см. в статьях [развертывание ресурсов с помощью шаблонов диспетчер ресурсов и Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md) и [развертывание ресурсов с помощью шаблонов диспетчер ресурсов и Azure CLI](../../azure-resource-manager/templates/deploy-cli.md) . 





## <a name="resource-logs"></a>Журналы ресурсов
Для журналов ресурсов добавьте в шаблон ресурс типа `<resource namespace>/providers/diagnosticSettings` . Раздел Properties соответствует формату, описанному в разделе [параметры диагностики — создание или обновление](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate). Укажите `category` в `logs` разделе для каждой категории, действующей для ресурса, который требуется получить. Добавьте `metrics` свойство для сбора метрик ресурсов в те же места назначения, если [ресурс поддерживает метрики](metrics-supported.md).

Ниже приведен шаблон, собирающий категорию журнала ресурсов для определенного ресурса в Log Analytics рабочей области, учетной записи хранения и концентратора событий.

```json
"resources": [
  {
    "type": "/<resource namespace>/providers/diagnosticSettings",
    "name": "[concat(parameters('resourceName'),'/microsoft.insights/', parameters('settingName'))]",
    "dependsOn": [
      "[<resource Id for which resource logs will be enabled>]"
    ],
    "apiVersion": "2017-05-01-preview",
    "properties": {
      "name": "[parameters('settingName')]",
      "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
      "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
      "eventHubName": "[parameters('eventHubName')]",
      "workspaceId": "[parameters('workspaceId')]",
      "logs": [
        {
          "category": "<category name>",
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
```



### <a name="example"></a>Пример
Ниже приведен пример, в котором создается параметр диагностики для параметра автомасштабирования, который обеспечивает потоковую передачу журналов ресурсов в концентратор событий, учетную запись хранения и рабочую область Log Analytics.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "autoscaleSettingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the autoscale setting"
            }
        },
        "settingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the diagnostic setting"
            }
        },
        "workspaceId": {
            "type": "string",
            "metadata": {
                "description": "ResourceIDl of the Log Analytics workspace in which resource logs should be saved."
            }
        },
        "storageAccountId": {
            "type": "string",
            "metadata": {
              "description": "ResourceID of the Storage Account in which resource logs should be saved."
            }
        },
        "eventHubAuthorizationRuleId": {
            "type": "string",
            "metadata": {
              "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
            }
        },
        "eventHubName": {
            "type": "string",
            "metadata": {
                "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
            }
        }
    },
    "variables": {},
    "resources": [
    {
      "type": "microsoft.insights/autoscalesettings/providers/diagnosticSettings",
      "apiVersion": "2017-05-01-preview",
      "name": "[concat(parameters('autoscaleSettingName'),'/microsoft.insights/', parameters('settingName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Insights/autoscalesettings', parameters('autoscaleSettingName'))]"
      ],
      "properties": {
        "workspaceId": "[parameters('workspaceId')]",
        "storageAccountId": "[parameters('storageAccountId')]",
        "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
        "eventHubName": "[parameters('eventHubName')]",
        "logs": [
          {
            "category": "AutoscaleScaleActions",
            "enabled": true
          },
          {
            "category": "AutoscaleEvaluations",
            "enabled": true
          }
        ]
      }
    }
  ]
}
```

## <a name="activity-log"></a>Журнал действий
Для журнала действий Azure Добавьте ресурс типа `Microsoft.Insights/diagnosticSettings` . Доступные категории перечислены в списке [категории в журнале действий](activity-log-view.md#categories-in-the-activity-log). Ниже приведен шаблон, собирающий все категории журналов действий в Log Analytics рабочей области, учетной записи хранения и концентратора событий.


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the diagnostic setting"
            }
        },
        "workspaceId": {
            "type": "string",
            "metadata": {
                "description": "ResourceID of the Log Analytics workspace in which resource logs should be saved."
            }
        },
        "storageAccountId": {
            "type": "string",
            "metadata": {
              "description": "ResourceID of the Storage Account in which resource logs should be saved."
            }
        },
        "eventHubAuthorizationRuleId": {
            "type": "string",
            "metadata": {
              "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
            }
        },
        "eventHubName": {
            "type": "string",
            "metadata": {
                "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Insights/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[parameters('settingName')]",
            "location": "global",
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


## <a name="next-steps"></a>Дальнейшие шаги
* Узнайте больше о [журналах платформы в Azure](platform-logs-overview.md).
* Сведения о [параметрах диагностики](diagnostic-settings.md).
