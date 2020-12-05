---
title: Миграция с версии v2 на v3 REST API-Speech Service
titleSuffix: Azure Cognitive Services
description: По сравнению с v2, Новая версия API V3 содержит набор небольших критических изменений. Этот документ поможет выполнить миграцию в новую основную версию без времени.
services: cognitive-services
author: bexxx
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: rbeckers
ms.custom: devx-track-csharp
ms.openlocfilehash: dd1dae963781cc0caacc25938e700a4c70a1f51a
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2020
ms.locfileid: "96738027"
---
# <a name="migration-from-v20-to-v30-of-speech-to-text-rest-api"></a>Переход с версии 2.0 на версию 3.0 от перевода речи в текст REST API

Версия V3 речевого REST API расширяется по сравнению с предыдущей версией API в отношении надежности и простоты использования. Макет API более тесно согласуется с другими Azure или API-интерфейсы Cognitive Services. Это поможет вам применить имеющиеся навыки при использовании нашего API распознавания речи.

Обзор API доступен в виде [документа Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0). Это идеальный вариант для получения обзора API и тестирования нового API.

Мы предоставляем примеры для C# и Python. Для пакетных записей можно найти примеры в [репозитории примера GitHub](https://aka.ms/csspeech/samples) внутри `samples/batch` подкаталога.

## <a name="forward-compatibility"></a>Прямая совместимость

Чтобы обеспечить плавную миграцию на v3, все сущности из v2 можно также найти в API V3 с тем же идентификатором. Если имеется изменение схемы результата (например, транскрипции), ответы для версии API GET в v3 будут находиться в схеме v3. При выполнении функции GET в версии 2 API результирующая схема будет иметь формат v2. Вновь созданные **сущности v3 недоступны** в версии 2.

## <a name="breaking-changes"></a>Критические изменения

Список критических изменений был отсортирован по величине изменений, необходимых для адаптации. Существует лишь несколько изменений, требующих нетривиальных изменений в вызывающем коде. Для большинства изменений требуются простые переименования. Время, затраченное на миграцию команд с v2 на v3 в течение нескольких часов в несколько дней. Однако преимущества повышенной стабильности, упрощения кода и более быстрые ответы быстро отправляют инвестиции. 

### <a name="host-name-changes"></a>Изменения имени узла

Имена узлов изменились с {Region}. пользовательские элементы отчета. AI на {Region}. API., используя функцию Microsoft. com. В этом случае пути больше не содержат "API/", так как они являются частью имени узла. Полное описание регионов и путей см. в [документе Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) .

### <a name="identity-of-an-entity"></a>Удостоверение сущности

Свойство `id` было заменено на `self` . В версии 2 пользователь API должен был понять, как создаются наши пути в API. Это было нерасширяемым и не требует от пользователя ненужной работы. Свойство `id` (UUID) заменяется на `self` (String), которое является расположением сущности (URL-адреса). Значение по-прежнему уникально для всех сущностей. Если `id` хранится в коде в виде строки, для поддержки новой схемы достаточно простого переименования. Теперь содержимое можно использовать в `self` качестве URL-адреса для всех вызовов RESTful для сущности (получение, исправление, удаление).

Если сущность имеет дополнительные функциональные возможности, доступные в других путях, они перечислены в разделе `links` . Хорошим примером является транскрипция, которая имеет отдельный метод для `GET` содержимого транскрипции.

Транскрипция v2:

```json
{
    "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Transcription using locale en-US"
}
```

