---
title: Анализ данных JSON и AVRO в Azure Stream Analytics
description: В этой статье описывается, как работать со сложными типами данных, такими как массивы, данные в формате JSON и CSV.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 01/29/2020
ms.openlocfilehash: 73905483850a47a9d036bef1b9e1ee60d3484555
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484593"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Анализ данных JSON и AVRO в Azure Stream Analytics

Аналитика Azure Stream поддерживает обработку событий в форматах данных CSV, JSON и Avro. Данные JSON и Avro могут быть структурированы и содержать некоторые сложные типы, такие как вложенные объекты (записи) и массивы. 

>[!NOTE]
>Файлы AVRO, созданные Event Hub Capture, используют определенный формат, который требует использования *пользовательского функции десериализатора.* Для получения дополнительной информации [см. Ввод в любом формате с помощью пользовательских десериализаторов .NET.](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples)



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
Используйте обозначение точек (.) для легкого доступа к вложенным полям непосредственно из запроса. Например, этот запрос выбирает координаты широты и долготы под свойством Расположение в предыдущих данных JSON. Обозначение точек может быть использовано для навигации по нескольким уровням, как показано ниже.

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

|DeviceID|Lat|Long|температура;|Версия|
|-|-|-|-|-|
|12345|47|122|80|1.2.45|


### <a name="select-all-properties"></a>Выберите все свойства
Все свойства вложенной записи можно выбрать с помощью подстановочного знака "*". Рассмотрим следующий пример:

```SQL
SELECT
    DeviceID,
    Location.*
FROM input
```

Результат:

|DeviceID|Lat|Long|
|-|-|-|
|12345|47|122|


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Доступ к вложенным полям, когда имя свойства является переменной

Используйте функцию [GetRecordPropertyValue,](https://docs.microsoft.com/stream-analytics-query/getrecordpropertyvalue-azure-stream-analytics) если имя свойства является переменной. Это позволяет строить динамические запросы без имен свойств хардкодирования.

Например, представьте себе, что поток выборочных данных должен **быть соединен с справочными данными,** содержащими пороговые значения для каждого датчика устройства. Фрагмент таких справочных данных показан ниже.

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

Цель здесь состоит в том, чтобы присоединиться к нашему набору выборочных данных из верхней части статьи к этим справочным данным и вывести одно событие для каждого датчика выше своего порога. Это означает, что наше единственное событие выше может генерировать несколько событий вывода, если несколько датчиков выше своих пороговых значений, благодаря соединению. Для достижения аналогичных результатов без соединения, см.

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

**GetRecordPropertyValue** выбирает свойство в *SensorReadings,* название которого совпадает с именем свойства, исходящем из справочных данных. Затем извлекается связанное значение из *SensorReadings.*

Результат:

|DeviceID|СенсорНамяэ|AlertMessage|
|-|-|-|
|12345|влажность.|Предупреждение : Датчик выше порога|

### <a name="convert-record-fields-into-separate-events"></a>Преобразование полей записи в отдельные события

Чтобы преобразовать поля записей в отдельные события, используйте оператор [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) вместе с функцией [GetRecordProperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics).

С исходными данными образца следующий запрос может быть использован для извлечения свойств в различные события.

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```

Результат:

|DeviceID|СенсорНамяэ|AlertMessage|
|-|-|-|
|12345|температура;|80|
|12345|влажность.|70|
|12345|CustomSensor01|5|
|12345|CustomSensor02|99|
|12345|СенсорМетадата|«Объект объекта»|

Используя [WITH,](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics)можно направить эти события в различные пункты назначения:

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

### <a name="parse-json-record-in-sql-reference-data"></a>Запись Parse JSON в справочных данных S'L
При использовании базы данных Azure S'L в качестве справочных данных в вашей работе можно иметь столбец с данными в формате JSON. Ниже приведен пример такого файла.

|DeviceID|Данные|
|-|-|
|12345|«ключ» : «стоимость1»»|
|54321|«ключ» : «стоимость2»»|

Запись JSON можно разбирать в колонке *Data,* написав простую функцию, определяемую пользователем JavaScript.

```javascript
function parseJson(string) {
return JSON.parse(string);
}
```

Затем можно создать шаг в запросе Stream Analytics, показанный ниже, чтобы получить доступ к полям записей JSON.

 ```SQL
 WITH parseJson as
 (
 SELECT DeviceID, udf.parseJson(sqlRefInput.Data) as metadata,
 FROM sqlRefInput
 )
 
 SELECT metadata.key
 INTO output
 FROM streamInput
 JOIN parseJson 
 ON streamInput.DeviceID = parseJson.DeviceID
```

## <a name="array-data-types"></a>Тип данных "массив"

Тип данных "массив" представляет собой упорядоченную коллекцию значений. Ниже приведены типичные операции со значениями массивов. В этих примерах используются функции [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics), [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics), [GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics) и оператор [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics).

Вот пример одного события. Оба `CustomSensor03` `SensorMetadata` и имеют **массив**типа:

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

### <a name="working-with-a-specific-array-element"></a>Работа с определенным элементом массива

Выберите элемент массива по указанному индексу (первый элемент массива):

```SQL
SELECT
    GetArrayElement(SensorReadings.CustomSensor03, 0) AS firstElement
FROM input
```

Результат:

|firstElement|
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

|deviceId|smKey|smValue|
|-|-|-|
|12345|Производитель|ABC|
|12345|Версия|1.2.45|

Если извлеченные поля должны отображаться в столбцах, можно поворачивать набор данных с помощью синтаксиса [WITH](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics) в дополнение к операции [JOIN.](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics) Это соединение потребует временной [границы](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics#BKMK_DateDiff) условие, которое предотвращает дублирование:

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

|deviceId|Lat|Long|smVersion|smManufacturer|
|-|-|-|-|-|
|12345|47|122|1.2.45|ABC|

## <a name="see-also"></a>См. также
[Типы данных в Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)
