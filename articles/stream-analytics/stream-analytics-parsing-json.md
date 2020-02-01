---
title: Анализ данных JSON и AVRO в Azure Stream Analytics
description: В этой статье описывается, как работать со сложными типами данных, такими как массивы, данные в формате JSON и CSV.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 01/29/2020
ms.openlocfilehash: ac06521df38bdc91ca717d888c73cd541576014d
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905456"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Анализ данных JSON и AVRO в Azure Stream Analytics

Azure Stream Analytics поддерживают обработку событий в форматах данных CSV, JSON и Avro. Данные JSON и Avro могут быть структурированы и содержать некоторые сложные типы, такие как вложенные объекты (записи) и массивы. 

>[!NOTE]
>Файлы AVRO, созданные функцией записи концентратора событий, используют конкретный формат, который требует использования *пользовательского средства десериализации* . Дополнительные сведения см. [в статье чтение входных данных в любом формате с помощью пользовательских десериализаторов .NET](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples).



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
    SensorReadings.Temperature,
    SensorReadings.SensorMetadata.Version
FROM input
```

Результат:

|DeviceID|Lat|Длинные|Температура|Версия|
|-|-|-|-|-|
|12345|47|122|80|1.2.45|


### <a name="select-all-properties"></a>Выбрать все свойства
Все свойства вложенной записи можно выбрать с помощью подстановочного знака "*". Рассмотрим следующий пример:

```SQL
SELECT
    DeviceID,
    Location.*
FROM input
```

Результат:

|DeviceID|Lat|Длинные|
|-|-|-|
|12345|47|122|


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Доступ к вложенным полям, если имя свойства является переменной

Если имя свойства является переменной, используйте функцию [GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getrecordpropertyvalue-azure-stream-analytics) . Это позволяет создавать динамические запросы без имен свойств прописано.

Например, представьте, что образец потока данных должен **быть соединен с эталонными данными,** содержащими пороговые значения для каждого датчика устройства. Ниже приведен фрагмент данных с такими справочными данными.

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 85
},
{
    "DeviceId" : "12345",
    "SensorName" : "Humidity",
    "Value" : 65
}
```

Цель этого примера — присоединить наш образец набора данных из верхней части статьи к этим эталонным данным и вывести одно событие для каждой меры датчика выше порогового значения. Это означает, что наше одно событие, описанное выше, может создать несколько выходных событий, если несколько датчиков выше соответствующих порогов, благодаря соединению. Чтобы получить аналогичные результаты без объединения, см. раздел ниже.

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName,
    "Alert : Sensor above threshold" AS AlertMessage
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
```

**GetRecordPropertyValue** выбирает свойство в *сенсорреадингс*, имя которого совпадает с именем свойства, поступающего от ссылочных данных. Затем извлекается связанное значение из *сенсорреадингс* .

Результат:

|DeviceID|сенсорнаме|AlertMessage|
|-|-|-|
|12345|влажность.|Предупреждение: датчик выше порогового значения|

### <a name="convert-record-fields-into-separate-events"></a>Преобразование полей записей в отдельные события

Чтобы преобразовать поля записей в отдельные события, используйте оператор [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) вместе с функцией [GetRecordProperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics).

С исходными примерами данных можно использовать следующий запрос для извлечения свойств в различные события.

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```

Результат:

|DeviceID|сенсорнаме|AlertMessage|
|-|-|-|
|12345|Температура|80|
|12345|влажность.|70|
|12345|CustomSensor01|5|
|12345|CustomSensor02|99|
|12345|сенсорметадата|[объект Object]|

С помощью [с](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics)можно перенаправлять эти события в разные места назначения:

```SQL
WITH Stage0 AS
(
    SELECT
        event.DeviceID,
        sensorReading.PropertyName,
        sensorReading.PropertyValue
    FROM input as event
    CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
)

SELECT DeviceID, PropertyValue AS Temperature INTO TemperatureOutput FROM Stage0 WHERE PropertyName = 'Temperature'
SELECT DeviceID, PropertyValue AS Humidity INTO HumidityOutput FROM Stage0 WHERE PropertyName = 'Humidity'
```

