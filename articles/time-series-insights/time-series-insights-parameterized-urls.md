---
title: Предоставление общего доступа к пользовательским представлениям службы "Аналитика временных рядов Azure" с помощью параметризованных URL-адресов | Документация Майкрософт
description: В этой статье объясняется, как создавать параметризованные URL-адреса в службе "Аналитика временных рядов Azure", чтобы с легкостью представлять общий доступ к пользовательскому представлению.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/03/2019
ms.custom: seodec18
ms.openlocfilehash: cad57e3e7e52ec291819110bab9d8d79f51e5a2f
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71958155"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Предоставление общего доступа к пользовательскому представлению с помощью параметризованного URL-адреса

Чтобы предоставить общий доступ к пользовательскому представлению в обозревателе "аналитика временных рядов", можно программно создать параметризованный URL-адрес пользовательского представления.

Обозреватель "аналитика временных рядов" поддерживает параметры запроса URL-адресов для указания представлений в интерфейсе непосредственно из URL-адреса. Например, используя только URL-адрес, можно указать целевую среду, предикат поиска и нужный период времени. Когда пользователь выбирает настраиваемый URL-адрес, интерфейс предоставляет ссылку непосредственно на этот ресурс на портале временных рядов Insights. Применяются политики доступа к данным.

> [!TIP]
> * Просмотрите [демонстрацию бесплатной временной серии Insights](https://insights.timeseries.azure.com/samples).
> * Ознакомьтесь с документацией по [обозревателю "аналитика временных рядов](./time-series-insights-explorer.md) ".

## <a name="environment-id"></a>Идентификатор среды

Параметр `environmentId=<guid>` указывает идентификатор целевой среды. Это компонент полного доменного имени для доступа к данным, и его можно найти в правом верхнем углу окна "Общие сведения о среде" в портал Azure. Это все символы, которые предшествуют `env.timeseries.azure.com`.

Пример параметра идентификатора среды: `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Time

С помощью параметризованного URL-адреса можно указать абсолютное или относительное значение времени.

### <a name="absolute-time-values"></a>Абсолютные значения времени

Для абсолютных значений времени используйте параметры `from=<integer>` и `to=<integer>`.

* `from=<integer>` — это значение начала времени на JavaScript (в миллисекундах) для периода поиска.
* `to=<integer>` — это значение времени окончания на JavaScript (в миллисекундах) для периода поиска.

Сведения о том, как указать миллисекунды для даты на JavaScript, см. на странице об [Epoch & Unix Timestamp Converter](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Относительные значения времени

Для относительных значений времени используйте `relativeMillis=<value>`, где *value* указывается в миллисекундах на JavaScript на основе последних данных в серверной части.

Например, `&relativeMillis=3600000` позволяет отобразить данные за последние 60 минут.

Допустимые значения соответствуют меню **быстрого времени** обозревателя "аналитика временных рядов" и включают:

* `1800000` (последние 30 минут)
* `3600000` (последние 60 мин)
* `10800000` (последние 3 часа)
* `21600000` (последние 6 часов)
* `43200000` (последние 12 часов)
* `86400000` (последние 24 часа)
* `604800000` (за последние 7 дней)
* `2592000000` (последние 30 часов)

### <a name="optional-parameters"></a>Необязательные параметры

Параметр `timeSeriesDefinitions=<collection of term objects>` указывает условия представления "аналитика временных рядов":

| Параметр | Элемент URL-адреса | Описание |
| --- | --- | --- |
| **name** | `\<string>` | имя *условия*; |
| **сплитби** | `\<string>` | имя столбца, по которому выполняется *разбиение*; |
| **measureName** | `\<string>` | имя столбца *меры*; |
| **предикат** | `\<string>` | предложение *where* для фильтрации на стороне сервера. |
| **усесум** | `true` | Необязательный параметр, который задает использование функции Sum для меры. </br>  Обратите внимание, что если выбрана мера `Events`, то счетчик будет выбран по умолчанию.  </br>  Если `Events` не выбран, по умолчанию выбирается Average. |

* Пара "ключ-значение" `multiChartStack=<true/false>` позволяет включать в диаграмму стек.
* Пара "ключ — значение" `multiChartSameScale=<true/false>` обеспечивает одинаковое масштабирование по оси Y в пределах необязательного параметра.  
* @No__t-0 позволяет настроить ползунок интервала, чтобы обеспечить более детализированное или более гладкое, более обобщенное представление диаграммы.  
* Параметр `timezoneOffset=<integer>` позволяет задать часовой пояс для просматриваемой диаграммы в качестве смещения в формате UTC.

| Пары | Описание |
| --- | --- |
| `multiChartStack=false` | `true` включен по умолчанию, поэтому передайте `false` в стек. |
| `multiChartStack=false&multiChartSameScale=true` | Чтобы использовать одну и ту же шкалу оси Y для разных условий, должно быть включено наложение.  По умолчанию это `false`, поэтому передача значения true позволяет реализовать эту функцию. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Единицы — дни, часы, минуты, секунды, миллисекунды.  Единицы всегда следует писать прописными буквами. </br> Определите число единиц, передав требуемое целое число для timeBucketSize.  Обратите внимание, что можно сократить представление до 7 дней.  |
| `timezoneOffset=-<integer>` | Значение целого числа всегда указывается в миллисекундах. </br> Обратите внимание, что эта функция немного отличается от того, что мы включили в обозревателе "аналитика временных рядов", где мы можем выбрать локальное (время браузера) или UTC. |

### <a name="examples"></a>Примеры

Чтобы добавить определения временных рядов в среду "аналитика временных рядов" в качестве параметра URL-адреса, добавьте:

```plaintext
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Используйте примеры определений временных рядов для:

* Идентификатор среды
* Последние 60 минут данных
* Термины (F1PressureID, F2TempStation и F3VibrationPL), составляющие необязательные параметры

Для представления можно создать следующий параметризованный URL-адрес:

```plaintext
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

> [!TIP]
> См. Обозреватель в режиме реального времени [, используя URL-адрес](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]).

Приведенный выше URL-адрес описывает и создает представление обозревателя "аналитика временных рядов".

[Условия обозревателя 1Time серии Insights для @no__t](media/parameterized-url/url1.png)](media/parameterized-url/url1.png#lightbox)

Полное представление (включая диаграмму):

[представление @no__t 1Chart](media/parameterized-url/url2.png)](media/parameterized-url/url2.png#lightbox)

## <a name="next-steps"></a>Следующие шаги

* Узнайте, как [запрашивать данные C#с помощью ](time-series-insights-query-data-csharp.md).

* Сведения о [обозревателе "аналитика временных рядов](./time-series-insights-explorer.md)".
