---
title: Создание параметра диагностики в Azure с помощью шаблона Resource Manager
description: Создавайте диагностические настройки с помощью шаблона «Менеджер ресурсов» для переадресации журналов платформы Azure в журналы Azure Monitor, хранилище Azure или концентраторы событий Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: a2569ca3f998030680bd7dbd872d71ccd372a25d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672435"
---
# <a name="create-diagnostic-setting-in-azure-using-a-resource-manager-template"></a>Создание диагностической настройки в Azure с помощью шаблона диспетчера ресурсов
[Диагностические настройки](diagnostic-settings.md) в Azure Monitor указывают, куда отправлять [журналы платформы,](platform-logs-overview.md) собранные ресурсами Azure и платформой Azure, от которой они зависят. В этой статье приведены подробные сведения и примеры использования [шаблона Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) для создания и настройки диагностических настроек для сбора журналов платформ в разных пунктах назначения.

> [!NOTE]
> Поскольку нельзя [создать диагностическую настройку](diagnostic-settings.md) для журнала Azure Activity, используя PowerShell или CLI, как диагностические настройки для других ресурсов Azure, создайте шаблон менеджера ресурсов для журнала Activity с использованием информации в этой статье и развернуть шаблон с помощью PowerShell или CLI.

## <a name="deployment-methods"></a>Методы развертывания
Шаблоны менеджера ресурсов можно развернуть с помощью любого действительного метода, включая PowerShell и CLI. Диагностические настройки для журнала activity `az deployment create` должны быть `New-AzDeployment` развернуты в подписке, используя для CLI или для PowerShell. Диагностические настройки для журналов ресурсов `az group deployment create` должны быть `New-AzResourceGroupDeployment` развернуты в группе ресурсов, используюйдля CLI или PowerShell.

Для получения подробной информации просмотрите [ресурсы «Развертывание» с шаблонами «Менеджер ресурсов» и ресурсами Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md) и [развертывании с шаблонами «Менеджер ресурсов» и Azure CLI.](../../azure-resource-manager/templates/deploy-cli.md) 





## <a name="resource-logs"></a>Журналы ресурсов
Для журналов ресурсов добавьте `<resource namespace>/providers/diagnosticSettings` в шаблон ресурс типа. Раздел свойств следует формату, описанного в [диагностических настройках - Создание или обновление.](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate) Предоставьте `category` в `logs` разделе раздел для каждой из категорий, действительных для ресурса, который вы хотите собрать. Добавьте `metrics` свойство для сбора метрик ресурсов в те же направления, если [ресурс поддерживает метрики.](metrics-supported.md)

Ниже приводится шаблон, который собирает категорию журнала ресурсов для определенного ресурса в рабочее пространство анализа журналов, учетную запись хранения и концентратор событий.

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
Ниже приводится пример, который создает диагностическую настройку для автоматической настройки, которая позволяет передавать журналы ресурсов в концентратор событий, учетную запись хранения и рабочее пространство log Analytics.

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
Для журнала Деятельности Azure добавьте `Microsoft.Insights/diagnosticSettings`ресурс типа . Доступные категории перечислены в [Категориях в журнале активности](activity-log-view.md#categories-in-the-activity-log). Ниже приводится шаблон, который собирает все категории журналов активности в рабочее пространство log Analytics, учетную запись хранения и концентратор событий.


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


## <a name="next-steps"></a>Дальнейшие действия
* Подробнее о [журналах платформ в Azure](platform-logs-overview.md).
* Узнайте о [настройках диагностики.](diagnostic-settings.md)
