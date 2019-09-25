---
title: Создание параметра диагностики в Azure с помощью шаблона диспетчер ресурсов | Документация Майкрософт
description: Создайте параметры диагностики с помощью шаблона диспетчер ресурсов, чтобы пересылать журналы платформы Azure в журналы Azure Monitor, службу хранилища Azure или концентраторы событий Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: f65e3c4f9582fcc5c28412d44e513fa6bcb9e870
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262340"
---
# <a name="create-diagnostic-setting-in-azure-using-a-resource-manager-template"></a>Создание параметра диагностики в Azure с помощью шаблона диспетчер ресурсов
[Журналы платформы](platform-logs-overview.md) в Azure предоставляют подробные сведения о диагностике и аудите для ресурсов Azure и платформы Azure, от которых они зависят. В этой статье приводятся сведения об использовании [шаблона Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) для настройки параметров диагностики для получения журналов платформы в разные места назначения. Это позволяет автоматически начать сбор журналов платформы при создании ресурса.


## <a name="resource-manager-template"></a>Шаблон Resource Manager
Существует два раздела шаблона диспетчер ресурсов, которые необходимо изменить для создания параметров диагностики. Эти разделы описаны в следующих разделах.

### <a name="parameters"></a>Параметры
В зависимости от [целевых](diagnostic-settings.md#destinations) объектов для параметра диагностики добавьте параметры в большой двоичный объект параметров для имени учетной записи хранения, идентификатора правила авторизации концентратора событий и идентификатора рабочей области log Analytics.
   
```json
"settingName": {
  "type": "string",
  "metadata": {
    "description": "Name for the diagnostic setting resource. Eg. 'archiveToStorage' or 'forSecurityTeam'."
  }
},
"storageAccountName": {
  "type": "string",
  "metadata": {
    "description": "Name of the Storage Account in which platform logs should be saved."
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
},
"workspaceId":{
  "type": "string",
  "metadata": {
    "description": "Azure Resource ID of the Log Analytics workspace for the Log Analytics workspace to which logs will be sent."
  }
}
```

### <a name="resources"></a>Ресурсы
В массиве ресурсов ресурса, для которого необходимо создать параметр диагностики, добавьте ресурс типа `[resource namespace]/providers/diagnosticSettings`. Раздел Properties соответствует формату, описанному в разделе [параметры диагностики — создание или обновление](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate). Добавьте свойство для сбора метрик ресурсов в те же места назначения, если [ресурс поддерживает метрики.](metrics-supported.md) `metrics`
   
```json
"resources": [
  {
    "type": "providers/diagnosticSettings",
    "name": "[concat('Microsoft.Insights/', parameters('settingName'))]",
    "dependsOn": [
      "[/*resource Id for which resource logs will be enabled>*/]"
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
          "category": "/* log category name */",
          "enabled": true,
          "retentionPolicy": {
            "days": 0,
            "enabled": false
          }
        }
      ],
      "metrics": [
        {
          "category": "AllMetrics",
          "enabled": true,
          "retentionPolicy": {
            "enabled": false,
            "days": 0
          }
        }
      ]
    }
  }
]
```



## <a name="example"></a>Пример
Ниже приведен полный пример, который создает приложение логики и создает параметр диагностики, позволяющий потоковую передачу журналов ресурсов в концентратор событий и хранилище в учетной записи хранения.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "logicAppName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Logic App that will be created."
      }
    },
    "testUri": {
      "type": "string",
      "defaultValue": "https://azure.microsoft.com/status/feed/"
    },
    "settingName": {
      "type": "string",
      "metadata": {
        "description": "Name of the setting. Name for the diagnostic setting resource. Eg. 'archiveToStorage' or 'forSecurityTeam'."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which resource logs should be saved."
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
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Logic/workflows",
      "name": "[parameters('logicAppName')]",
      "apiVersion": "2016-06-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "definition": {
          "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "testURI": {
              "type": "string",
              "defaultValue": "[parameters('testUri')]"
            }
          },
          "triggers": {
            "recurrence": {
              "type": "recurrence",
              "recurrence": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          "actions": {
            "http": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('testUri')"
              },
              "runAfter": {}
            }
          },
          "outputs": {}
        },
        "parameters": {}
      },
      "resources": [
        {
          "type": "providers/diagnosticSettings",
          "name": "[concat('Microsoft.Insights/', parameters('settingName'))]",
          "dependsOn": [
            "[resourceId('Microsoft.Logic/workflows', parameters('logicAppName'))]"
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
                "category": "WorkflowRuntime",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              }
            ],
            "metrics": [
              {
                "timeGrain": "PT1M",
                "enabled": true,
                "retentionPolicy": {
                  "enabled": false,
                  "days": 0
                }
              }
            ]
          }
        }
      ],
      "dependsOn": []
    }
  ]
}

```


## <a name="next-steps"></a>Следующие шаги
* Узнайте больше о [журналах платформы в Azure](platform-logs-overview.md).
* Сведения о [параметрах диагностики](diagnostic-settings.md).