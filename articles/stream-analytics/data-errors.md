---
title: Ошибки в диагностических журналах Azure Stream Analytics
description: В этой статье разъясняются различные ошибки ввода и вывода данных, которые могут возникнуть при использовании Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 5457308d577b95201fa31bfad0a6634a7a79eda3
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398130"
---
# <a name="azure-stream-analytics-data-errors"></a>Ошибки данных Azure Stream Analytics

Ошибки данных являются ошибками, которые возникают при обработке данных.  Эти ошибки чаще всего возникают во время десериализации данных, сериализации и записи операций.  При возникновении ошибок данных Stream Analytics записывает подробную информацию и примеры событий в диагностические журналы.  В некоторых случаях резюме этой информации также предоставляется через уведомления портала.

В этой статье описаны различные типы ошибок, причины и детали диагностического журнала для ошибок ввода и выходных данных.

## <a name="diagnostic-log-schema"></a>Схема журнала диагностики

Просмотрите [аналитику Troubleshoot Azure Stream Analytics, используя журналы диагностики,](stream-analytics-job-diagnostic-logs.md#diagnostics-logs-schema) чтобы увидеть схему для диагностических журналов. Следующее значение JSON является примером для поля **свойств** диагностического журнала для ошибки данных.

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

### <a name="inputdeserializererrorinvalidcompressiontype"></a>ВхотливаяDeserializerОшибка.НедействительныйКомпрессионныйтип

* Причина: Выбранный тип сжатия ввода не соответствует данным.
* Уведомление портала предоставлено: Да
* Уровень диагностического журнала: Предупреждение
* Воздействие: Сообщения с любыми ошибками десериализации, включая недействительный тип сжатия, отбрасываются из ввода.
* Подробные сведения журнала
   * Идентификатор ввода сообщения. Для концентратора событий идентификатор — это номер раздела, смещения и последовательности.

**Сообщение об ошибке**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>ВхотливаяDeserializerОшибка.НедействительныйHeader

* Причина: Заголовок входных данных недействителен. Например, CSV имеет столбцы с дублирующими именами.
* Уведомление портала предоставлено: Да
* Уровень диагностического журнала: Предупреждение
* Воздействие: Сообщения с любыми ошибками десериализации, включая недействительный заголовок, отклоняются от ввода.
* Подробные сведения журнала
   * Идентификатор ввода сообщения. 
   * Фактическая полезная нагрузка до нескольких килобайт.

**Сообщение об ошибке**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>ВхотливаяDeserializerОшибка.MissingКолонки

* Причина: Ввод столбцов, определенных с CREATE TABLE или через TIMESTAMP BY не существует.
* Уведомление портала предоставлено: Да
* Уровень диагностического журнала: Предупреждение
* Воздействие: События с отсутствующими столбцов отопадают из ввода.
* Подробные сведения журнала
   * Идентификатор ввода сообщения. 
   * Имена недостающих столбцов. 
   * Фактическая полезная нагрузка до нескольких килобайт.

**Сообщения об ошибках**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>ВхотливаяDeserializerОшибка.TypeConversionОшибка

* Причина: Не удается преобразовать входные данные в тип, указанный в заявлении CREATE TABLE.
* Уведомление портала предоставлено: Да
* Уровень диагностического журнала: Предупреждение
* Воздействие: События с ошибкой преобразования типа отбрасываются из ввода.
* Подробные сведения журнала
   * Идентификатор ввода сообщения. 
   * Название столбца и ожидаемый тип.

**Сообщения об ошибках**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>InputDeserializerError.Недействительныеданные данные

* Причина: Входные данные не в нужном формате. Например, входный вход не действителен JSON.
* Уведомление портала предоставлено: Да
* Уровень диагностического журнала: Предупреждение
* Воздействие: Все события в сообщении после ошибки недействительных данных отсутываются из ввода.
* Подробные сведения журнала
   * Идентификатор ввода сообщения. 
   * Фактическая полезная нагрузка до нескольких килобайт.

**Сообщения об ошибках**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>НедействительноInputTimeStamp

* Причина: Значение выражения TIMESTAMP BY не может быть преобразовано в datetime.
* Уведомление портала предоставлено: Да
* Уровень диагностического журнала: Предупреждение
* Воздействие: События с недействительной меткой времени ввода отогнаны из ввода.
* Подробные сведения журнала
   * Идентификатор ввода сообщения. 
   * Сообщение об ошибке. 
   * Фактическая полезная нагрузка до нескольких килобайт.

**Сообщение об ошибке**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>НедействительноInputTimeStampKey

* Причина: Значение TIMESTAMP BY OVER timestampColumn null.
* Уведомление портала предоставлено: Да
* Уровень диагностического журнала: Предупреждение
* Воздействие: События с недействительным ключом времени ввода отсутсвиваются от ввода.
* Подробные сведения журнала
   * Фактическая полезная нагрузка до нескольких килобайт.

**Сообщение об ошибке**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>LateInputEvent

* Причина: Разница между временем подачи заявки и временем прибытия больше, чем окно допуска позднего прибытия.
* Уведомление портала предоставлено: Нет
* Уровень диагностического журнала: Информация
* Воздействие: Поздние события ввода обрабатываются в соответствии с настройками "Обработка других событий" в разделе Заказа событий конфигурации задания. Для получения дополнительной [информации см.](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)
* Подробные сведения журнала
   * Время подачи заявки и время прибытия. 
   * Фактическая полезная нагрузка до нескольких килобайт.

**Сообщение об ошибке**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent

* Причина: Разница между временем подачи заявки и временем прибытия превышает 5 минут.
* Уведомление портала предоставлено: Нет
* Уровень диагностического журнала: Информация
* Воздействие: Ранние события ввода обрабатываются в соответствии с настройками "Обработка других событий" в разделе Заказа событий конфигурации задания. Для получения дополнительной [информации см.](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)
* Подробные сведения журнала
   * Время подачи заявки и время прибытия. 
   * Фактическая полезная нагрузка до нескольких килобайт.

**Сообщение об ошибке**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutOfOrderEvent

* Причина: Событие считается неупорядоченным в соответствии с определенным окном допуска в порядке.
* Уведомление портала предоставлено: Нет
* Уровень диагностического журнала: Информация
* Воздействие: События вне порядка обрабатываются в соответствии с настройками "Обработка других событий" в разделе Заказа событий конфигурации задания. Для получения дополнительной [информации см.](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)
* Подробные сведения журнала
   * Фактическая полезная нагрузка до нескольких килобайт.

**Сообщение об ошибке**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>Ошибки выходных данных

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionОшибка.RequiredColumnMissing

* Причина: Столбец, необходимый для вывода, не существует. Например, не существует столбца, определяемого как Azure Table PartitionKey.
* Уведомление портала предоставлено: Да
* Уровень диагностического журнала: Предупреждение
* Воздействие: Все ошибки преобразования выходных данных, включая отсутствие требуемого столбца, обрабатываются в соответствии с настройками [политики данных.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Подробные сведения журнала
   * Название столбца и либо идентификатор записи, либо часть записи.

**Сообщение об ошибке**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError.columnNameInvalid

* Причина: Значение столбца не соответствует выходу. Например, имя столбца не является допустимым столбцом таблицы Azure.
* Уведомление портала предоставлено: Да
* Уровень диагностического журнала: Предупреждение
* Воздействие: Все ошибки преобразования выходных данных, включая недействительное имя столбца, обрабатываются в соответствии с настройками [политики данных вывода.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Подробные сведения журнала
   * Название столбца и либо идентификатор записи, либо часть записи.

**Сообщение об ошибке**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionОшибка

* Причина: столбец не может быть преобразован в действительный тип в выходе. Например, значение столбца несовместимо с ограничениями или типом, определенным в таблице S'L.
* Уведомление портала предоставлено: Да
* Уровень диагностического журнала: Предупреждение
* Воздействие: Все ошибки преобразования выходных данных, включая ошибку преобразования типа, обрабатываются в соответствии с настройками [политики данных.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Подробные сведения журнала
   * Имя столбца.
   * Либо идентификатор записи, либо часть записи.

**Сообщение об ошибке**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* Причина: Значение сообщения больше, чем поддерживаемый размер вывода. Например, запись для вывода концентратора событий превышает 1 МБ.
* Уведомление портала предоставлено: Да
* Уровень диагностического журнала: Предупреждение
* Воздействие: Все ошибки преобразования выходных данных, включая превышение лимита по размеру, обрабатываются в соответствии с настройками [политики данных.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Подробные сведения журнала
   * Либо идентификатор записи, либо часть записи.

**Сообщение об ошибке**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* Причина: Запись уже содержит столбец с тем же именем, что и столбец System. Например, выход CosmosDB с идентификатором столбца, когда столбец идентификатора находится в другой колонке.
* Уведомление портала предоставлено: Да
* Уровень диагностического журнала: Предупреждение
* Воздействие: Все ошибки преобразования выходных данных, включая дубликат ключа, обрабатываются в соответствии с настройками [политики данных.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Подробные сведения журнала
   * Имя столбца.
   * Либо идентификатор записи, либо часть записи.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>Следующие шаги

* [Устранение неполадок Azure Stream Analytics с помощью журналов диагностики](stream-analytics-job-diagnostic-logs.md)

* [Основные сведения о мониторинге заданий Stream Analytics и порядок мониторинга запросов](stream-analytics-monitoring.md)
