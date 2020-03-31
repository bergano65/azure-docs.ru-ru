---
title: включить файл
description: включить файл
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 02/07/2020
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: 7bc6938523a6d66a2bc20b37c659568fc5ca494d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77123167"
---
### <a name="general-availability-and-preview-comparison"></a>Сравнение общего доступа и предварительного просмотра

В следующей таблице кратко излагается несколько ключевых различий между общей доступностью Azure Time Series Insights (GA) и экземплярами предварительного просмотра.

| | GA | Preview (Предварительный просмотр) |
| --- | --- | ---|
| Первоклассный гражданин | События-ориентированные | Временно-ориентированные |
| Семантическое рассуждение | Низкий уровень (справочные данные) | Высокого уровня (модели) |
| Контекстуализация данных | Уровень неустройства | Уровень устройства и неустройства |
| Вычислительная логика хранения | нет | Сохранено в переменных типа часть модели |
| Управление хранением и доступом | нет | Включено с помощью модели |
| Агрегаты/выборка | нет | Событие взвешенное и взвешенное время |
| Реконструкция сигнала | нет | Интерполяции |
| Производство производных временных рядов | нет | Да, сливается и присоединяется |
| Гибкость языка | Некомпозитные | Композитные |
| Язык выражений | Предикатная строка | Выражения временных рядов (предикатные строки, значения, выражения и функции) |

### <a name="property-limits"></a>Ограничения свойств

Лимиты свойств Time Series Insights увеличились до 1000 с максимального лимита в 800 в GA. Поставляемые свойства событий имеют соответствующие столбцы JSON, CSV и диаграммы, которые можно просмотреть в [Explorer Time Series Insights.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)

| номер SKU | Максимальные свойства |
| --- | --- |
| Предварительный просмотр PAYG | 1000 свойств (столбцов) |
| ГА С1 | 600 свойств (столбцы) |
| GA S2 | 800 свойств (столбцов) |

### <a name="event-sources"></a>Источники событий

Поддерживается максимум два источника событий в каждом экземпляре. 

* Узнайте, как [добавить источник концентратора событий.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)
* Наверстывайте [источник концентратора IoT.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)

По умолчанию [среды Preview поддерживают скорость входа](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#ingress-scale-and-preview-limitations) до 1 **мегабайт в секунду (Mb/s) за среду.** Клиенты могут при необходимости масштабировать среды предварительного просмотра до **16 МБ/с.** Существует также ограничение на раздел **0,5 МБ/с.** 

### <a name="api-limits"></a>Ограничения API

Ограничения REST API для просмотра Time Series Insights указаны в [справочной документации REST API.](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#limits)
