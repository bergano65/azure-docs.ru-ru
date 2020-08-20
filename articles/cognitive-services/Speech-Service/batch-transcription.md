---
title: Как использовать транскрипции пакетной службы — речь
titleSuffix: Azure Cognitive Services
description: Пакетное транскрибирование идеально подходит, если вам нужно расшифровать большой объем аудиоматериала в хранилище, например в хранилище BLOB-объектов Azure. Используя выделенный REST API, вы можете указывать аудиофайлы с помощью URI подписанного URL-адреса (SAS) и асинхронно получать транскрипции.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: wolfma
ms.openlocfilehash: df1266070e9fb69ec94811a3120412d9b238e470
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88640163"
---
# <a name="how-to-use-batch-transcription"></a>Использование записи пакетов

Запись пакетов — это набор REST API операций, позволяющих транскрипция большой объем аудио в хранилище. Вы можете указывать на звуковые файлы с помощью URI подписанного URL-кода (SAS) и асинхронно получать результаты транскрипции. Новый API v 3.0 позволяет выбрать фотографировать один или несколько звуковых файлов или обработать весь контейнер хранилища.

Асинхронное преобразование речи в текст — лишь одна из функций. Для вызова следующих методов можно использовать API-интерфейсы RESTFUL для записи пакетов.

|    Операция записи пакетов                                             |    Метод    |    Вызов REST API                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    Создает новую транскрипцию.                                              |    POST      |    спичтотекст/v 3.0/транскрипции            |
|    Извлекает список транскрипций для подписки, прошедшей проверку подлинности.    |    GET       |    спичтотекст/v 3.0/транскрипции            |
|    Возвращает список поддерживаемых языковых стандартов для автономных транскрипций.              |    GET       |    спичтотекст/v 3.0/транскрипции/языковые стандарты    |
|    Обновляет изменяемые сведения о транскрипции, определяемые его ИДЕНТИФИКАТОРом.    |    PATCH     |    спичтотекст/v 3.0/транскрипции/{ID}       |
|    Удаляет указанную задачу транскрипции.                                 |    DELETE    |    спичтотекст/v 3.0/транскрипции/{ID}       |
|    Возвращает транскрипцию, определяемую заданным ИДЕНТИФИКАТОРом.                        |    GET       |    спичтотекст/v 3.0/транскрипции/{ID}       |
|    Возвращает файлы результатов транскрипции, идентифицируемые по заданному ИДЕНТИФИКАТОРу.    |    GET       |    спичтотекст/v 3.0/транскрипции/{ID}/файлов |




Вы можете просматривать и тестировать подробный API, который доступен в виде [документа Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0).

Задания записи пакетов планируются в соответствии с оптимальными усилиями. В настоящее время не существует оценки, когда задание изменяется в состояние выполнения. В условиях обычной загрузки системы это должно происходить в течение нескольких минут. В состоянии выполнения фактическая транскрипция обрабатываются быстрее, чем в режиме реального времени.

Рядом с простым в использовании API не нужно развертывать пользовательские конечные точки, и у вас нет требований к параллелизму.

## <a name="prerequisites"></a>Предварительные требования