## <a name="array-data-types"></a>Тип данных "массив"

Тип данных "массив" представляет собой упорядоченную коллекцию значений. Ниже приведены типичные операции со значениями массивов. В этих примерах используются функции [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics), [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics), [GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics) и оператор [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics).

Ниже приведен пример одного события. `CustomSensor03` и `SensorMetadata` имеют **массив**типов:

```json
{
    "DeviceId" : "12345",
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "CustomSensor03": [12,-5,0]
     },
    "SensorMetadata":[
        {          
            "smKey":"Manufacturer",
            "smValue":"ABC"                
        },
        {
            "smKey":"Version",
            "smValue":"1.2.45"
        }
    ]
}
```

### <a name="working-with-a-specific-array-element"></a>Работа с конкретным элементом массива

Выберите элемент массива по указанному индексу (первый элемент массива):

```SQL
SELECT
    GetArrayElement(SensorReadings.CustomSensor03, 0) AS firstElement
FROM input
```

Результат:

|фирстелемент|
|-|
|12|

### <a name="select-array-length"></a>Выберите длину массива

```SQL
SELECT
    GetArrayLength(SensorReadings.CustomSensor03) AS arrayLength
FROM input
```

Результат:

|arrayLength|
|-|
|3|

### <a name="convert-array-elements-into-separate-events"></a>Преобразование элементов массива в отдельные события

Выберите все элементы массива как отдельные события. Оператор [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) вместе со встроенной функцией [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) извлекает все элементы массива как отдельные события:

```SQL
SELECT
    DeviceId,
    CustomSensor03Record.ArrayIndex,
    CustomSensor03Record.ArrayValue
FROM input
CROSS APPLY GetArrayElements(SensorReadings.CustomSensor03) AS CustomSensor03Record

```

Результат:

|deviceId|ArrayIndex|ArrayValue|
|-|-|-|
|12345|0|12|
|12345|1|-5|
|12345|2|0|

```SQL
SELECT   
    i.DeviceId, 
    SensorMetadataRecords.ArrayValue.smKey as smKey,
    SensorMetadataRecords.ArrayValue.smValue as smValue
FROM input i
CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords
 ```
 
Результат:

|deviceId|смкэй|смвалуе|
|-|-|-|
|12345|Производитель|ABC|
|12345|Версия|1.2.45|

Если извлеченные поля должны появиться в столбцах, можно выполнить сведение набора данных с помощью синтаксиса [with](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics) в дополнение к операции [Join](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics) . Для этого объединения потребуется условие [временных границ](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics#BKMK_DateDiff) , которое предотвращает дублирование:

```SQL
WITH DynamicCTE AS (
    SELECT   
        i.DeviceId,
        SensorMetadataRecords.ArrayValue.smKey as smKey,
        SensorMetadataRecords.ArrayValue.smValue as smValue
    FROM input i
    CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords 
)

SELECT
    i.DeviceId,
    i.Location.*,
    V.smValue AS 'smVersion',
    M.smValue AS 'smManufacturer'
FROM input i
LEFT JOIN DynamicCTE V ON V.smKey = 'Version' and V.DeviceId = i.DeviceId AND DATEDIFF(minute,i,V) BETWEEN 0 AND 0 
LEFT JOIN DynamicCTE M ON M.smKey = 'Manufacturer' and M.DeviceId = i.DeviceId AND DATEDIFF(minute,i,M) BETWEEN 0 AND 0
```

Результат:

|deviceId|Lat|Длинные|смверсион|сммануфактурер|
|-|-|-|-|-|
|12345|47|122|1.2.45|ABC|

## <a name="see-also"></a>См. также:
[Типы данных в Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)
