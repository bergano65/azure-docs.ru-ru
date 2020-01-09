---
title: Анализ данных JSON и AVRO в Azure Stream Analytics
description: В этой статье описывается, как работать со сложными типами данных, такими как массивы, данные в формате JSON и CSV.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 1741510c7398ce74da81f006cb4109d9a33f8f9f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431608"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Анализ данных JSON и AVRO в Azure Stream Analytics

Azure Stream Analytics поддерживают обработку событий в форматах данных CSV, JSON и Avro. Данные JSON и Avro могут быть структурированы и содержать некоторые сложные типы, такие как вложенные объекты (записи) и массивы. 




## <a name="record-data-types"></a>Тип данных "запись"
Тип данных "запись" используется для представления массивов JSON и Avro, когда соответствующие форматы используются во входных потоках данных. Эти примеры демонстрируют пример датчика, который считывает входные события в формате JSON. Ниже приведен пример одного события:

```json
{
    "DeviceId" : "12345",
    "Location" :
    {
        "Lat": 47,
        "Long": 122
    },
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "SensorMetadata" : 
        {
        "Manufacturer":"ABC",
        "Version":"1.2.45"
        }
    }
}
```


### <a name="access-nested-fields-in-known-schema"></a>Доступ к вложенным полям в известной схеме
Используйте точечную нотацию (.) для простого доступа к вложенным полям непосредственно из запроса. Например, этот запрос выбирает координаты широты и долготы в свойстве Location в предыдущих данных JSON. Точечную нотацию можно использовать для навигации на нескольких уровнях, как показано ниже.

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.SensorMetadata.Version
FROM input
```

### <a name="select-all-properties"></a>Выбрать все свойства
Все свойства вложенной записи можно выбрать с помощью подстановочного знака "*". Рассмотрим следующий пример:

```SQL
SELECT input.Location.*
FROM input
```

Результат:

```json
{
    "Lat" : 47,
    "Long" : 122
}
```


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Доступ к вложенным полям, если имя свойства является переменной
Если имя свойства является переменной, используйте функцию [GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue) . 

Например, представьте, что пример потока данных должен быть соединен с эталонными данными, содержащими пороговые значения для каждого датчика устройства. Ниже приведен фрагмент данных с такими справочными данными.

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 75
}
```

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
    -- the where statement selects the property value coming from the reference data
```

### <a name="convert-record-fields-into-separate-events"></a>Преобразование полей записей в отдельные события
Чтобы преобразовать поля записей в отдельные события, используйте оператор [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) вместе с функцией [GetRecordProperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics). Например, если в предыдущем примере имелось несколько записей для Сенсорреадинг, то для их извлечения в различные события можно использовать следующий запрос:

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```



## <a name="array-data-types"></a>Тип данных "массив"

Тип данных "массив" представляет собой упорядоченную коллекцию значений. Ниже приведены типичные операции со значениями массивов. В этих примерах предполагается, что входные события имеют свойство с именем "arrayField", который принадлежит к типу данных "массив".

В этих примерах используются функции [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics), [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics), [GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics) и оператор [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics).

### <a name="working-with-a-specific-array-element"></a>Работа с конкретным элементом массива
Выберите элемент массива по указанному индексу (первый элемент массива):

```SQL
SELECT
    GetArrayElement(arrayField, 0) AS firstElement
FROM input
```

### <a name="select-array-length"></a>Выберите длину массива

```SQL
SELECT
    GetArrayLength(arrayField) AS arrayLength
FROM input
```

### <a name="convert-array-elements-into-separate-events"></a>Преобразование элементов массива в отдельные события
Выберите все элементы массива как отдельные события. Оператор [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) вместе со встроенной функцией [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) извлекает все элементы массива как отдельные события:

```SQL
SELECT
    arrayElement.ArrayIndex,
    arrayElement.ArrayValue
FROM input as event
CROSS APPLY GetArrayElements(event.arrayField) AS arrayElement
```


## <a name="see-also"></a>См. также:
[Типы данных в Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)