v3 запись транскрипции:

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US", 
    "displayName": "Transcription using locale en-US",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    }
}
```

В зависимости от реализации клиента может быть недостаточно для переименования свойства. Мы рекомендуем использовать преимущества возвращаемых значений `self` и в `links` качестве целевых URL-адресов вызовов RESTful, а не создавать пути в клиенте. Используя возвращенные URL-адреса, можно убедиться, что будущие изменения в путях не нарушат код клиента.

### <a name="working-with-collections-of-entities"></a>Работа с коллекциями сущностей

Ранее API v2 вернул все доступные сущности в ответе. Чтобы обеспечить более точный контроль над ожидаемым размером ответа, в v3 все ответы на коллекции разбиты на страницы. Вы можете управлять количеством возвращаемых сущностей и смещением страницы. Такое поведение упрощает прогнозирование среды выполнения обработчика реагирования и его совместимость с другими API-интерфейсами Azure.

Основная форма ответа одинакова для всех коллекций:

```json
{
    "values": [
        {     
        }
    ],
    "@nextLink": "https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/{collection}?skip=100&top=100"
}
```

Свойство `values` содержит подмножество доступных сущностей коллекции. Количество и смещение можно контролировать с помощью параметров запроса `skip` и `top` . Если `@nextLink` параметр не равен null, то доступны дополнительные данные, и можно получить следующий пакет данных, выполнив команду Get On `$.@nextLink` .

Это изменение требует вызова `GET` для коллекции в цикле, пока не будут возвращены все элементы.

### <a name="creating-transcriptions"></a>Создание транскрипций

Подробное описание создания транскрипции можно найти в [разделе Практические руководства](./batch-transcription.md)по записи пакетов.

Создание транскрипций изменено в версии 3 для явного включения параметров записи. Все свойства конфигурации (необязательно) теперь можно задать в `properties` свойстве.
Кроме того, Версия V3 поддерживает несколько входных файлов, поэтому для версии 2 требуется список URL-адресов, а не один URL-адрес. Имя свойства было переименовано из `recordingsUrl` в `contentUrls` . Функция анализа тональности в транскрипциях была удалена на v3. Вместо этого рекомендуется использовать [анализ текста](https://azure.microsoft.com/en-us/services/cognitive-services/text-analytics/) службы Microsoft.

Новое свойство `timeToLive` в разделе `properties` может помочь очистить существующие завершенные сущности. `timeToLive`Указывает длительность, после которой Завершенная сущность будет автоматически удалена. Задайте для него большое значение (например, `PT12H` ), если сущности постоянно отправляются, потребляются и удаляются, и, следовательно, обычно обрабатываются до 12 часов.

текст запроса записи транскрипции v2:

```json
{
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "properties": {
    "AddDiarization": "False",
    "AddWordLevelTimestamps": "False",
    "PunctuationMode": "DictatedAndAutomatic",
    "ProfanityFilterMode": "Masked"
  }
}
```

текст запроса POST для транскрипции V3:

```json
{
  "locale": "en-US",
  "displayName": "Transcription using locale en-US",
  "contentUrls": [
    "https://contoso.com/mystoragelocation",
    "https://contoso.com/myotherstoragelocation"
  ],
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  }
}
```

### <a name="format-of-v3-transcription-results"></a>Формат результатов транскрипции v3

Схема результатов транскрипции немного изменена для согласования с транскрипциями, созданными конечными точками в режиме реального времени. Подробное описание нового формата можно найти в разделе Руководство по записи [пакетов](./batch-transcription.md). Схема результата публикуется в [репозитории примера GitHub](https://aka.ms/csspeech/samples) в разделе `samples/batch/transcriptionresult_v3.schema.json` .

Имена свойств теперь имеют стиль Camel, а значения для каналов и динамиков используют целочисленные типы. Чтобы согласовать формат длительности с другими API-интерфейсами Azure, теперь он отформатирован, как описано в ISO 8601.

Пример результата транскрипции v3. Различия описаны в комментариях.

```json
{
  "source": "...",                      // (new in v3) was AudioFileName / AudioFileUrl
  "timestamp": "2020-06-16T09:30:21Z",  // (new in v3)
  "durationInTicks": 41200000,          // (new in v3) was AudioLengthInSeconds
  "duration": "PT4.12S",                // (new in v3)
  "combinedRecognizedPhrases": [        // (new in v3) was CombinedResults
    {
      "channel": 0,                     // (new in v3) was ChannelNumber
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // (new in v3) was SegmentResults
    {
      "recognitionStatus": "Success",   // 
      "channel": 0,                     // (new in v3) was ChannelNumber
      "offset": "PT0.07S",              // (new in v3) new format, was OffsetInSeconds
      "duration": "PT1.59S",            // (new in v3) new format, was DurationInSeconds
      "offsetInTicks": 700000.0,        // (new in v3) was Offset
      "durationInTicks": 15900000.0,    // (new in v3) was Duration

      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,phrase
          "speaker": 1,
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",

          "words": [
            {
              "word": "hello",
              "offset": "PT0.09S",
              "duration": "PT0.48S",
              "offsetInTicks": 900000.0,
              "durationInTicks": 4800000.0,
              "confidence": 0.987572
            },
            {
              "word": "world",
              "offset": "PT0.59S",
              "duration": "PT0.16S",
              "offsetInTicks": 5900000.0,
              "durationInTicks": 1600000.0,
              "confidence": 0.906032
            }
          ]
        }
      ]
    }
  ]
}
```

### <a name="getting-the-content-of-entities-and-the-results"></a>Получение содержимого сущностей и результатов

В версии 2 ссылки на входные и результирующие файлы были встроены вместе с остальными метаданными сущности. В качестве улучшения в версии 3 существует четкое разделение метаданных сущности, которое возвращается функцией GET on, `$.self` а также сведения и учетные данные для доступа к результирующим файлам. Это разделение помогает защищать данные клиентов и позволяет точно контролировать длительность срока действия учетных данных.

В версии 3 имеется свойство, вызываемое в виде `files` ссылок в случае, если сущность предоставляет данные (наборы данных, транскрипции, конечные точки, вычисления). При вызове GET `$.links.files` возвращается список файлов и URL-адрес SAS для доступа к содержимому каждого файла. Для управления сроком действия URL-адресов SAS можно использовать параметр запроса `sasValidityInSeconds` для указания времени существования.

Транскрипция v2:

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "status": "Succeeded",
  "reportFileUrl": "https://contoso.com/report.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f728327c53b5",
  "resultsUrls": {
    "channel_0": "https://contoso.com/audiofile1.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f72832e6600c",
    "channel_1": "https://contoso.com/audiofile2.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=3e0163f1-0029-4d4a-988d-3fba7d7c53b5"
  }
}
```

