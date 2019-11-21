---
title: How to use Batch Transcription - Speech Service
titleSuffix: Azure Cognitive Services
description: Пакетное транскрибирование идеально подходит, если вам нужно расшифровать большой объем аудиоматериала в хранилище, например в хранилище BLOB-объектов Azure. Используя выделенный REST API, вы можете указывать аудиофайлы с помощью URI подписанного URL-адреса (SAS) и асинхронно получать транскрипции.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 5418b378c2c3cff09dbccbaa7b7240c61bbb583e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74221531"
---
# <a name="why-use-batch-transcription"></a>Преимущества пакетного транскрибирования

Пакетное транскрибирование идеально подходит, если вам нужно расшифровать большой объем аудиоматериала в хранилище, например в хранилище BLOB-объектов Azure. Используя выделенный REST API, вы можете указывать аудиофайлы с помощью URI подписанного URL-адреса (SAS) и асинхронно получать транскрипции.

## <a name="prerequisites"></a>Технические условия

### <a name="subscription-key"></a>Ключ подписки

Как и для всех функций службы "Речь", вам необходимо создать ключ подписки на [портале Azure](https://portal.azure.com), следуя [руководству по началу работы](get-started.md). Если вы планируете получать расшифровки из наших базовых моделей, то вам нужно всего лишь создать ключ.

>[!NOTE]
> Для использования пакетного транскрибирования необходима стандартная подписка (S0) для Служб речи. Ключи бесплатной подписки (F0) не подходят. Дополнительные сведения см. на странице с [ценами и ограничениями](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Настраиваемые модели

Чтобы настроить акустическую или языковую модель, следуйте инструкциям в статьях [Создание пользовательской акустической модели](how-to-customize-acoustic-models.md) и [Создание пользовательской языковой модели](how-to-customize-language-model.md). Чтобы использовать созданные модели при пакетном транскрибировании, необходимы идентификаторы моделей. Это не идентификатор конечной точки, указанный в представлении "Сведения о конечной точке", а идентификатор модели, который можно получить, щелкнув пункт "Сведения" для этой модели.

## <a name="the-batch-transcription-api"></a>API пакетного транскрибирования

API пакетного транскрибирования предоставляет асинхронное транскрибирование речи в текст и другие дополнительные возможности. Это REST API, предоставляющий методы для следующих задач:

1. Создание запросов пакетной обработки
1. Состояние запросов
1. Скачивание транскрипций

> [!NOTE]
> API пакетного транскрибирования идеально подходит для колл-центров, где обычно накапливаются тысячи часов аудио. Он упрощает транскрибирование больших объемов аудиозаписей.

### <a name="supported-formats"></a>Поддерживаемые форматы

API пакетного транскрибирования поддерживает следующие форматы:

| Формат | Кодек | Bitrate | Частота выборки |
|--------|-------|---------|-------------|
| WAV | PCM | 16-разрядный | 8 или 16 кГц, моно, стерео |
| MP3 | PCM | 16-разрядный | 8 или 16 кГц, моно, стерео |
| OGG | OPUS | 16-разрядный | 8 или 16 кГц, моно, стерео |

API пакетного транскрибирования разделяет левый и правый каналы во время обработки стереопотоков. Два файла JSON с результатом создаются из одного канала. Временные метки каждой фразы позволяют разработчику создавать упорядоченную окончательную расшифровку. Этот пример запроса включает свойства для фильтрации ненормативной лексики, пунктуации и меток времени на уровне слова.

### <a name="configuration"></a>Настройка

Параметры конфигурации указываются в формате JSON:

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to use, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True",
    "AddSentiment" : "True"
  }
}
```

> [!NOTE]
> API пакетного транскрибирования использует службу REST для запроса расшифровок, их состояния и связанных результатов. Вы можете использовать API для любого языка. В следующем разделе описывается, как используется API.

### <a name="configuration-properties"></a>Свойства конфигурации

Use these optional properties to configure transcription:

| Параметр | Описание |
|-----------|-------------|
| `ProfanityFilterMode` | Указывает, как обрабатывать ненормативную лексику в результатах распознавания. Допустимые значения: `None` — отключает фильтрацию ненормативной лексики, `masked` — заменяет ненормативную лексику звездочками, `removed` — удаляет всю ненормативную лексику из результата, `tags` — добавляет теги, указывающие на ненормативную лексику. Значение по умолчанию — `masked`. |
| `PunctuationMode` | Указывает, как обрабатывать знаки препинания в результатах распознавания. Допустимые значения: `None` — отключает знаки препинания, `dictated` — обозначает явные знаки препинания, `automatic` — передает знаки препинания для определения декодеру, `dictatedandautomatic` — определяет продиктованные знаки препинания или передает их декодеру. |
 | `AddWordLevelTimestamps` | Указывает, следует ли добавлять к выводимым данным метки времени на уровне слова. Допустимые значения: `true` — включает метки времени на уровне слова, `false` — (значение по умолчанию) отключает их. |
 | `AddSentiment` | Specifies sentiment should be added to the utterance. Accepted values are `true` which enables sentiment per utterance and `false` (the default value) to disable it. |
 | `AddDiarization` | Specifies that diarization analysis should be carried out on the input which is expected to be mono channel containing two voices. Accepted values are `true` which enables diarization and `false` (the default value) to disable it. It also requires `AddWordLevelTimestamps` to be set to true.|

### <a name="storage"></a>Storage

Batch transcription supports [Azure Blob storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) for reading audio and writing transcriptions to storage.

## <a name="webhooks"></a>Веб-перехватчики

Polling for transcription status may not be the most performant, or provide the best user experience. To poll for status, you can register callbacks, which will notify the client when long-running transcription tasks have completed.

For more details, see [Webhooks](webhooks.md).

## <a name="speaker-separation-diarization"></a>Speaker Separation (Diarization)

Diarization is the process of separating speakers in a piece of audio. Our Batch pipeline supports Diarization and is capable of recognizing two speakers on mono channel recordings.

To request that your audio transcription request is processed for diarization, you simply have to add the relevant parameter in the HTTP request as shown below.

 ```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to us, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "AddWordLevelTimestamps" : "True",
    "AddDiarization" : "True"
  }
}
```

Word level timestamps would also have to be 'turned on' as the parameters in the above request indicate.

The corresponding audio will contain the speakers identified by a number (currently we support only two voices, so the speakers will be identified as 'Speaker 1 'and 'Speaker 2') followed by the transcription output.

Also note that Diarization is not available in Stereo recordings. Furthermore, all JSON output will contain the Speaker tag. If diarization is not used, it will show 'Speaker: Null' in the JSON output.

> [!NOTE]
> Diarization is available in all regions and for all locales!

## <a name="sentiment"></a>Мнение

Sentiment is a new feature in Batch Transcription API and is an important feature in the call center domain. Customers can use the `AddSentiment` parameters to their requests to

1.  Get insights on customer satisfaction
2.  Get insight on the performance of the agents (team taking the calls)
3.  Pinpoint the exact point in time when a call took a turn in a negative direction
4.  Pinpoint what went well when turning negative calls to positive
5.  Identify what customers like and what they dislike about a product or a service

Sentiment is scored per audio segment where an audio segment is defined as the time lapse between the start of the utterance (offset) and the detection silence of end of byte stream. The entire text within that segment is used to calculate sentiment. We DO NOT calculate any aggregate sentiment values for the entire call or the entire speech of each channel. These aggregations are left to the domain owner to further apply.

Sentiment is applied on the lexical form.

A JSON output sample looks like below:

```json
{
  "AudioFileResults": [
    {
      "AudioFileName": "Channel.0.wav",
      "AudioFileUrl": null,
      "SegmentResults": [
        {
          "RecognitionStatus": "Success",
          "ChannelNumber": null,
          "Offset": 400000,
          "Duration": 13300000,
          "NBest": [
            {
              "Confidence": 0.976174,
              "Lexical": "what's the weather like",
              "ITN": "what's the weather like",
              "MaskedITN": "what's the weather like",
              "Display": "What's the weather like?",
              "Words": null,
              "Sentiment": {
                "Negative": 0.206194,
                "Neutral": 0.793785,
                "Positive": 0.0
              }
            }
          ]
        }
      ]
    }
  ]
}
```
The feature uses a Sentiment model, which is currently in Beta.

## <a name="sample-code"></a>Пример кода

Complete samples are available in the [GitHub sample repository](https://aka.ms/csspeech/samples) inside the `samples/batch` subdirectory.

Чтобы использовать настраиваемую акустическую или языковую модель, необходимо добавить в пример кода сведения о подписке, регион службы, URI SAS с указанием на аудиофайл для транскрибирования и идентификаторы моделей.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

Пример кода настроит клиент и отправит запрос на транскрибирование. Затем он запросит информацию о состоянии и выведет сведения о ходе выполнения транскрибирования.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Подробные сведения о предыдущих вызовах см. в [документации по Swagger](https://westus.cris.ai/swagger/ui/index). Полная версия показанного здесь примера находится на [GitHub](https://aka.ms/csspeech/samples) в подкаталоге `samples/batch`.

Обратите внимание на асинхронную настройку для отправки аудио и получения состояния транскрибирования. Клиент, который вы создаете, является клиентом .NET HTTP. Существует метод `PostTranscriptions` для отправки сведений об аудиофайле и метод `GetTranscriptions` для получения результатов. `PostTranscriptions` возвращает дескриптор, который `GetTranscriptions` использует для создания дескриптора, чтобы получить состояние транскрибирования.

Текущий пример кода не определяет какие-либо пользовательские модели. Служба использует базовые модели для расшифровки файлов. Чтобы указать модели, можно передать с тем же методом идентификаторы акустической и языковой моделей.

> [!NOTE]
> Для базового транскрибирования не нужно объявлять идентификаторы базовых моделей. Если вы указали только идентификатор языковой модели (без идентификатора акустической модели), соответствующая акустическая модель выбирается автоматически. Если вы указали только идентификатор акустической модели, соответствующая языковая модель выбирается автоматически.

## <a name="download-the-sample"></a>Скачивание примера приложения

Пример можно скачать в [репозитории с примерами GitHub](https://aka.ms/csspeech/samples) в каталоге `samples/batch`.

> [!NOTE]
> Задания пакетного транскрибирования планируются на условиях максимально возможного выполнения запросов, при этом время запуска задания не прогнозируется. После запуска транскрибирование обрабатывается быстрее, чем аудиоматериал в реальном времени.

## <a name="next-steps"></a>Дальнейшие действия

* [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/)
