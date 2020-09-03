---
title: Примеры шаблонов Resource Manager для запросов журнала
description: Примеры шаблонов Azure Resource Manager для развертывания запросов журнала Azure Monitor.
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 05/18/2020
ms.openlocfilehash: c5c7d1b79f9dd74db8bb8241166b4a0fbc5954c1
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855736"
---
# <a name="resource-manager-template-samples-for-log-queries-in-azure-monitor"></a>Примеры шаблонов Resource Manager для запросов журнала в Azure Monitor
В этой статье представлены примеры [шаблонов Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) для создания и настройки запросов журнала в Azure Monitor. Каждый пример включает файл шаблона и файл параметров с примерами значений для предоставления шаблона.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="template-references"></a>Ссылки на шаблоны

- [Microsoft.OperationalInsights workspaces/savedSearches](/azure/templates/microsoft.operationalinsights/2020-03-01-preview/workspaces/savedsearches)

## <a name="simple-log-query"></a>Простой запрос журнала
В следующем примере запрос журнала добавляется в рабочую область Log Analytics.

### <a name="template-file"></a>Файл шаблона

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "workspaceName": {
          "type": "string"
      },
      "location": {
        "type": "string"
      }
  },
  "resources": [
    {
      "type": "Microsoft.OperationalInsights/workspaces",
      "apiVersion": "2020-08-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "resources": [
        {
          "type": "savedSearches",
          "apiVersion": "2015-03-20",
          "name": "VMSS query",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "eTag": "*",
            "displayName": "VMSS Instance Count",
            "category": "VMSS",
            "query": "Event | where Source == \"ServiceFabricNodeBootstrapAgent\" | summarize AggregatedValue = count() by Computer",
            "version": 1
          }
        }
      ]
    }
  ]
}

```

### <a name="parameter-file"></a>Файл параметров

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```

## <a name="log-query-as-a-function"></a>Запрос журнала как функция
В следующем примере запрос журнала добавляется как функция в рабочую область Log Analytics.

### <a name="template-file"></a>Файл шаблона

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "workspaceName": {
          "type": "string"
      },
      "location": {
        "type": "string"
      }
  },
  "resources": [
    {
      "type": "Microsoft.OperationalInsights/workspaces",
      "apiVersion": "2017-03-15-preview",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "resources": [
        {
          "type": "savedSearches",
          "apiVersion": "2020-08-01",
          "name": "Cross workspace query",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
            ],
            "properties": {
              "etag": "*",
              "displayName": "Failed Logon Events",
              "category": "Security",
              "FunctionAlias": "failedlogonsecurityevents",
              "query": "
                union withsource=SourceWorkspace
                workspace('workspace1').SecurityEvent,
                workspace('workspace2').SecurityEvent,
                workspace('workspace3').SecurityEvent,
                | where EventID == 4625",
              "version": 1
          }
        }
      ]
    }
  ]
}

```

### <a name="parameter-file"></a>Файл параметров

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```

## <a name="parameterized-function"></a>Параметризованная функция
В следующем примере запрос журнала добавляется как функция, которая использует параметр, в рабочую область Log Analytics. Включается второй запрос журнала, который использует параметризованную функцию.

> [!NOTE]
> В настоящее время шаблон ресурсов является единственным методом, который можно использовать с параметризованными функциями. Любой запрос журнала может использовать функцию после ее установки в рабочей области.

### <a name="template-file"></a>Файл шаблона

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "workspaceName": {
          "type": "string"
      },
      "location": {
        "type": "string"
      }
  },
  "resources": [
    {
      "type": "Microsoft.OperationalInsights/workspaces",
      "apiVersion": "2020-08-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "resources": [
        {
          "type": "savedSearches",
          "apiVersion": "2020-08-01",
          "name": "Parameterized function",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
            ],
            "properties": {
              "etag": "*",
              "displayName": "Unavailable computers function",
              "category": "Samples",
              "FunctionAlias": "UnavailableComputers",
              "FunctionParameters": "argSpan: timespan",
              "query": " Heartbeat | summarize LastHeartbeat=max(TimeGenerated) by Computer| where LastHeartbeat < ago(argSpan)"
          }
        },
        {
          "type": "savedSearches",
          "apiVersion": "2020-08-01",
          "name": "Query using function",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
            ],
            "properties": {
              "etag": "*",
              "displayName": "Unavailable computers",
              "category": "Samples",
              "query": "UnavailableComputers(7days)"
          }
        }
      ]
    }
  ]
}

```

### <a name="parameter-file"></a>Файл параметров

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```


## <a name="next-steps"></a>Дальнейшие действия

* [Другие примеры шаблонов для Azure Monitor](resource-manager-samples.md).
* [Узнайте подробнее о запросах журнала](../log-query/log-query-overview.md).
* [Узнайте подробнее о функциях](../log-query/functions.md).