v3 запись транскрипции:

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    } 
}
```

Затем в результате ПОЛУЧАЕТся `$.links.files` следующее:

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/f23e54f5-ed74-4c31-9730-2f1a3ef83ce8",
      "name": "Name",
      "kind": "Transcription",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/mywavefile1.wav.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/28bc946b-c251-4a86-84f6-ea0f0a2373ef",
      "name": "Name",
      "kind": "TranscriptionReport",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/report.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files?skip=2&top=2"
}
```

`kind`Указывает формат содержимого файла. Для транскрипций файлы типа `TranscriptionReport` представляют собой сводку по заданию, а файлы типа `Transcription` являются результатом самого задания.

### <a name="customizing-models"></a>Настройка моделей

До версии 3 существовало различие между "акустической моделью" и "языковой моделью" при обучении модели. Это различие привело к тому, что при создании конечных точек или транскрипций необходимо указать несколько моделей. Чтобы упростить этот процесс для вызывающего, мы удалили различия и зависели от содержимого наборов данных, используемых для обучения модели. После этого изменения модель создания модели теперь поддерживает смешанные наборы данных (данные языка и акустические данные). Для конечных точек и транскрипций теперь требуется только одна модель.

В результате этого изменения потребность `kind` в записи в Post была удалена и `datasets[]` теперь может содержать несколько наборов данных одного и того же или смешанного типа.

Чтобы улучшить результаты обученной модели, акустические данные автоматически используются внутри для обучения языков. Как правило, модели, созданные с помощью API V3, предоставляют более точные результаты, чем модели, созданные с помощью API v2.

### <a name="retrieving-base-and-custom-models"></a>Получение базовых и пользовательских моделей

Чтобы упростить получение доступных моделей, v3 разделяет коллекции "базовых моделей" от клиента, принадлежащего к настраиваемым моделям. Два маршрута теперь `GET /speechtotext/v3.0/models/base` и `GET /speechtotext/v3.0/models/` .

Ранее все модели были возвращены вместе в одном ответе.

### <a name="name-of-an-entity"></a>Имя сущности

Имя свойства `name` переименовывается в `displayName` . Это согласуется с другими интерфейсами API Azure, чтобы не указывать свойства идентификации. Значение этого свойства не должно быть уникальным и может быть изменено после создания сущности с помощью `PATCH` .

Транскрипция v2:

```json
{
    "name": "Transcription using locale en-US"
}
```

v3 запись транскрипции:

```json
{
    "displayName": "Transcription using locale en-US"
}
```

### <a name="accessing-referenced-entities"></a>Доступ к упоминаемым сущностям

В сущностях v2, на которые имеются ссылки, всегда были встроены, например используемые модели конечной точки. Вложение сущностей привело к большим откликам, и потребители редко потребляют вложенное содержимое. Чтобы уменьшить размер ответа и повысить производительность всех пользователей API, упоминаемые сущности больше не включаются в ответе. Вместо этого используется ссылка на другую сущность, которую можно использовать напрямую. Эту ссылку можно использовать для последующего использования `GET` (это URL-адрес), следуя тому же шаблону, что и `self` ссылка.

