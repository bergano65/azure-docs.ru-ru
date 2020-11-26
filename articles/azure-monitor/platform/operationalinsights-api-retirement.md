---
title: Прекращение использования API Azure Monitor
description: Описание прекращения использования старых версий API поставщика ресурсов OperationalInsights.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/29/2020
ms.openlocfilehash: e2b12d7a2206ab369328563af438c6ef1ea39327
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184116"
---
# <a name="operationalinsights-api-version-retirement"></a>Прекращение использования версии API OperationalInsights
Корпорация Майкрософт предоставляет уведомление не менее чем за 12 месяцев до прекращения использования API для сглаживания перехода до более новой или поддерживаемой версии. Мы выпустили новую версию (2020-08-01) для API поставщика ресурсов **OperationalInsights** и выпустим более ранние версии API-29 февраля 2024.

Мы рекомендуем начать использовать версию 2020-08-01, чтобы получить преимущества новых функций, таких как [выделенный кластер](../log-query/logs-dedicated-clusters.md), [управляемые клиентом ключи](./customer-managed-keys.md), [Частная связь](./private-link-security.md) и [Экспорт данных](./logs-data-export.md). Кроме того, новые функции и функции и оптимизации добавляются только к текущему API.

После 29 февраля 2024 Azure Monitor больше не будет поддерживать более ранние версии API, чем 2020-08-01. Если вы предпочитаете не выполнять обновление, запросы, отправленные из более ранних версий, продолжат обслуживаться службой Azure Monitor до 29 февраля 2024.

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

- См. [Справочник по API рабочей области OperationalInsights](/rest/api/loganalytics/workspaces).