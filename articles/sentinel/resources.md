---
title: Полезные ресурсы при работе с Azure Sentinel | Документация Майкрософт
description: В этом документе представлен список полезных ресурсов при работе с Sentinel Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/27/2021
ms.author: yelevin
ms.openlocfilehash: 11c5c77623fcc693210d5a42bf94c968e884fec3
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920026"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Полезные ресурсы для работы с Azure Sentinel



В этой статье перечислены ресурсы, которые могут помочь получить дополнительные сведения о работе с Sentinel.

- **Соединители Azure Logic Apps**: <https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>Аудит и создание отчетов
Журналы аудита для Azure Sentinel хранятся в [журналах действий Azure](../azure-monitor/platform/platform-logs-overview.md).

Можно проводить аудит следующих поддерживаемых операций.

|Имя операции|    Тип ресурса|
|----|----|
|Создать или обновить книгу  |Microsoft. Insights/книги|
|Удалить книгу    |Microsoft. Insights/книги|
|Задание рабочего процесса   |Microsoft.Logic/workflows|
|Удалить рабочий процесс    |Microsoft.Logic/workflows|
|Создать сохраненный поиск    |Microsoft. OperationalInsights/workspaces/Саведсеарчес|
|Удалить сохраненный поиск    |Microsoft. OperationalInsights/workspaces/Саведсеарчес|
|Обновление правил генерации оповещений |Microsoft. Секуритинсигхтс/alertRules|
|Удаление правил генерации оповещений |Microsoft. Секуритинсигхтс/alertRules|
|Обновление действий ответа для правила генерации оповещений |Microsoft. Секуритинсигхтс/alertRules/действия|
|Удаление действий ответа на правило генерации оповещений |Microsoft. Секуритинсигхтс/alertRules/действия|
|Обновить закладки   |Microsoft. Секуритинсигхтс/закладки|
|Удалить закладки   |Microsoft. Секуритинсигхтс/закладки|
|Обновление вариантов   |Microsoft. Секуритинсигхтс/cases|
|Исследование вариантов обновления  |Microsoft. Секуритинсигхтс/cases/расследования|
|Создание комментариев к вариантам   |Microsoft. Секуритинсигхтс/cases/Comments|
|Обновление соединителей данных |Microsoft. Секуритинсигхтс/Connects|
|Удаление соединителей данных |Microsoft. Секуритинсигхтс/Connects|
|Обновить параметры    |Microsoft. Секуритинсигхтс/параметры|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>Просмотр данных аудита и отчетов в Azure Sentinel

Вы можете просмотреть эти данные, перепотокая их из журнала действий Azure в Azure Sentinel, где вы сможете выполнять исследование и анализ.

1. Подключите источник данных [действий Azure](connect-azure-activity.md) . После этого события аудита передаются в виде потока в новую таблицу на экране **журналов** с именем AzureActivity.

1. Затем запросите данные с помощью ККЛ, как в любой другой таблице.

    Например, чтобы узнать, кто был последним пользователем для изменения определенного правила аналитики, используйте следующий запрос (заменив `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` идентификатором правила, которое требуется проверить):

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```



## <a name="vendor-documentation"></a>Документация поставщика

| **поставщик**  | **Использование инцидента в Azure Sentinel** | **Ссылка**|
|----|----|----|
| GitHub| Используется для доступа к странице сообщества| <https://github.com/Azure/Azure-Sentinel> |
| палоалто| Настройка CEF| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| PluralSight | Курс по языку запросов Kusto| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>Блоги и форумы

Мы любим слышать наших пользователей!

- **Опубликуйте свои вопросы** в [Течкоммунити пространстве](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) для Azure Sentinel. 

- **Отправьте предложения для улучшения** с помощью нашей [пользовательской](https://feedback.azure.com/forums/920458-azure-sentinel) программы.

- **Просмотр и комментирование** в записях блога Azure Sentinel:

    - [течкоммунити](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) 
    - [Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/)

Дополнительные сведения о безопасности и соответствии требованиям Azure см. в [блоге Microsoft Azure безопасность и соответствие требованиям](https://techcommunity.microsoft.com/t5/microsoft-security-and/bg-p/MicrosoftSecurityandCompliance).


## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Получите сертификацию!](/learn/paths/security-ops-sentinel/)

> [!div class="nextstepaction"]
> [Чтение истории вариантов использования клиента](https://customers.microsoft.com/en-us/search?sq=%22Azure%20Sentinel%20%22&ff=&p=0&so=story_publish_date%20desc)

