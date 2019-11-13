---
title: Совместное использование пользовательских представлений с параметризованными URL-адресами Azure Time Series Insights | Документация Майкрософт
description: Узнайте, как разрабатывать параметризованные URL-адреса в службе "аналитика временных рядов Azure", чтобы легко обмениваться настраиваемыми представлениями.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/18/2019
ms.custom: seodec18
ms.openlocfilehash: 145af35f8c36d7f4659c3937209cb0d4d5b221a3
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74006372"
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

Допустимые значения соответствуют меню **быстрого времени** обозревателя "аналитика временных рядов" и включают:

* `1800000` (последние 30 минут)
* `3600000` (последние 60 мин)
* `10800000` (последние 3 часа)
* `21600000` (последние 6 часов)
* `43200000` (последние 12 часов)
* `86400000` (за последние 24 часа)
* `604800000` (за последние 7 дней)
* `2592000000` (последние 30 часов)

### <a name="optional-parameters"></a>Необязательные параметры

Параметр `timeSeriesDefinitions=<collection of term objects>` указывает условия представления "аналитика временных рядов":

| Параметр | Элемент URL-адреса | ОПИСАНИЕ |
| --- | --- | --- |
| **name** | `\<string>` | имя *условия*; |
| **сплитби** | `\<string>` | имя столбца, по которому выполняется *разбиение*; |
| **measureName** | `\<string>` | имя столбца *меры*; |
| **предикат** | `\<string>` | предложение *where* для фильтрации на стороне сервера. |
| **усесум** | `true` | Необязательный параметр, который задает использование функции Sum для меры. </br>  Обратите внимание, что если выбрана мера `Events`, то счетчик будет выбран по умолчанию.  </br>  Если `Events` не выбран, по умолчанию выбирается Average. |

* `multiChartStack=<true/false>` пара "ключ-значение" позволяет включать в диаграмму стек.
* `multiChartSameScale=<true/false>` пара "ключ-значение" обеспечивает одинаковую шкалу по оси Y в пределах необязательного параметра.  
* `timeBucketUnit=<Unit>&timeBucketSize=<integer>` позволяет настроить ползунок интервала, чтобы обеспечить более детализированное или более гладкое, более обобщенное представление диаграммы.  
* Параметр `timezoneOffset=<integer>` позволяет задать часовой пояс для просматриваемой диаграммы в качестве смещения в формате UTC.

| Пары | ОПИСАНИЕ |
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

[Условия обозревателя "аналитика временных рядов" ![](media/parameterized-url/url1.png)](media/parameterized-url/url1.png#lightbox)

Полное представление (включая диаграмму):

[представление диаграммы ![](media/parameterized-url/url2.png)](media/parameterized-url/url2.png#lightbox)

## <a name="next-steps"></a>Дополнительная информация

* Узнайте, как [запрашивать данные C#с помощью ](time-series-insights-query-data-csharp.md).

* Сведения о [обозревателе "аналитика временных рядов](./time-series-insights-explorer.md)".
