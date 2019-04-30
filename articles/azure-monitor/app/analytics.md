---
title: Analytics — мощный инструмент Azure Application Insights для поиска и создания запросов | Документация Майкрософт
description: 'Обзор аналитики — мощного инструмента поиска Application Insights. '
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/02/2019
ms.author: mbullwin
ms.openlocfilehash: 4c3ecdd01106cc8d305764206bc75535fa4dac3a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60691958"
---
# <a name="analytics-in-application-insights"></a>Аналитика в Application Insights
Analytics — это мощный инструмент [Application Insights](app-insights-overview.md) для поиска и создания запросов. Analytics — это веб-инструмент, поэтому дополнительная настройка не требуется.
Если вы уже настроили Application Insights для одного из приложений, вы можете проанализировать данные приложения, открыв Analytics в [колонке обзора](app-insights-dashboards.md) своего приложения.

![На сайте portal.azure.com откройте ресурс Application Insights и щелкните "Аналитика".](./media/analytics/001.png)

Можно также использовать [площадку для Analytics](https://go.microsoft.com/fwlink/?linkid=859557) — бесплатную демонстрационную среду, содержащую множество примеров данных.
<br>
<br>
> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

## <a name="relation-to-azure-monitor-logs"></a>Отношение к журналам Azure Monitor
Аналитика Application Insights основана на [Azure Data Explorer](/azure/data-explorer), как и журналы Azure Monitor, а также использует [язык запросов Kusto](/azure/kusto/query). Она использует тот же [портал Log Analytics](../log-query/get-started-portal.md), что и журналы Azure Monitor, несмотря на то что ее данные хранятся в отдельном разделе.

Вы не можете получить прямой доступ к данным в рабочей области Log Analytics из аналитики Application Insights, а также не можете получить прямой доступ к данным приложения из Log Analytics. Чтобы запросить оба набора данных вместе, запишите [запрос в Log Analytics](../log-query/log-query-overview.md) и используйте выражение [app ()](../log-query/app-expression.md), чтобы получить доступ к данным приложения.


## <a name="query-data-in-analytics"></a>Запрос данных в Analytics
Типичный запрос содержит имя таблицы и ряд *операторов*, разделенных символом `|`.
Например, давайте узнаем, сколько запросов наше приложение получило из разных стран в течение последних трех часов:
```AIQL
requests
| where timestamp > ago(3h)
| summarize count() by client_CountryOrRegion
| render piechart
```

Мы начнем с выполнения *запросов* имени таблицы и при необходимости добавим последовательность элементов.  Сначала мы определим фильтр времени, чтобы просмотреть записи только за последние три часа.
Затем мы подсчитаем число записей для каждой страны (эти данные находятся в столбце *client_CountryOrRegion*). Наконец, мы визуализируем полученные результаты на круговой диаграмме.
<br>

![Результаты запроса](./media/analytics/030.png)

Язык имеет много привлекательных функций.

* [Фильтрация](/azure/kusto/query/whereoperator) необработанных данных телеметрии приложения по любым полям, включая пользовательские свойства и метрики.
* [Соединение](/azure/kusto/query/joinoperator) нескольких таблиц — соотношение запросов с просмотрами страниц, вызовами зависимостей, исключениями и трассировками журнала.
* Сложные статистические [агрегаты](/azure/kusto/query/summarizeoperator).
* Мгновенные яркие визуализации.
* [REST API](https://dev.applicationinsights.io/), который можно использовать для программного выполнения запросов, например из PowerShell.

[Полный справочник по языку](https://go.microsoft.com/fwlink/?linkid=856079), который описывает все поддерживаемые команды. Он регулярно обновляется.

## <a name="next-steps"></a>Дальнейшие действия
* Начало работы с [порталом Analytics](https://go.microsoft.com/fwlink/?linkid=856587)
* [Написание запросов](https://go.microsoft.com/fwlink/?linkid=856078)
* Просмотр [памятки для пользователей SQL](https://aka.ms/sql-analytics), которая содержит самые распространенные идиомы для преобразования.
* Тестирование Analytics на нашей [площадке](https://analytics.applicationinsights.io/demo), если ваше приложение не отправляет данные в Application Insights.
* Просмотр видео с [вводной информацией](https://applicationanalytics-media.azureedge.net/home_page_video.mp4).
