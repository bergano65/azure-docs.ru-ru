---
title: Запрос данных в Azure Time Series Insights Gen2 | Документация Майкрософт
description: Основные понятия о запросах данных и REST API обзор в статье Azure Time Series Insights Gen2.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: seodec18
ms.openlocfilehash: a4b969ecbc92df45021b4a9ec711960171d77d4e
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86495301"
---
# <a name="querying-data-from-azure-time-series-insights-gen2"></a>Запрос данных из Gen2 службы "аналитика временных рядов Azure"

Gen2 "аналитика временных рядов Azure" позволяет выполнять запросы данных о событиях и метаданных, хранящихся в среде, через API-интерфейсы общедоступной поверхности. Эти API-интерфейсы также используются в [обозревателе Gen2 "аналитика временных рядов Azure](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer)".

В Gen2 "аналитика временных рядов Azure" доступны три основные категории API:

* **API среды**. Эти API-интерфейсы позволяют выполнять запросы в самой среде Gen2 временных рядов Azure. Их можно использовать для сбора списка сред, к которым у вызывающего объекта есть доступ, и метаданных среды.
* **API-интерфейсы модели временных рядов (ТСМ-Q)**. обеспечивает выполнение операций создания, чтения, обновления и удаления (CRUD) с метаданными, хранящимися в модели временных рядов среды. Их можно использовать для доступа к экземплярам, типам и иерархиям, а также для их изменения.
* **API-интерфейсы запросов временных рядов (ТСК)**: позволяют получать данные телеметрии или событий, так как они записываются из поставщика источника и позволяют выполнять вычисления и агрегирование данных с помощью расширенных скалярных и агрегатных функций.

Gen2 "аналитика временных рядов Azure" использует язык выражений с расширенными строками, [выражение временных рядов (целевой сервер)](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)для выражения вычислений в [переменных временных рядов](./concepts-variables.md).

## <a name="azure-time-series-insights-gen2-apis-overview"></a>Обзор API-интерфейсов службы "аналитика временных рядов Azure" Gen2

Поддерживаются следующие основные программные интерфейсы.

[![Общие сведения о запросе временных рядов](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Программные интерфейсы среды

* [Получить API окружений](https://docs.microsoft.com/rest/api/time-series-insights/management/environments/get): Возвращает список сред, к которым вызывающему объекту разрешен доступ.
* [Получение API доступности сред](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/getavailability): Возвращает распределение количества событий по метке времени события `$ts` . Этот API помогает определить, есть ли в среде какие-либо события, возвращая количество событий, разбитых на интервалы времени, если таковые существуют.
* [Получение API схемы событий](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/geteventschema): Возвращает метаданные схемы событий для заданного диапазона поиска. Этот API позволяет получить все метаданные и свойства, доступные в схеме для заданной области поиска.

## <a name="time-series-model-query-tsm-q-apis"></a>API запросов модели временных рядов (TSM-Q)

Большинство этих API поддерживают операцию пакетного выполнения для включения операций пакетной обработки команд CRUD в нескольких сущностях модели временных рядов:

* [API параметров модели](https://docs.microsoft.com/rest/api/time-series-insights/preview#model-settings-api): включает *Получение* и *исправление* для типа по умолчанию и имя модели среды.
* [API типов](https://docs.microsoft.com/rest/api/time-series-insights/preview#types-api): включает CRUD на типах временных рядов и связанных с ними переменных.
* [API иерархий](https://docs.microsoft.com/rest/api/time-series-insights/preview#hierarchies-api): включает CRUD в иерархиях временных рядов и связанные с ними пути к полям.
* [API экземпляров](https://docs.microsoft.com/rest/api/time-series-insights/preview#instances-api): включает CRUD на экземплярах временных рядов и связанных с ними полях экземпляров. Кроме того, API экземпляров поддерживает следующие операции:
  * [Поиск](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/search): получает частичный список попаданий в поиск экземпляров временных рядов на основе атрибутов экземпляра.
  * [Предложение](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/suggest). ищет и предлагает частичный список попаданий в поиск экземпляров временных рядов на основе атрибутов экземпляра.

## <a name="time-series-query-tsq-apis"></a>API запросов временных рядов (TSQ)

Эти API-интерфейсы доступны в обоих хранилищах (горячий и холодного) в решении для многоуровневой системы хранения данных. Параметры URL-адреса запроса используются для указания [типа хранилища](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters) , в котором должен выполняться запрос:

* [API получения событий](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents): позволяет выполнять запросы и получать необработанные события и связанные с ними метки времени, так как они записываются в Gen2 временных рядов Azure из поставщика источника. Этот API позволяет получать необработанные события для заданного идентификатора временного ряда и диапазона поиска. Этот API поддерживает разбивку на страницы для получения полного набора данных ответа для выбранного входа. 

  > [!IMPORTANT]
  > * В рамках [предстоящих изменений в правилах обработки и преобразования JSON](https://docs.microsoft.com/azure/time-series-insights/ingestion-rules-update), массивы будут храниться как **динамические** типы. Свойства полезных данных, хранящиеся в этом типе, доступны **только через API получения событий**.

* [Получить API серии](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries): позволяет выполнять запросы и получать вычисляемые значения и связанные метки времени событий, применяя вычисления, определенные переменными для необработанных событий. Эти переменные могут быть определены либо в модели временных рядов, либо в строке запроса. Этот API поддерживает разбивку на страницы для получения полного набора данных ответа для выбранного входа. 

* [API-интерфейс агрегатной серии](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries): включает запросы и получение агрегированных значений и связанных меток времени, применяя вычисления, определенные переменными для необработанных событий. Эти переменные могут быть определены либо в модели временных рядов, либо в строке запроса. Этот API поддерживает разбивку на страницы для получения полного набора данных ответа для выбранного входа. 
  
  Для указанного диапазона поиска и интервала этот API возвращает агрегированный ответ для каждого интервала на переменную для идентификатора временного ряда. Количество интервалов в наборе данных ответа вычисляется тактами эпохи счетчика (число миллисекунд, истекших с момента UNIX — 1 января, 1970) и деление тактов на размер интервала, указанный в запросе.

  Метки времени, возвращаемые в наборе ответов, имеют левые границы интервала, а не события выборки из интервала. 

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о различных переменных, которые могут быть определены в [модели временных рядов](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm).
- Узнайте больше о том, как запрашивать данные из [обозревателя "аналитика временных рядов Azure Gen2](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer)".
