---
title: Прекращение использования API Azure Monitor
description: Описание прекращения использования старых версий API поставщика ресурсов OperationalInsights.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/02/2020
ms.openlocfilehash: cf48e26133326d43754b38df6f3b2caaf7a587ab
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978094"
---
# <a name="operationalinsights-api-version-retirement"></a>Прекращение использования версии API OperationalInsights
Корпорация Майкрософт предоставляет уведомление не менее чем за 12 месяцев до прекращения использования API для сглаживания перехода до более новой или поддерживаемой версии. Мы выпустили новую версию (2020-08-01) для API поставщика ресурсов **OperationalInsights** и запустим более ранние версии API до 31 октября 2023 г. Поскольку новые функции и функции и оптимизации добавляются только к текущему API, следует как можно раньше выполнить обновление до последней версии API.

После 31 октября 2023 Azure Monitor больше не будет поддерживать более ранние версии API, чем 2020-08-01. Если вы предпочитаете не выполнять обновление, запросы, отправленные более ранними версиями, будут по прежнему обслуживаться Azure Monitorной службой до 31 октября 2023 г.

## <a name="migration-steps"></a>Этапы миграции
В зависимости от используемого метода конфигурации следует обновить новую версию в запросах **RESTful** и **Диспетчер ресурсов шаблонах**. Чтобы обновить версию API, выполните приведенные ниже примеры.

1. Запросы REST API используют версию API в URL-адресе запроса. Замените эту версию последней версией (2020-08-01), как показано в следующем примере.

    ```rest
    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}?api-version=2020-08-01
    ```

2. Azure Resource Manager шаблоны используют версию API в свойстве **apiVersion** ресурса. Замените эту версию последней версией (2020-08-01), как показано в следующем примере.


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
