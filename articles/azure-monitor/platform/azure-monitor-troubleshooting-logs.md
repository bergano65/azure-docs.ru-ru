---
title: Журналы устранения неполадок Azure Monitor (Предварительная версия)
description: Используйте Azure Monitor для быстрого или регулярного изучения проблем, устранения проблем с кодом или конфигурацией или устранения обращений в службу поддержки, которые часто зависят от поиска по большому объему данных для получения конкретных ценных сведений.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/29/2020
ms.openlocfilehash: b3d1ad2d327da60874d2d07ba697b8f5ab815189
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127096"
---
# <a name="azure-monitor-troubleshooting-logs-preview"></a>Журналы устранения неполадок Azure Monitor (Предварительная версия)
Используйте Azure Monitor, чтобы быстро и/или периодически исследовать проблемы, устранять неполадки с кодом или конфигурацией или устранять обращения в службу поддержки, которые часто зависят от поиска большого объема данных для получения конкретных ценных сведений.

>[!NOTE]
> * Журналы устранения неполадок находятся в режиме предварительного просмотра.
>* Обратитесь к [группе log Analytics](mailto:orens@microsoft.com) с любыми вопросами или примените эту функцию.
## <a name="troubleshoot-and-query-your-code-or-configuration-issues"></a>Устранение неполадок и выполнение запросов к коду или конфигурации
Используйте Azure Monitor журналы устранения неполадок для выборки записей и изучения проблем и проблем в более простом и дешевом способах использования ККЛ.
В журналах устранения неполадок декрисся плата за использование основных возможностей для устранения неполадок.

> [!NOTE]
>* Решение о режиме устранения неполадок можно настроить.
>* Журналы устранения неполадок могут применяться к конкретным таблицам, в настоящее время в таблицах "журналы контейнеров" и "трассировки приложения".
>* Срок хранения в 4 дня можно продлить за счет дополнительных затрат.
>* По умолчанию таблицы наследуют хранение рабочей области. Чтобы избежать дополнительной оплаты, рекомендуется изменить срок хранения таблиц. [Щелкните здесь, чтобы узнать, как изменить хранение таблицы](https://docs.microsoft.com//azure/azure-monitor/platform/manage-cost-storage).

## <a name="turn-on-troubleshooting-logs-on-your-tables"></a>Включение журналов устранения неполадок в таблицах

Чтобы включить журналы устранения неполадок в рабочей области, необходимо использовать следующий вызов API.
```http
PUT https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

(With body in the form of a GET single table request response)

Response:

{
        "properties": {
          "retentionInDays": 40,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": true
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": "{tableName}"
}
```
## <a name="check-if-the-troubleshooting-logs-feature-is-enabled-for-a-given-table"></a>Проверьте, включена ли функция журналов устранения неполадок для заданной таблицы.
Чтобы проверить, включен ли журнал устранения неполадок для данной таблицы, можно использовать следующий вызов API.

```http
GET https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

Response: 
"properties": {
          "retentionInDays": 30,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": true,
          "lastTroubleshootDate": "Thu, 19 Nov 2020 07:40:51 GMT"
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": " {tableName}"
                }

```
## <a name="check-if-the-troubleshooting-logs-feature-is-enabled-for-all-of-the-tables-in-a-workspace"></a>Проверьте, включена ли функция "журналы устранения неполадок" для всех таблиц в рабочей области.
Чтобы проверить, в каких таблицах включен журнал устранения неполадок, можно использовать следующий вызов API.

```http
GET "https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables"

Response: 
{
          "properties": {
            "retentionInDays": 30,
            "isTroubleshootingAllowed": true,
            "isTroubleshootEnabled": true,
            "lastTroubleshootDate": "Thu, 19 Nov 2020 07:40:51 GMT"
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table1",
          "name": "table1"
 },
        {
          "properties": {
            "retentionInDays": 7,
            "isTroubleshootingAllowed": true
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table2",
          "name": "table2"
        },
        {
          "properties": {
            "retentionInDays": 7,
            "isTroubleshootingAllowed": false
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table3",
          "name": "table3"
        }
```
## <a name="turn-off-troubleshooting-logs-on-your-tables"></a>Отключите журналы устранения неполадок в таблицах

Чтобы отключить журналы устранения неполадок в рабочей области, необходимо использовать следующий вызов API.
```http
PUT https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

(With body in the form of a GET single table request response)

Response:

{
        "properties": {
          "retentionInDays": 40,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": false
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": "{tableName}"
}
```
>[!TIP]
>* Для выполнения команд можно использовать любое REST API средство. [Подробнее](https://docs.microsoft.com/rest/api/azure/)
>* Для проверки подлинности необходимо использовать токен носителя. [Подробнее](https://social.technet.microsoft.com/wiki/contents/articles/51140.azure-rest-management-api-the-quickest-way-to-get-your-bearer-token.aspx)

>[!NOTE]
>* Флаг "Истраублешутингалловед" — указывает, разрешена ли таблица в службе.
>* "Истраублешутенаблед" указывает, включена ли функция для таблицы — может быть переключена или отключена (true или false)
>* При отключении флага "Истраублешутенаблед" для определенной таблицы включение повторного включения возможно только через одну неделю после предыдущей даты включения.
>* В настоящее время эта возможность поддерживается только для таблиц в (некоторые другие номера SKU также будут поддерживаться в будущем). [Дополнительные сведения о ценах](https://docs.microsoft.com/services-hub/health/azure_pricing)см. в этой статье.

## <a name="query-limitations-for-troubleshooting"></a>Ограничения запросов для устранения неполадок
Существует несколько ограничений для таблицы, помеченной как «журналы устранения неполадок»:
*   Будет иметь меньше ресурсов обработки и, следовательно, не будет подойти для больших панелей мониторинга, сложной аналитики или многих одновременных вызовов API.
*   Количество запросов ограничено интервалом в два дня.
* Очистка не будет работать — [Дополнительные сведения об очистке](https://docs.microsoft.com/rest/api/loganalytics/workspacepurge/purge).
* В этой службе предупреждения не поддерживаются.
## <a name="next-steps"></a>Дальнейшие действия
* [Написание запросов](https://docs.microsoft.com/azure/data-explorer/write-queries)


