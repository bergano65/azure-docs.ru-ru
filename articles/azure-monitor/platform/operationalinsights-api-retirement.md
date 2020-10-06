---
title: Прекращение использования API Azure Monitor
description: Описание прекращения использования старых версий API поставщика ресурсов OperationalInsights.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/02/2020
ms.openlocfilehash: 708901dbc37daa075cf38d3f9ef046ae658ea979
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91744894"
---
# <a name="operationalinsights-api-version-retirement"></a>Прекращение использования версии API OperationalInsights
Корпорация Майкрософт предоставляет уведомление не менее чем за 12 месяцев до прекращения использования API для сглаживания перехода до более новой или поддерживаемой версии. Мы выпустили новую версию (2020-08-01) для API поставщика ресурсов **OperationalInsights** и запустим более ранние версии API до 31 октября 2023 г. Поскольку новые функции и функции и оптимизации добавляются только к текущему API, следует как можно раньше выполнить обновление до последней версии API.

После 31 октября 2023 Azure Monitor больше не будет поддерживать более ранние версии API, чем 2020-08-01. Если вы предпочитаете не выполнять обновление, запросы, отправленные более ранними версиями, будут по прежнему обслуживаться Azure Monitorной службой до 31 октября 2023 г.

В зависимости от используемого метода конфигурации следует обновить новую версию в запросах и диспетчер ресурсов шаблоны в следующих примерах:


## <a name="rest"></a>REST
Запросы REST API используют версию API в URL-адресе запроса. Замените эту версию последней версией, как показано в следующем примере.

```rest
https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}?api-version=2020-08-01
```

## <a name="azure-resource-manager"></a>Azure Resource Manager
Диспетчер ресурсов Шаблоны используют версию API в свойстве **apiVersion** ресурса. Замените эту версию последней версией, как показано в следующем примере.


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
              "description": "Name of the workspace."
            }
        },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2020-08-01",
            "location": "westus",
            "properties": {
                "sku": {
                    "name": "pergb2018"
                },
                "retentionInDays": 30,
                "features": {
                    "searchVersion": 1,
                    "legacy": 0,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            }
        }
    ]
  }
}
```


## <a name="next-steps"></a>Дальнейшие действия

- См. [Справочник по API OperationalInsights](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/allversions).
