---
title: Делитесь пользовательскими представлениями с параметрами URL-адресов - Azure Time Series Insights Документы Майкрософт
description: Узнайте, как создавать параметризированные URL-адреса для легкого обмена индивидуальными представлениями исследователей в Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: conceptual
ms.workload: big-data
ms.date: 02/04/2020
ms.custom: seodec18
ms.openlocfilehash: 9dfe499a7d6084a23fd71ab98db472befe71fc04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024370"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Предоставление общего доступа к пользовательскому представлению с помощью параметризованного URL-адреса

Чтобы поделиться пользовательским представлением в Time Series Insights Explorer, можно запрограммно создать параметризированный URL пользовательского представления.

Time Series Insights Explorer поддерживает параметры запроса URL для указания представлений в опыте непосредственно из URL-. Например, используя только URL-адрес, можно указать целевую среду, предикат поиска и нужный период времени. Когда пользователь выбирает настроенный URL, интерфейс предоставляет ссылку непосредственно на этот актив на портале Time Series Insights. Применяются политики доступа к данным.

> [!TIP]
> * Посмотреть бесплатную [демонстрацию Time Series Insights](https://insights.timeseries.azure.com/samples).
> * Ознакомьте сопроводительную документацию [Time Series Insights Explorer.](./time-series-insights-explorer.md)

## <a name="environment-id"></a>Идентификатор среды

Параметр `environmentId=<guid>` указывает идентификатор целевой среды. Это компонент доступа к данным F'DN, и вы можете найти его в правом верхнем углу обзора среды на портале Azure. Это все символы, которые предшествуют `env.timeseries.azure.com`.

Пример параметра идентификатора среды: `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Time

С помощью параметризованного URL-адреса можно указать абсолютное или относительное значение времени.

### <a name="absolute-time-values"></a>Абсолютные значения времени

Для абсолютных значений времени используйте параметры `from=<integer>` и `to=<integer>`.

* `from=<integer>` — это значение начала времени на JavaScript (в миллисекундах) для периода поиска.
* `to=<integer>` — это значение времени окончания на JavaScript (в миллисекундах) для периода поиска.

> [!TIP]
> Чтобы легко перевести даты в Milliseconds JavaScript, попробуйте [Epoch & Unix Timestamp Converter.](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html)

### <a name="relative-time-values"></a>Относительные значения времени

Для относительного значения `relativeMillis=<value>`времени используйте, где *значение* находится в Миллисекундах JavaScript от последней метки времени, полученной от API.

Например, `&relativeMillis=3600000` позволяет отобразить данные за последние 60 минут.

Принятые значения соответствуют меню **быстрого времени** Time Series Insights и включают в себя:

* `1800000`(Последние 30 минут)
* `3600000`(Последние 60 минут)
* `10800000`(Последние 3 часа)
* `21600000`(Последние 6 часов)
* `43200000`(Последние 12 часов)
* `86400000`(Последние 24 часа)
* `604800000`(Последние 7 дней)
* `2592000000`(Последние 30 часов)

### <a name="optional-parameters"></a>Необязательные параметры

Параметр `timeSeriesDefinitions=<collection of term objects>` определяет предикатные термины, которые будут отображаться в представлении Time Series Insights:

| Параметр | URL Пункт | Описание |
| --- | --- | --- |
| **name** | `\<string>` | имя *условия*; |
| **расколBy** | `\<string>` | имя столбца, по которому выполняется *разбиение*; |
| **мераИмя** | `\<string>` | имя столбца *меры*; |
| **Предикат** | `\<string>` | предложение *where* для фильтрации на стороне сервера. |
| **useSum** | `true` | Дополнительный параметр, который определяет использование суммы для вашей меры. |

> [!NOTE]
> Если `Events` выбрана мера **useSum,** то подсчет выбирается по умолчанию.  
> Если `Events` не выбран, среднее выбирается по умолчанию. |

* Пара `multiChartStack=<true/false>` ключей-значение позволяет укладываться в графике.
* Пара `multiChartSameScale=<true/false>` ключей-значение обеспечивает одиниковую шкалу Y-оси в пределах дополнительного параметра.  
* Позволяет `timeBucketUnit=<Unit>&timeBucketSize=<integer>` настроить интервалполние, чтобы обеспечить более детальное или более плавное, более агрегированное представление диаграммы.  
* Параметр `timezoneOffset=<integer>` позволяет установить часовой пояс для диаграммы, который будет рассматриваться в качестве смещения для UTC.

| Пара (ы) | Описание |
| --- | --- |
| `multiChartStack=false` | `true`включен по умолчанию, так что перейдите `false` в стек. |
| `multiChartStack=false&multiChartSameScale=true` | Чтобы использовать одну и ту же шкалу оси Y для разных условий, должно быть включено наложение.  Это `false` по умолчанию, `true` так что передача позволяет эту функциональность. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Единицы `days` `hours`, `minutes` `seconds`, `milliseconds`, .  Единицы всегда следует писать прописными буквами. </br> Определите количество единиц, пройдя желаемую целых для **timeBucketSize.**  |
| `timezoneOffset=-<integer>` | Значение целого числа всегда указывается в миллисекундах. |

> [!NOTE]
> **Значения timeBucketUnit** могут быть сглажены до 7 дней.
> **Значения timezoneOffset** не являются ни UTC, ни по местному времени.

### <a name="examples"></a>Примеры

Чтобы добавить определения временных рядов в среду Time Series Insights в качестве параметра URL- данных, притязай:

```URL parameter
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Используйте пример определения временных рядов для:

* Идентификатор среды
* Последние 60 минут данных
* Условия **(F1PressureID**, **F2TempStation**и **F3VibrationPL),** которые составляют дополнительные параметры

Можно создать следующий параметризированный URL для представления:

```URL
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

[![Time Series Исследования исследователь параметризированный URL](media/parameterized-url/share-parameterized-url.png)](media/parameterized-url/share-parameterized-url.png#lightbox)

> [!TIP]
> Смотрите Explorer в прямом [эфире, используя](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]) пример URL выше.

URL выше описывает и отображает параметризированный вид Explorer Time Series Insights. 

* Параметрыстизированные предикаты.

  [![Исследователь Тайм Серии Исследования параметризированных предикатов.](media/parameterized-url/share-parameterized-url-predicates.png)](media/parameterized-url/share-parameterized-url-predicates.png#lightbox)

* Общий полный вид диаграммы.

  [![Общий полный вид диаграммы.](media/parameterized-url/share-parameterized-url-full-chart.png)](media/parameterized-url/share-parameterized-url-full-chart.png#lightbox)

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как [заставить данные зазапрос с помощью C .](time-series-insights-query-data-csharp.md)

* Узнайте больше о [Исследователе исследования временных рядов.](./time-series-insights-explorer.md)
