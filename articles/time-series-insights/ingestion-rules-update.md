---
title: Предстоящие изменения в правилах приема и анализа данных в службе "аналитика временных рядов Azure" | Документация Майкрософт
description: Изменения правил приема
ms.service: time-series-insights
services: time-series-insights
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/16/2020
ms.custom: lyhughes
ms.openlocfilehash: 067244aa40256e3cc76239343790974bc3c06481
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85919029"
---
# <a name="upcoming-changes-to-the-json-flattening-and-escaping-rules-for-new-environments"></a>Предстоящие изменения в правилах обработки и преобразования JSON для новых сред

Эти изменения будут применены к *новым* средам Azure Time Series Insights с оплатой по мере использования (PAYG). Эти изменения не применяются к стандартным средам SKU (S).

Среда службы "аналитика временных рядов Azure" динамически создает столбцы хранилища, следуя определенному набору соглашений об именовании. При приеме события к полезной нагрузке JSON и именам свойств применяется набор правил. Изменения в способах преобразования и сохранения данных JSON вступят в действие для новых сред Azure Time Series Insights с оплатой по мере использования в июле 2020. Это изменение влияет на работу в следующих случаях:

* Если полезные данные JSON содержат вложенные объекты
*  Если полезные данные JSON содержат массивы
*  При использовании любого из следующих четырех специальных символов в имени свойства JSON: [\. '
*  Если одно или несколько свойств идентификатора TS находятся внутри вложенного объекта.

Если создается новая среда и один или несколько описанных выше вариантов применяются к полезной нагрузке события, вы увидите, что данные сведены и сохранены по-разному. Ниже приведена сводка изменений.

| Текущее правило | Создать правило | Пример JSON | Предыдущее имя столбца | Новое имя столбца
|---|---| ---| ---|  ---|
| Вложенный JSON преобразуется с использованием символа подчеркивания в качестве описания |Вложенные JSON сведены с использованием точки в качестве описания  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| Специальные символы не преобразуются в escape-последовательность | Имена свойств JSON, которые содержат специальные символы. символы [\ и ' экранированы с помощью [' и ']. В пределах ["и"] есть дополнительные Escape, заключенные в одинарные и обратные кавычки. Одинарная кавычка будет записана как \' , а обратная косая черта будет записана как\\\  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` | 
| Массивы примитивов хранятся в виде строки | Массивы типов-примитивов хранятся в динамическом типе  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
Массивы объектов всегда сведены, создавая несколько событий. | Если объекты в массиве не имеют надлежащего идентификатора TS или метки времени (ий), массив объектов хранится в целом как динамический тип | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>Рекомендуемые изменения для новых сред

#### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>Если идентификатор TS или свойство timestamp вложены в объект:

*  Все новые развертывания должны соответствовать новым правилам приема. Например, если вы используете идентификатор TS, `telemetry_tagId` вам потребуется обновить все шаблоны ARM или скрипты автоматического развертывания, чтобы настроить в `telemetry.tagId` качестве идентификатора TS среды. Это изменение необходимо для меток времени источника события в вложенном JSON.

 #### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>Если полезные данные содержат вложенные знаки JSON или специальные символы и автоматизируют создание выражений переменных [модели временных рядов](.\time-series-insights-update-tsm.md)

*  Обновите код клиента, выполняя [типесбатчпут](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch#typesbatchput) , в соответствии с новыми правилами приема. Например, предыдущее [выражение временного ряда](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) `"value": {"tsx": "$event.series_value.Double"}` должно быть обновлено до одного из следующих вариантов:
    * `"value": {"tsx": "$event.series.value.Double"}`
    * `"value": {"tsx": "$event['series']['value'].Double"}`



## <a name="next-steps"></a>Дальнейшие шаги

- Чтение [добавления поддержки для типа данных Long](./time-series-insights-long-data-type.md).

- Чтение [хранилища и входных данных службы "аналитика временных рядов Azure](./time-series-insights-update-storage-ingress.md)".

