---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 02/27/2020
ms.author: orspodek
ms.openlocfilehash: a2297301a0b9c0540c73c0f50483cccfc3181a0f
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129025"
---
### <a name="event-system-properties-mapping"></a>Сопоставление свойств системы событий

> [!Note]
> * Системные свойства поддерживаются для событий с одной записью.
> * Для сопоставления `csv` свойства добавляются в начало записи. Для сопоставления `json` свойства добавляются в соответствии с именем, которое отображается в раскрывающемся списке.

Если в разделе **источника данных** таблицы были выбраны **Свойства системы событий** , необходимо включить следующие свойства в схему таблицы и сопоставление.

**Пример схемы таблицы**

Если данные содержат три столбца (`Timespan`, `Metric`и `Value`), а включаемые свойства `x-opt-enqueued-time` и `x-opt-offset`, создайте или измените схему таблицы с помощью следующей команды:

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:string)
```

**Пример сопоставления CSV**

Выполните следующие команды, чтобы добавить данные в начало записи. Обратите внимание на порядковые значения.

```kusto
    .create table TestTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "Timespan", "Properties":{"Ordinal":"2"}},'
    '   { "column" : "Metric", "Properties":{"Ordinal":"3"}},'
    '   { "column" : "Value", "Properties":{"Ordinal":"4"}},'
    '   { "column" : "EventHubEnqueuedTime", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "EventHubOffset", "Properties":{"Ordinal":"1"}}'
    ']'
```
 
**Пример сопоставления JSON**

Данные добавляются с помощью имен системных свойств, которые отображаются в списке **системных свойств событий** в колонке **подключения к данным** . Выполните следующие команды:

```kusto
    .create table TestTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "Timespan", "Properties":{"Path":"$.timestamp"}},'
    '    { "column" : "Metric", "Properties":{"Path":"$.metric"}},'
    '    { "column" : "Value", "Properties":{"Path":"$.metric_value"}},'
    '    { "column" : "EventHubEnqueuedTime", "Properties":{"Path":"$.x-opt-enqueued-time"}},'
    '    { "column" : "EventHubOffset", "Properties":{"Path":"$.x-opt-offset"}}'
    ']'
```
