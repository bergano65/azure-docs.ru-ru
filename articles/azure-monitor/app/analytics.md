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
ms.date: 02/08/2018
ms.author: mbullwin
ms.openlocfilehash: 24a911978d6c9f9a33c81254a5e2f5951029ff3a
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54120892"
---
# <a name="analytics-in-application-insights"></a>Аналитика в Application Insights
Analytics — это мощный инструмент [Application Insights](../../azure-monitor/app/app-insights-overview.md) для поиска и создания запросов. Analytics — это веб-инструмент, поэтому дополнительная настройка не требуется. Если вы уже настроили Application Insights для одного из приложений, вы можете проанализировать данные приложения, открыв Analytics в [колонке обзора](../../azure-monitor/app/app-insights-dashboards.md) своего приложения.

![На сайте portal.azure.com откройте ресурс Application Insights и щелкните "Аналитика".](./media/analytics/001.png)

Можно также использовать [площадку для Analytics](https://go.microsoft.com/fwlink/?linkid=859557) — бесплатную демонстрационную среду, содержащую множество примеров данных.
<br>
<br>
> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

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

## <a name="next-steps"></a>Дополнительная информация
* Начало работы с [порталом Analytics](https://go.microsoft.com/fwlink/?linkid=856587)
* [Написание запросов](https://go.microsoft.com/fwlink/?linkid=856078)
* Просмотр [памятки для пользователей SQL](https://aka.ms/sql-analytics), которая содержит самые распространенные идиомы для преобразования.
* Тестирование Analytics на нашей [площадке](https://analytics.applicationinsights.io/demo), если ваше приложение не отправляет данные в Application Insights.
* Просмотр видео с [вводной информацией](https://applicationanalytics-media.azureedge.net/home_page_video.mp4).