Транскрипция v2:

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "models": [
    {
      "id": "827712a5-f942-4997-91c3-7c6cde35600b",
      "modelKind": "Language",
      "lastActionDateTime": "2019-01-07T11:36:07Z",
      "status": "Running",
      "createdDateTime": "2019-01-07T11:34:12Z",
      "locale": "en-US",
      "name": "Acoustic model",
      "description": "Example for an acoustic model",
      "datasets": [
        {
          "id": "702d913a-8ba6-4f66-ad5c-897400b081fb",
          "dataImportKind": "Language",
          "lastActionDateTime": "2019-01-07T11:36:07Z",
          "status": "Succeeded",
          "createdDateTime": "2019-01-07T11:34:12Z",
          "locale": "en-US",
          "name": "Language dataset",
          
        }
      ]
    },
  ]
}
```

v3 запись транскрипции:

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/021a72d0-54c4-43d3-8254-27336ead9037"
  }
}
```

Если необходимо использовать сведения о связанной модели, как показано в приведенном выше примере, упростите инструкцию GET `$.model.self` .

### <a name="retrieving-endpoint-logs"></a>Получение журналов конечной точки

Версия V2 службы поддерживала регистрацию ответов конечных точек. Чтобы получить результаты работы конечной точки с v2, необходимо создать "экспорт данных", который представляет моментальный снимок результатов, определенных диапазоном времени. Процесс экспорта пакетов данных становится негибким. API V3 предоставляет доступ к каждому отдельному файлу и позволяет выполнять итерацию по ним.

Конечная точка v3 успешно запущена:

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6",
  "links": {
    "logs": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs" 
  }
}
```

Ответ на получение `$.links.logs` :

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/6d72ad7e-f286-4a6f-b81b-a0532ca6bcaa/files/logs/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "name": "2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "kind": "Audio",
      "properties": {
        "size": 12345
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }    
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs?top=2&SkipToken=2!188!MDAwMDk1ITZhMjhiMDllLTg0MDYtNDViMi1hMGRkLWFlNzRlOGRhZWJkNi8yMDIwLTA0LTAxLzEyNDY0M182MzI5NGRkMi1mZGYzLTRhZmEtOTA0NC1mODU5ZTcxOWJiYzYud2F2ITAwMDAyOCE5OTk5LTEyLTMxVDIzOjU5OjU5Ljk5OTk5OTlaIQ--"
}
```

Разбиение на страницы для журналов конечных точек работает аналогично всем другим коллекциям, за исключением того, что смещение не может быть указано. Из-за большого объема доступных данных было реализовано разбиение на страницы, привязанное к серверу.

В версии 3 журнал каждого из конечных точек можно удалить по отдельности, выполнив команду DELETE для `self` файла или воспользовавшись командой delete On `$.links.logs` . Чтобы указать конечные данные, в `endDate` запрос можно добавить параметр запроса.

### <a name="using-custom-properties"></a>Использование свойств Custom

Чтобы отделить пользовательские свойства от необязательных свойств конфигурации, все явно именованные свойства теперь находятся в `properties` свойстве, а все свойства, определенные вызывающими объектами, теперь находятся в `customProperties` свойстве.

сущность транскрипции v2

```json
{
  "properties": {
    "customerDefinedKey": "value",
    "diarizationEnabled": "False",
    "wordLevelTimestampsEnabled": "False"
  }
}
```

сущность транскрипции v3

```json
{
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false 
  },
  "customProperties": {
    "customerDefinedKey": "value"
  }
}
```

Это изменение также включает использование правильных типов для всех явно именованных свойств в разделе `properties` (например, bool вместо String).

### <a name="response-headers"></a>Заголовки ответов

v3 больше не возвращает заголовок `Operation-Location` в дополнение к заголовку `Location` для запросов POST. Значения обоих заголовков используются одинаково. Теперь `Location` возвращается только.

Так как новая версия API теперь управляется службой управления API Azure (APIM), заголовки, связанные с регулированием `X-RateLimit-Limit` , `X-RateLimit-Remaining` и `X-RateLimit-Reset` не содержащиеся в заголовках ответа.

### <a name="accuracy-tests"></a>Проверки точности

Тесты точности были переименованы в оценки, так как новое имя описывает более подходящее представление. Пути к новостям: например, "https://{Region}. API., с помощью функции. Microsoft. com/спичтотекст/v 3.0/evaluations".