Как и для всех функций службы "Речь", вам необходимо создать ключ подписки на [портале Azure](https://portal.azure.com), следуя [руководству по началу работы](get-started.md).

>[!NOTE]
> Для использования записи пакетов требуется стандартная подписка (S0) для службы речи. Ключи бесплатной подписки (F0) не работают. Дополнительные сведения см. в разделе [цены и ограничения](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Если вы планируете настроить модели, выполните действия, описанные в разделе [Настройка акустической настройки](how-to-customize-acoustic-models.md) и [Настройка языка](how-to-customize-language-model.md). Для использования созданных моделей в записи пакетной службы требуется расположение их модели. Расположение модели можно получить при проверке сведений о модели ( `self` свойство). Развернутая Пользовательская конечная точка *не требуется* для службы транскрипции пакетов.

## <a name="batch-transcription-api"></a>API пакетного транскрибирования

API-интерфейс для транскрипции пакетов поддерживает следующие форматы:

| Формат | Кодек | Бит на выборку | Частота выборки             |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16-разрядный  | 8 кГц или 16 кГц, моно или стерео |
| MP3    | PCM   | 16-разрядный  | 8 кГц или 16 кГц, моно или стерео |
| OGG    | OPUS  | 16-разрядный  | 8 кГц или 16 кГц, моно или стерео |

Для звуковых потоков стереозвука левый и правый каналы разбиваются во время транскрипции. Для каждого канала создается файл результатов JSON. Метки времени, созданные для каждого utterance, позволяют разработчику создавать упорядоченную окончательную запись.

### <a name="configuration"></a>Конфигурация

Параметры конфигурации предоставляются в виде JSON (один или несколько отдельных файлов):

```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

Параметры конфигурации предоставляются в виде JSON (обработка всего контейнера хранилища):

```json
{
  "contentContainerUrl": "<SAS URL to the Azure blob container to transcribe>",
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of container using default model for en-US"
}
```

Чтобы использовать пользовательские обученные модели в пакетных транскрипциях, можно ссылаться на них, как показано ниже:

```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}"
  },
  "displayName": "Transcription of file using default model for en-US"
}
```


### <a name="configuration-properties"></a>Свойства конфигурации

Используйте эти необязательные свойства для настройки транскрипции:

:::row:::
   :::column span="1":::
      **Параметр**
   :::column-end:::
   :::column span="2":::
      **Описание**
:::row-end:::
:::row:::
   :::column span="1":::
      `profanityFilterMode`
   :::column-end:::
   :::column span="2":::
      Указывает, как обрабатывать ненормативную лексику в результатах распознавания. Допустимые значения: `None` отключить фильтрацию ненормативных слов, заменить ненормативную лексику `Masked` звездочками, `Removed` удалить всю ненормативную лексику из результата или `Tags` Добавить теги "Ненормативная лексика". Значение по умолчанию — `Masked`.
:::row-end:::
:::row:::
   :::column span="1":::
      `punctuationMode`
   :::column-end:::
   :::column span="2":::
      Указывает, как обрабатывать знаки препинания в результатах распознавания. Допустимые значения — `None` Отключить пунктуацию, `Dictated` чтобы указать явный (произнесенный) знак пунктуации, `Automatic` Разрешить декодеру использовать знаки препинания или `DictatedAndAutomatic` задиктовать и автоматически применять пунктуацию. Значение по умолчанию — `DictatedAndAutomatic`.
:::row-end:::
:::row:::
   :::column span="1":::
      `wordLevelTimestampsEnabled`
   :::column-end:::
   :::column span="2":::
      Указывает, следует ли добавлять к выводимым данным метки времени на уровне слова. Допустимые значения: `true` Включение меток времени на уровне Word и `false` (значение по умолчанию) для отключения.
:::row-end:::
:::row:::
   :::column span="1":::
      `diarizationEnabled`
   :::column-end:::
   :::column span="2":::
      Указывает, что анализ диаризатион должен выполняться во входных данных, который должен быть каналом Mono, содержащим два голоса. Допустимые значения: `true` Включение диаризатион и `false` (значение по умолчанию) для отключения. Также необходимо `wordLevelTimestampsEnabled` установить значение true.
:::row-end:::
:::row:::
   :::column span="1":::
      `channels`
   :::column-end:::
   :::column span="2":::
      Необязательный массив номеров каналов для обработки. Здесь подмножество доступных каналов в звуковом файле можно указать для обработки (например, `0` только). Если не указано, каналы `0` и `1` являются расшифрованной по умолчанию.
:::row-end:::
:::row:::
   :::column span="1":::
      `timeToLive`
   :::column-end:::
   :::column span="2":::
      Необязательная длительность автоматического удаления записей после завершения транскрипции. Параметр `timeToLive` полезен при обходе массовой обработки, чтобы убедиться в их окончательном удалении (например, `PT12H` ). Если параметр не указан или имеет значение `PT0H` , то транскрипция не будет автоматически удалена.
:::row-end:::
:::row:::
   :::column span="1":::
      `destinationContainerUrl`
   :::column-end:::
   :::column span="2":::
      Необязательный URL-адрес со [службой SAS](../../storage/common/storage-sas-overview.md) для записываемого контейнера в Azure. Результат сохраняется в этом контейнере. Если этот параметр не указан, корпорация Майкрософт сохраняет результаты в контейнере хранилища, который управляется корпорацией Майкрософт. При удалении транскрипции путем вызова функции [удаления](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription)записей результирующие данные также будут удалены.
:::row-end:::

### <a name="storage"></a>Память

Транскрипция пакетов поддерживает [хранилище BLOB-объектов Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) для чтения звука и записи записанных данных в хранилище.

## <a name="batch-transcription-result"></a>Результат транскрипции пакета

Для каждого входного звука создается один файл результатов для транскрипции. Список файлов результатов можно получить, вызвав [файл Get транскрипции](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptionFiles). Этот метод возвращает список файлов результатов для этого транскрипции. Чтобы найти файл транскрипции для определенного входного файла, отфильтруйте все возвращенные файлы с помощью `kind`  ==  `Transcription` и `name`  ==  `{originalInputName.suffix}.json` .

Каждый файл результатов транскрипции имеет следующий формат:

```json
{
  "source": "...",                                                 // the sas url of a given contentUrl or the path relative to the root of a given container
  "timestamp": "2020-06-16T09:30:21Z",                             // creation time of the transcription, ISO 8601 encoded timestamp, combined date and time
  "durationInTicks": 41200000,                                     // total audio duration in ticks (1 tick is 100 nanoseconds)
  "duration": "PT4.12S",                                           // total audio duration, ISO 8601 encoded duration
  "combinedRecognizedPhrases": [                                   // concatenated results for simple access in single string for each channel
    {
      "channel": 0,                                                // channel number of the concatenated results
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                                           // results for each phrase and each channel individually
    {
      "recognitionStatus": "Success",                              // recognition state, e.g. "Success", "Failure"
      "channel": 0,                                                // channel number of the result
      "offset": "PT0.07S",                                         // offset in audio of this phrase, ISO 8601 encoded duration 
      "duration": "PT1.59S",                                       // audio duration of this phrase, ISO 8601 encoded duration
      "offsetInTicks": 700000.0,                                   // offset in audio of this phrase in ticks (1 tick is 100 nanoseconds)
      "durationInTicks": 15900000.0,                               // audio duration of this phrase in ticks (1 tick is 100 nanoseconds)
      
      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,                               // confidence value for the recognition of the whole phrase
          "speaker": 1,                                            // if `diarizationEnabled` is `true`, this is the identified speaker (1 or 2), otherwise this property is not present
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",
          
          // if wordLevelTimestampsEnabled is `true`, there will be a result for each word of the phrase, otherwise this property is not present
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

Результат содержит следующие формы:

:::row:::
   :::column span="1":::
      **Form**
   :::column-end:::
   :::column span="2":::
      **Содержимое**
:::row-end:::
:::row:::
   :::column span="1":::
      `lexical`
   :::column-end:::
   :::column span="2":::
      Фактические слова распознаны.
:::row-end:::
:::row:::
   :::column span="1":::
      `itn`
   :::column-end:::
   :::column span="2":::
      Обратная текстовая Нормализованная форма распознанного текста. Аббревиатуры ("врач Смит" в "Dr Smith"), Номера телефонов и другие преобразования применяются.
:::row-end:::
:::row:::
   :::column span="1":::
      `maskedITN`
   :::column-end:::
   :::column span="2":::
      Форма восстановление с применением маскировки ненормативной лексики.
:::row-end:::
:::row:::
   :::column span="1":::
      `display`
   :::column-end:::
   :::column span="2":::
      Отображаемая форма распознанного текста. Добавлены знаки препинания и прописные буквы.
:::row-end:::

## <a name="speaker-separation-diarization"></a>Разделение докладчика (диаризатион)

Диаризатион — это процесс отделения динамиков от звука. Конвейер пакетной обработки поддерживает диаризатион и может распознать два динамика в записях каналов Mono. Эта функция недоступна в стерео записях.

Выходные данные транскрипции с включенным диаризатион содержат `Speaker` запись для каждой расшифрованной фразы. Если диаризатион не используется, свойство отсутствует `Speaker` в выходных данных JSON. Для диаризатион мы поддерживаем два голоса, поэтому динамики обозначены как `1` или `2` .

Чтобы запросить диаризатион, необходимо просто добавить соответствующий параметр в HTTP-запрос, как показано ниже.

 ```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "diarizationEnabled": true,
    "wordLevelTimestampsEnabled": true,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

Метки времени на уровне слов должны быть включены, так как параметры в приведенном выше запросе указывают.

## <a name="best-practices"></a>Рекомендации

Служба транскрипции пакетов может работать с большим количеством отправленных транскрипций. Вы можете запросить состояние ваших транскрипций, используя `GET` для [получения транскрипции](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptions). Регулярно вызывайте функцию [транскрипции удаления](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription) из службы после получения результатов. В качестве альтернативы можно задать `timeToLive` для свойства разумное значение, чтобы обеспечить окончательное удаление результатов.

## <a name="sample-code"></a>Образец кода

Полные примеры доступны в [репозитории примера GitHub](https://aka.ms/csspeech/samples) внутри `samples/batch` подкаталога.

Обновите пример кода, указав сведения о подписке, регион службы, URI SAS, указывающий на звуковой файл, в транскрипция и расположение модели на случай, если вы хотите использовать пользовательскую модель.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptiondefinition)]

В примере кода настраивается клиент и отправляется запрос на расшифровку. Затем он опрашивает сведения о состоянии и выводит сведения о ходе процесса транскрипции.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptionstatus)]

Подробные сведения о предыдущих вызовах см. в [документации по Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0). Полная версия показанного здесь примера находится на [GitHub](https://aka.ms/csspeech/samples) в подкаталоге `samples/batch`.

Обратите внимание на асинхронную настройку для отправки аудио и получения состояния транскрибирования. Клиент, который вы создаете, является клиентом .NET HTTP. Существует `PostTranscriptions` метод отправки сведений о звуковом файле и `GetTranscriptions` метод получения состояний. `PostTranscriptions` возвращает дескриптор, который `GetTranscriptions` использует для создания дескриптора, чтобы получить состояние транскрибирования.

Текущий пример кода не определяет какие-либо пользовательские модели. Служба использует базовую модель для фотографировать файлов или файлов. Чтобы указать модель, можно передать в тот же метод ссылку на модель для настраиваемой модели.

> [!NOTE]
> Для транскрипций базовых показателей не нужно объявлять идентификатор для базовой модели.

## <a name="download-the-sample"></a>Скачивание примера приложения

Пример можно скачать в [репозитории с примерами GitHub](https://aka.ms/csspeech/samples) в каталоге `samples/batch`.

## <a name="next-steps"></a>Дальнейшие действия

- [Справочник по API для преобразования речи в текст v3](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription)
