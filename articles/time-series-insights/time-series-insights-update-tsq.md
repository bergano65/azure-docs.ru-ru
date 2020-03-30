---
title: Запрос данных в предварительный просмотр - Исследования серии времени Azure Документы Майкрософт
description: Концепции запроса данных и обзор HTTP REST API в Azure Time Series Insights Preview.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/25/2020
ms.custom: seodec18
ms.openlocfilehash: 23094ec71dac5780def10e16b90de0b818ef3c68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284897"
---
# <a name="data-querying-in-azure-time-series-insights-preview"></a>Запрос данных в Azure Time Series Обзор

Azure Time Series Insights позволяет задать запрос данных о событиях и метаданных, хранящихся в среде через общедоступные AIS. Эти AA также используются [Time Series Insights Explorer.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer)

Доступны три основные категории API в службе "Аналитика временных рядов":

* **AIS среды**: Эти AAP позволяют запрашивать запросы в самой среде Time Series Insights. Они могут быть использованы для сбора списка сред, к которой абонент имеет доступ, и метаданных среды.
* **AI-класса модели серии времени (TSM-q)**: Включает в себя создание, чтение, обновление и удаление (CRUD) операций на метаданных, хранящихся в модели временных рядов среды. Они могут использоваться для доступа к экземплярам, типам и иерархиям и их отсвазанию.
* **AAИ time Series Query (TS':** Позволяет получить данные телеметрии или событий в том виде, в каком они записаны у поставщика-источника, и позволяют выполнять вычисления и агрегации данных с использованием расширенных функций scalar и агрегатных.

Time Series Insights использует для выражения вычислений богатый язык выражения на основе [строки, Выражение временных рядов (TSX).](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

## <a name="azure-time-series-insights-core-apis"></a>ApIs,cinaina Azure Time Series Insights

Поддерживаются следующие основные программные интерфейсы.

[![Обзор запросов временных рядов](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Программные интерфейсы среды

* [Получите API среды](https://docs.microsoft.com/rest/api/time-series-insights/management/environments/get): Возвращает список сред, к которым абонент имеет право получить доступ.
* [Получите API доступности среды](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability): Возвращает распределение количества `$ts`событий по метке времени события. Этот API помогает определить, есть ли какие-либо события в среде, вернув количество событий, разбитых на промежутки времени, если таковые имеются.
* [Получите API schema:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)Возвращает метаданные схемы событий для заданного диапазона поиска. Этот API позволяет получить все метаданные и свойства, доступные в схеме для заданной области поиска.

## <a name="time-series-model-query-tsm-q-apis"></a>API запросов модели временных рядов (TSM-Q)

Большинство из этих AA поддерживают операцию пакетного выполнения для включения пакетных операций CRUD на нескольких объектах модели Time Series:

* [Параметры моделей API:](https://docs.microsoft.com/rest/api/time-series-insights/preview#model-settings-api)Включает *GET* и *PATCH* по типу по умолчанию и названию среды модели.
* [Типы API](https://docs.microsoft.com/rest/api/time-series-insights/preview#types-api): Включает CRUD на типах временных рядов и связанных с ними переменных.
* [Иерархии API](https://docs.microsoft.com/rest/api/time-series-insights/preview#hierarchies-api): Включает CRUD в иерархиях временных рядов и связанных с ними полевых путей.
* [Экземпляры API:](https://docs.microsoft.com/rest/api/time-series-insights/preview#instances-api)Включает CRUD на экземплярах временных рядов и связанных с ними полях экземпляров. Кроме того, API Instances поддерживает следующие операции:
  * [Поиск:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search)Извлекает частичный список хитов в поисках экземпляров временных рядов на основе атрибутов экземпляра.
  * [Предложите](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest): Поиск и предлагает частичный список хитов в поисках экземпляров временных рядов на основе атрибутов экземпляра.

## <a name="time-series-query-tsq-apis"></a>API запросов временных рядов (TSQ)

Эти AA доступны во всех магазинах в нашем многослойном решении для хранения данных в Time Series Insights. Параметры URL-адреса запроса используются для указания [типа хранилища,](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters) который должен выполняться запросом:

* [Получение API событий](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents): Включает запрос и поиск необработанных событий и связанных с ними меток времени, поскольку они записаны в Time Series Insights от поставщика-источника. Этот API позволяет получать исходные события для заданного идентификатора Time Series и диапазона поиска. Этот API поддерживает pagination для получения полного набора данных отклика для выбранного ввода. 

* [Получите API серии:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries)Включает запрос и поиск вычисляемых значений и связанных с ними меток времени событий, применяя расчеты, определяемые переменными на необработанных событиях. Эти переменные могут быть определены в модели временных рядов или приведены в строке запроса. Этот API поддерживает pagination для получения полного набора данных отклика для выбранного ввода. 

* [Агрегированный API серии:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)Включает запрос и поиск агрегированных значений и связанных с ними временных меток, применяя расчеты, определяемые переменными на необработанных событиях. Эти переменные могут быть определены в модели временных рядов или приведены в строке запроса. Этот API поддерживает pagination для получения полного набора данных отклика для выбранного ввода. 
  
  Для заданного диапазона поиска и интервала этот API возвращает агрегированный ответ на переменную за интервал для идентификатора Time Series. Количество интервалов в наборе данных ответов рассчитывается путем подсчета теков эпохи (количество миллисекунд, прошедшее со времен эпохи Unix - 1 января 1970 года) и деления тиков на размер интервала, указанный в запросе.

  Метки времени, возвращенные в наборе ответов, относятся к левым интервалным границам, а не к отобранным событиям из интервала. 

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о различных переменных, которые могут быть определены в [модели временных рядов](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm).
- Узнайте больше о том, как загонять данные из [Time Series Insights Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer).
