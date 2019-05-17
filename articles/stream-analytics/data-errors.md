---
title: Ошибки данных журнала диагностики Azure Stream Analytics
description: Здесь объясняются различные входные и выходные данные ошибки, которые могут возникнуть при использовании Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: b00eb12092838746f4bfe16f00eac55df9224b09
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65607233"
---
# <a name="azure-stream-analytics-data-errors"></a>Ошибки данных Azure Stream Analytics

Если имеется несоответствие в данных, обрабатываемых задание Azure Stream Analytics, Stream Analytics отправляет событие ошибки данных журналов диагностики. Stream Analytics записывает подробные сведения и пример события, диагностических журналов при возникновении ошибок данных. Сводные данные также предоставляются через некоторые ошибки в уведомлениях портала.

В этой статье описаны различные типы ошибок, причины и сведения журнала диагностики для входных и выходных данных ошибки.

## <a name="diagnostic-log-schema"></a>Схема журналов диагностики

См. в разделе [Устранение неполадок Azure Stream Analytics с помощью журналов диагностики](stream-analytics-job-diagnostic-logs.md#diagnostics-logs-schema) для просмотра схемы для журналов диагностики. Приведенный ниже код JSON является примером значения **свойства** поле журнала диагностики для ошибок данных.

```json
{
    "Source": "InputTelemetryData",
    "Type": "DataError",
    "DataErrorType": "InputDeserializerError.InvalidData",
    "BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "Message": "Input Message Id: https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt Error: Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "ExampleEvents": "[\"1,2\\\\u000d\\\\u000a3,4\\\\u000d\\\\u000a5,6\"]",
    "FromTimestamp": "2019-03-22T22:34:18.5664937Z",
    "ToTimestamp": "2019-03-22T22:34:18.5965248Z",
    "EventCount": 1
}
```

## <a name="input-data-errors"></a>Ошибки ввода данных

### <a name="inputdeserializererrorinvalidcompressiontype"></a>InputDeserializerError.InvalidCompressionType

* Причина. Тип сжатия не соответствуют данным.
* Предоставляемые уведомления с портала: Да
* Уровень журнала диагностики: Предупреждение
* Подробные сведения журнала
   * Введите идентификатор сообщения. Для концентратора событий идентификатор является PartitionId, смещение и порядковый номер.

**Сообщение об ошибке**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* Причина. Недопустимый заголовок входных данных. Например CSV-ФАЙЛ содержит столбцы с повторяющимися именами.
* Предоставляемые уведомления с портала: Да
* Уровень журнала диагностики: Предупреждение
* Подробные сведения журнала
   * Введите идентификатор сообщения. 
   * Фактически применяемые полезные данные до несколько килобайт.

**Сообщение об ошибке**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* Причина. Входные столбцы, определенные в инструкции CREATE TABLE или с помощью TIMESTAMP BY не существует.
* Предоставляемые уведомления с портала: Да
* Уровень журнала диагностики: Предупреждение
* Подробные сведения журнала
   * Введите идентификатор сообщения. 
   * Имена столбцов, которые отсутствуют. 
   * Фактически применяемые полезные данные до несколько килобайт.

**Сообщения об ошибках**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* Причина. Не удалось преобразовать входные данные для типа, указанного в инструкции CREATE TABLE.
* Предоставляемые уведомления с портала: Да
* Уровень журнала диагностики: Предупреждение
* Подробные сведения журнала
   * Введите идентификатор сообщения. 
   * Имя столбца и ожидаемому типу.

**Сообщения об ошибках**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>InputDeserializerError.InvalidData

* Причина. Входные данные не в правильном формате. Например входные данные не допустимых данных JSON.
* Предоставляемые уведомления с портала: Да
* Уровень журнала диагностики: Предупреждение
* Подробные сведения журнала
   * Введите идентификатор сообщения. 
   * Фактически применяемые полезные данные до несколько килобайт.

**Сообщения об ошибках**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>InvalidInputTimeStamp

* Причина. Значение выражения TIMESTAMP BY невозможно преобразовать в тип datetime.
* Предоставляемые уведомления с портала: Да
* Уровень журнала диагностики: Предупреждение
* Подробные сведения журнала
   * Введите идентификатор сообщения. 
   * Сообщение об ошибке. 
   * Фактически применяемые полезные данные до несколько килобайт.

**Сообщение об ошибке**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>InvalidInputTimeStampKey

* Причина. Значение TIMESTAMP BY OVER timestampColumn имеет значение NULL.
* Предоставляемые уведомления с портала: Да
* Уровень журнала диагностики: Предупреждение
* Подробные сведения журнала
   * Фактически применяемые полезные данные до несколько килобайт.

**Сообщение об ошибке**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>LateInputEvent

* Причина. Разница между временем приложения и время получения больше, чем значения поступления.
* Предоставляемые уведомления с портала: Нет
* Уровень журнала диагностики: Информация
* Подробные сведения журнала
   * Приложение временем и временем. 
   * Фактически применяемые полезные данные до несколько килобайт.

**Сообщение об ошибке**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent

* Причина. Разница между временем приложения и временем более чем на 5 минут.
* Предоставляемые уведомления с портала: Нет
* Уровень журнала диагностики: Информация
* Подробные сведения журнала
   * Приложение временем и временем. 
   * Фактически применяемые полезные данные до несколько килобайт.

**Сообщение об ошибке**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutOfOrderEvent

* Причина. Событие считается порядке в соответствии с такой же окна, определенного.
* Предоставляемые уведомления с портала: Нет
* Уровень журнала диагностики: Информация
* Подробные сведения журнала
   * Фактически применяемые полезные данные до несколько килобайт.

**Сообщение об ошибке**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>Выходные данные ошибки

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError.RequiredColumnMissing

* Причина. Столбец, необходимый для выходных данных не существует. Например существует столбец, определенный как does't PartitionKey таблицы Azure.
* Предоставляемые уведомления с портала: Да
* Уровень журнала диагностики: Предупреждение
* Подробные сведения журнала
   * Имя столбца и идентификатора записи или часть записи.

**Сообщение об ошибке**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError.ColumnNameInvalid

* Причина. Значение столбца не соответствуют с выходными данными. Например имя столбца не является столбцом допустимую таблицу Azure.
* Предоставляемые уведомления с портала: Да
* Уровень журнала диагностики: Предупреждение
* Подробные сведения журнала
   * Имя столбца и идентификатор записи или часть записи.

**Сообщение об ошибке**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionError

* Причина. Столбец невозможно преобразовать в допустимый тип в выходных данных. Например значение столбца несовместим с ограничениями или тип, определенный в таблице SQL.
* Предоставляемые уведомления с портала: Да
* Уровень журнала диагностики: Предупреждение
* Подробные сведения журнала
   * Имя столбца.
   * Идентификатор записи, или часть записи.

**Сообщение об ошибке**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* Причина. Значение сообщения больше, чем размер поддерживаемой выходной. Например запись размером более 1 МБ для выходных данных концентратора событий.
* Предоставляемые уведомления с портала: Да
* Уровень журнала диагностики: Предупреждение
* Подробные сведения журнала
   * Идентификатор записи, или часть записи.

**Сообщение об ошибке**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* Причина. Запись уже содержит столбец с тем же именем, как системный столбец. Например выходные данные CosmosDB со столбцом с именем идентификатора при идентификатор столбца в другой столбец.
* Предоставляемые уведомления с портала: Да
* Уровень журнала диагностики: Предупреждение
* Подробные сведения журнала
   * Имя столбца.
   * Идентификатор записи, или часть записи.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>Дальнейшие действия

* [Устранение неполадок Azure Stream Analytics с помощью журналов диагностики](stream-analytics-job-diagnostic-logs.md)

* [Сведения о мониторинга задания Stream Analytics и порядок мониторинга запросов](stream-analytics-monitoring.md)
