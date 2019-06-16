---
title: Предоставление общего доступа к пользовательским представлениям службы "Аналитика временных рядов Azure" с помощью параметризованных URL-адресов | Документация Майкрософт
description: В этой статье объясняется, как создавать параметризованные URL-адреса в службе "Аналитика временных рядов Azure", чтобы с легкостью представлять общий доступ к пользовательскому представлению.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.topic: conceptual
ms.workload: big-data
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: dfc04397b1d7e9f3256810cbe469067ae52c99bd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66238975"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Предоставление общего доступа к пользовательскому представлению с помощью параметризованного URL-адреса

Чтобы предоставить доступ к пользовательскому представлению в обозревателе Time Series Insights, возможность программно создавать параметризованные URL-адрес этого представления.

Обозревателе Time Series Insights поддерживает параметры запроса URL-адреса для указания представлений в интерфейсе непосредственно из URL-адрес. Например, используя только URL-адрес, можно указать целевую среду, предикат поиска и нужный период времени. Когда пользователь щелкает настраиваемый URL-адрес, в интерфейсе отображается ссылка непосредственно на конкретный ресурс на портале "Аналитика временных рядов". Применяются политики доступа к данным.

> [!TIP]
> * Просмотреть бесплатный [Демонстрация Time Series Insights](https://insights.timeseries.azure.com/samples).
> * Чтение сопутствующий [обозревателе Time Series Insights](./time-series-insights-explorer.md) документации.

## <a name="environment-id"></a>Идентификатор среды

Параметр `environmentId=<guid>` указывает идентификатор целевой среды. Он входит в состав полного доменного ИМЕНИ для доступа к данным и его можно найти в правом верхнем углу сведений о среде, на портале Azure. Это все символы, которые предшествуют `env.timeseries.azure.com`.

Пример параметра идентификатора среды: `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Время

С помощью параметризованного URL-адреса можно указать абсолютное или относительное значение времени.

### <a name="absolute-time-values"></a>Абсолютные значения времени

Для абсолютных значений времени используйте параметры `from=<integer>` и `to=<integer>`.

* `from=<integer>` — это значение начала времени на JavaScript (в миллисекундах) для периода поиска.
* `to=<integer>` — это значение времени окончания на JavaScript (в миллисекундах) для периода поиска.

Сведения о том, как указать миллисекунды для даты на JavaScript, см. на странице об [Epoch & Unix Timestamp Converter](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Относительные значения времени

Для относительных значений времени используйте `relativeMillis=<value>`, где *value* указывается в миллисекундах на JavaScript на основе последних данных в серверной части.

Например, `&relativeMillis=3600000` позволяет отобразить данные за последние 60 минут.

Допустимые значения соответствуют значениям в обозревателе Time Series Insights **время быстрого** меню и включают:

* `1800000` (За последние 30 мин)
* `3600000` (За последние 60 мин.)
* `10800000` (Последние 3 часа)
* `21600000` (За последние 6 часов)
* `43200000` (Последние 12 часов)
* `86400000` (Последние 24 часа)
* `604800000` (Последние 7 дней)
* `2592000000` (Последние 30 часов)

### <a name="optional-parameters"></a>Необязательные параметры

`timeSeriesDefinitions=<collection of term objects>` Параметр указывает условия представления аналитики временных рядов:

| Параметр | URL-адрес элемента | Описание |
| --- | --- | --- |
| **name** | `\<string>` | имя *условия*; |
| **splitBy** | `\<string>` | имя столбца, по которому выполняется *разбиение*; |
| **measureName** | `\<string>` | имя столбца *меры*; |
| **предикат** | `\<string>` | предложение *where* для фильтрации на стороне сервера. |
| **useSum** | `true` | Необязательный параметр, который определяет использование функции суммирования для меры. </br>  Обратите внимание, если `Events` выбрана мера, число выбрано по умолчанию.  </br>  Если `Events` является не выбран, по умолчанию выбирается среднее значение. |

* `multiChartStack=<true/false>` Пары "ключ значение" активирует наложение в диаграмме.
* `multiChartSameScale=<true/false>` Пары "ключ значение" обеспечивает ту же шкалу оси y для условия, заключенные в необязательный параметр.  
* `timeBucketUnit=<Unit>&timeBucketSize=<integer>` Дает возможность настроить ползунок интервала, чтобы обеспечить более детальное или более гладким, более объединенное представление диаграммы.  
* `timezoneOffset=<integer>` Параметр позволяет задать часовой пояс для диаграммы для просмотра в виде смещения в формат UTC.

| Пары | Описание |
| --- | --- |
| `multiChartStack=false` | `true` включено по умолчанию таким образом передать `false` стека. |
| `multiChartStack=false&multiChartSameScale=true` | Чтобы использовать одну и ту же шкалу оси Y для разных условий, должно быть включено наложение.  Он имеет `false` по умолчанию, поэтому для передачи 'true' активирует эту функцию. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Единицы — дни, часы, минуты, секунды, миллисекунды.  Единицы всегда следует писать прописными буквами. </br> Определите число единиц, передав требуемое целое число для timeBucketSize.  Обратите внимание, что можно сократить представление до 7 дней.  |
| `timezoneOffset=-<integer>` | Значение целого числа всегда указывается в миллисекундах. </br> Обратите внимание, что эта функциональность немного отличается от того, что включено в обозревателе Time Series Insights, где мы можем выбрать локальное (время браузера) или в формате UTC. |

### <a name="examples"></a>Примеры

Чтобы добавить определения временных рядов в среде Time Series Insights, как параметр URL-адрес, добавьте следующую команду:

```plaintext
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Используйте примеры определений временных рядов для:

* Идентификатор среды
* Последние 60 минут данных
* Условий (F1PressureID, F2TempStation и F3VibrationPL), представляющих необязательные параметры.

Вы можете создать следующий параметризованный URL-адрес для представления.

```plaintext
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

> [!TIP]
> Просмотреть Explorer live [с помощью URL-адрес](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]).

URL-адрес выше описывает и создает представление обозревателе Time Series Insights:

[![Условия в обозревателе время Series Insights](media/parameterized-url/url1.png)](media/parameterized-url/url1.png#lightbox)

Полное представление (включая диаграмму):

[![Представление диаграммы](media/parameterized-url/url2.png)](media/parameterized-url/url2.png#lightbox)

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как [запрос данных с помощью C# ](time-series-insights-query-data-csharp.md).

* Дополнительные сведения о [Time Series Insights Explorer](./time-series-insights-explorer.md).
