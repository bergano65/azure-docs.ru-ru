---
title: Предстоящие изменения в правилах приема и анализа данных в службе "аналитика временных рядов Azure" Gen2 | Документация Майкрософт
description: Изменения правил приема
ms.service: time-series-insights
services: time-series-insights
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/12/2020
ms.custom: lyhughes
ms.openlocfilehash: cdf90614e1f766fc37e04a1416081bd35e19b74e
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88168207"
---
# <a name="upcoming-changes-to-the-json-flattening-and-escaping-rules-for-new-environments"></a>Предстоящие изменения в правилах обработки и преобразования JSON для новых сред

**Эти изменения будут применены только к *вновь созданным* средам Gen2 "аналитика временных рядов Azure". Эти изменения не применяются к средам Gen1.**

Среда Gen2 "аналитика временных рядов Azure" динамически создает столбцы хранилища, следуя определенному набору соглашений об именовании. При приеме события к полезной нагрузке JSON и именам свойств применяется набор правил. Изменения в том, как данные JSON сведены и сохранены, вступают в действие в новых средах Azure Time Series Insights Gen2 в июле 2020. Это изменение влияет на работу в следующих случаях:

* Если полезные данные JSON содержат вложенные объекты
* Если полезные данные JSON содержат массивы
* При использовании любого из следующих четырех специальных символов в имени свойства JSON: [\. '
* Если одно или несколько свойств идентификатора TS находятся внутри вложенного объекта.

Если создается новая среда и один или несколько описанных выше вариантов применяются к полезной нагрузке события, вы увидите, что данные сведены и сохранены по-разному. Ниже приведена сводка изменений.

| Текущее правило | Создать правило | Пример JSON | Предыдущее имя столбца | Новое имя столбца
|---|---| ---| ---|  ---|
| Вложенный JSON преобразуется с использованием символа подчеркивания в качестве описания |Вложенные JSON сведены с использованием точки в качестве описания  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| Специальные символы не преобразуются в escape-последовательность | Имена свойств JSON, которые содержат специальные символы. символы [\ и ' экранированы с помощью [' и ']. В пределах ["и"] есть дополнительные Escape, заключенные в одинарные и обратные кавычки. Одинарная кавычка будет записана как \' , а обратная косая черта будет записана как\\\  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` |
| Массивы примитивов хранятся в виде строки | Массивы типов-примитивов хранятся в динамическом типе  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
Массивы объектов всегда сведены, создавая несколько событий. | Если объекты в массиве не имеют надлежащего идентификатора TS или метки времени (ий), массив объектов хранится в целом как динамический тип | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>Рекомендуемые изменения для новых сред

### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>Если идентификатор TS и свойство "timestamp" вложены в объект

* Все новые развертывания должны соответствовать новым правилам приема. Например, если вы используете идентификатор TS, `telemetry_tagId` вам потребуется обновить все шаблоны Azure Resource Manager (ARM) или автоматически развертывать скрипты, чтобы настроить в `telemetry.tagId` качестве идентификатора TS среды. Это изменение необходимо для меток времени источника события в вложенном JSON.

### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>Если полезные данные содержат вложенные знаки JSON или специальные символы и автоматизируют создание выражений переменных [модели временных рядов](.\time-series-insights-update-tsm.md)

* Обновите код клиента, выполняя [типесбатчпут](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput) , в соответствии с новыми правилами приема. Например, предыдущее [выражение временного ряда](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) `"value": {"tsx": "$event.series_value.Double"}` должно быть обновлено до одного из следующих вариантов:
  * `"value": {"tsx": "$event.series.value.Double"}`
  * `"value": {"tsx": "$event['series']['value'].Double"}`

## <a name="next-steps"></a>Дальнейшие действия

* Чтение [хранилища Gen2 и входных данных Azure Time Series Insights](./time-series-insights-update-storage-ingress.md).

* Узнайте больше о том, как выполнять запросы к данным с помощью [API запросов временных рядов](./concepts-query-overview.md).

* Дополнительные сведения о [новом синтаксисе выражений временных рядов](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)см. в этой статье.
