---
title: Полезные ресурсы при работе с Azure Sentinel Документы Майкрософт
description: Этот документ предоставляет список полезных ресурсов при работе с Azure Sentinel.
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
ms.date: 12/02/2019
ms.author: yelevin
ms.openlocfilehash: 2110d3319cebf693ef06deec26a29fa655e35035
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585277"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Полезные ресурсы для работы с Azure Sentinel



В этой статье перечислены ресурсы, которые помогут вам получить больше информации о работе с Azure Sentinel.

Разъемы приложений Azure Logic Apps:<https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>Аудит и создание отчетов
Аудиторские журналы Azure Sentinel хранятся в [журналах деятельности Azure.](../azure-monitor/platform/platform-logs-overview.md)

Следующие поддерживаемые операции могут быть проверены.

|Имя операции|    Тип ресурса|
|----|----|
|Создание или обновление трудовой книжки  |Microsoft.Insights/рабочие книги|
|Удалить трудовую книжку    |Microsoft.Insights/рабочие книги|
|Набор рабочего процесса   |Microsoft.Logic/workflows|
|Удаление рабочего потока    |Microsoft.Logic/workflows|
|Создание сохраненного поиска    |Microsoft.OperationalInsights/workspaces/savedSearches|
|Удаление сохраненного поиска    |Microsoft.OperationalInsights/workspaces/savedSearches|
|Правила обновления оповещения |Microsoft.SecurityInsights/alertRules|
|Правила оповещения удалить |Microsoft.SecurityInsights/alertRules|
|Обновление действий реагирования на правила оповещения |Microsoft.SecurityInsights/alertRules/actions|
|Удаление действий реагирования на правила оповещения |Microsoft.SecurityInsights/alertRules/actions|
|Обновление закладок   |Microsoft.SecurityInsights/bookmarks|
|Удаление закладок   |Microsoft.SecurityInsights/bookmarks|
|Случаи обновления   |Microsoft.SecurityInsights/Cases|
|Обновление Дело Расследование  |Microsoft.SecurityInsights/Случаи/расследования|
|Создание комментариев к случаям   |Microsoft.SecurityInsights/Cases/comments|
|Обновление разъемов данных |Microsoft.SecurityInsights/dataConnectors|
|Удаление разъемов данных |Microsoft.SecurityInsights/dataConnectors|
|Обновить параметры    |Microsoft.SecurityInsights/настройки|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>Просмотр данных аудита и отчетности в Azure Sentinel

Вы можете просмотреть эти данные, переместив их из журнала Azure Activity в Azure Sentinel, где можно провести исследования и аналитику.

1. Подключите источник действий [Azure.](connect-azure-activity.md) После этого события аудита передаются в новую таблицу на экране **журналов под** названием AzureActivity.
2. Затем задайте запрос данных с помощью КЗЛ, как и любую другую таблицу.



## <a name="vendor-documentation"></a>Документация поставщика

| **Поставщика**  | **Инцидент использования в Azure Sentinel** | **Ссылку**|
|----|----|----|
| GitHub| Используется для доступа к странице Сообщества| <https://github.com/Azure/Azure-Sentinel> |
| Пало-Альто| Настройка CEF| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| PluralSight | Курс по языку запросов Kusto| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>Блоги и форумы

Опубликуйте свои вопросы в [пространстве TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) для Azure Sentinel.

Посмотреть сообщения в блогах Azure Sentinel от [TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) и [Microsoft Azure.](https://azure.microsoft.com/blog/tag/azure-sentinel/)


## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы получили список ресурсов, которые полезны при работе с Azure Sentinel. Дополнительную информацию о безопасности и соответствии требованиям Azure вы найдете в [блоге Microsoft Azure Security and Compliance.](https://blogs.msdn.com/b/azuresecurity/)
