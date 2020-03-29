---
title: Что такое партийная транскрипция - Речевая служба
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
ms.openlocfilehash: ee7fbddade055c11f5870aa5a588a2fd02f10a23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131604"
---
# <a name="what-is-batch-transcription"></a>Что такое партийная транскрипция?

Расшифровка пакетов — это набор операций REST API, который позволяет транскрибировать большое количество звука в хранилище. Вы можете указать на аудиофайлы с общей подписью доступа (SAS) URI и асинхронно получать результаты транскрипции.

Асинхронная транскрипция речи к тексту является лишь одной из особенностей. Для вызова следующих методов можно использовать пакетные транскрипционные aIS REST:



|    Операция по расшифровке партии                                             |    Метод    |    ВЫЗОВ REST API                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    Создает новую транскрипцию.                                              |    POST      |    api/speechtotext/v2.0/transcriptions            |
|    Извлекает список транскрипций для подлинной подписки.    |    GET       |    api/speechtotext/v2.0/transcriptions            |
|    Получает список поддерживаемых локатов для оффлайн транскрипций.              |    GET       |    api/speechtotext/v2.0/transcriptions/locales    |
|    Обновляет изменяемые детали транскрипции, идентифицированные по ее идентификатору.    |    PATCH     |    api/speechtotext/v2.0/транскрипции/       |
|    Удаляет заданную задачу транскрипции.                                 |    DELETE    |    api/speechtotext/v2.0/транскрипции/       |
|    Получает транскрипцию, идентифицированную по данному идентификатору.                        |    GET       |    api/speechtotext/v2.0/транскрипции/       |




Вы можете просмотреть и протестировать подробный API, который доступен `Custom Speech transcriptions`в качестве [документа Swagger,](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A)под заголовком .

Пакет транскрипции заданий запланированы на основе лучших усилий. В настоящее время нет оценки того, когда задание изменяется в запущенное состояние. При нормальной загрузке системы, это должно произойти в течение нескольких минут. Попав в запущенное состояние, фактическая транскрипция обрабатывается быстрее, чем аудио в режиме реального времени.

Рядом с простым в использовании API не нужно развертывать пользовательские конечные точки, и у вас нет каких-либо требований к параллелизму для наблюдения.

## <a name="prerequisites"></a>Предварительные требования

### <a name="subscription-key"></a>Ключ подписки

Как и для всех функций службы "Речь", вам необходимо создать ключ подписки на [портале Azure](https://portal.azure.com), следуя [руководству по началу работы](get-started.md).

>[!NOTE]
> Стандартная подписка (S0) на услугу Speech требуется для использования пакетной транскрипции. Бесплатные ключи подписки (F0) не работают. Для получения дополнительной информации см [цены и ограничения](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Настраиваемые модели

Если вы планируете настроить акустические или языковые модели, выполните последующие шаги в [настройке акустических моделей](how-to-customize-acoustic-models.md) и [моделей языка настройки дизайна.](how-to-customize-language-model.md) Для использования созданных моделей в пакетной транскрипции нужны их иноеды модели. При проверке деталей модели можно получить идентификатор модели. Развернутая пользовательская конечная точка не требуется для службы транскрипции пакетной транскрипции.

## <a name="the-batch-transcription-api"></a>API пакетного транскрибирования

### <a name="supported-formats"></a>Поддерживаемые форматы

API пакетного транскрибирования поддерживает следующие форматы:

| Формат | Кодек | Bitrate | Частота выборки                     |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16-разрядный  | 8 кГц или 16 кГц, моно или стерео |
| MP3    | PCM   | 16-разрядный  | 8 кГц или 16 кГц, моно или стерео |
| OGG    | OPUS  | 16-разрядный  | 8 кГц или 16 кГц, моно или стерео |

Для стерео аудио потоков, левые и правые каналы делятся во время транскрипции. Для каждого канала создается файл результатов JSON. Временные метки, генерируемые в высказывании, позволяют разработчику создать упорядоченную окончательную стенограмму.

### <a name="configuration"></a>Параметр Configuration

Параметры конфигурации указываются в формате JSON:

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to use, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "None | Removed | Tags | Masked",
    "PunctuationMode": "None | Dictated | Automatic | DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True | False",
    "AddSentiment" : "True | False",
    "AddDiarization" : "True | False",
    "TranscriptionResultsContainerUrl" : "<service SAS URI to Azure container to store results into (write permission required)>"
  }
}
```

### <a name="configuration-properties"></a>Свойства конфигурации

Используйте эти дополнительные свойства для настройки транскрипции:

:::row:::
   :::column span="1":::
      **Параметр**
   :::column-end:::
   :::column span="2":::
      **Описание**
:::row-end:::
:::row:::
   :::column span="1":::
      `ProfanityFilterMode`
   :::column-end:::
   :::column span="2":::
      Указывает, как обрабатывать ненормативную лексику в результатах распознавания. Принятые значения `None` заключаются в том, чтобы `Masked` отключить фильтрацию ненормативной `Removed` лексики, заменить ненормативную лексику звездочками, удалить всю ненормативную лексику из результата или `Tags` добавить метки «ненормативной лексики». Значение по умолчанию равно `Masked`.
:::row-end:::
:::row:::
   :::column span="1":::
      `PunctuationMode`
   :::column-end:::
   :::column span="2":::
      Указывает, как обрабатывать знаки препинания в результатах распознавания. Принятые значения `None` должны отклонять знаки препинания, `Dictated` подразумевать явные (говорят) знаки препинания, `DictatedAndAutomatic` `Automatic` позволить декодеру иметь дело с пунктуацией, или использовать продиктованные и автоматические знаки препинания. Значение по умолчанию равно `DictatedAndAutomatic`.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddWordLevelTimestamps`
   :::column-end:::
   :::column span="2":::
      Указывает, следует ли добавлять к выводимым данным метки времени на уровне слова. Принятые значения `true` должны включать метки `false` времени уровня слова и (значение по умолчанию) отключить его.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddSentiment`
   :::column-end:::
   :::column span="2":::
      Увеяет, следует ли применять анализ настроений к высказыванию. Принятые значения `true` должны `false` включить и (значение по умолчанию) отключить его.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddDiarization`
   :::column-end:::
   :::column span="2":::
      Уточняется, что анализ диаринизации должен проводиться на входе, который, как ожидается, будет моноканалом, содержащим два голоса. Принятые значения `true` позволяют диарационизации и `false` (значение по умолчанию) отключить его. Она также `AddWordLevelTimestamps` требует, чтобы быть установлен на истину.
:::row-end:::
:::row:::
   :::column span="1":::
      `TranscriptionResultsContainerUrl`
   :::column-end:::
   :::column span="2":::
      Дополнительный URL-адрес с [сервисом SAS](../../storage/common/storage-sas-overview.md) для записываемого контейнера в Azure. Результат хранится в этом контейнере.
:::row-end:::

### <a name="storage"></a>Хранилище

Транскрипция пакетов поддерживает [хранение Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) для чтения аудио и записи транскрипций для хранения.

## <a name="the-batch-transcription-result"></a>Результат расшифровки партии

Для моноводного аудио-сигнала создается один файл результатов транскрипции. Для стереовливого звука создаются два файла результатов транскрипции. Каждый из них имеет эту структуру:

```json
{
  "AudioFileResults":[
    {
      "AudioFileName": "Channel.0.wav | Channel.1.wav"      'maximum of 2 channels supported'
      "AudioFileUrl": null                                  'always null'
      "AudioLengthInSeconds": number                        'Real number. Two decimal places'
      "CombinedResults": [
        {
          "ChannelNumber": null                             'always null'
          "Lexical": string
          "ITN": string
          "MaskedITN": string
          "Display": string
        }
      ]
      SegmentResults:[                                      'for each individual segment'
        {
          "RecognitionStatus": "Success | Failure"
          "ChannelNumber": null
          "SpeakerId": null | "1 | 2"                       'null if no diarization
                                                             or stereo input file, the
                                                             speakerId as a string if
                                                             diarization requested for
                                                             mono audio file'
          "Offset": number                                  'time in ticks (1 tick is 100 nanosec)'
          "Duration": number                                'time in ticks (1 tick is 100 nanosec)'
          "OffsetInSeconds" : number                        'Real number. Two decimal places'
          "DurationInSeconds" : number                      'Real number. Two decimal places'
          "NBest": [
            {
              "Confidence": number                          'between 0 and 1'
              "Lexical": string
              "ITN": string
              "MaskedITN": string
              "Display": string
              "Sentiment":
                {                                           'this is omitted if sentiment is
                                                             not requested'
                  "Negative": number                        'between 0 and 1'
                  "Neutral": number                         'between 0 and 1'
                  "Positive": number                        'between 0 and 1'
                }
              "Words": [
                {
                  "Word": string
                  "Offset": number                          'time in ticks (1 tick is 100 nanosec)'
                  "Duration": number                        'time in ticks (1 tick is 100 nanosec)'
                  "OffsetInSeconds": number                 'Real number. Two decimal places'
                  "DurationInSeconds": number               'Real number. Two decimal places'
                  "Confidence": number                      'between 0 and 1'
                }
              ]
            }
          ]
        }
      ]
    }
  ]
}
```

Результат содержит следующие формы:

| Форма        | Содержимое                                                                                                                                                  |
|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| `Lexical`   | Фактические слова распознаны.                                                                                                                             |
| `ITN`       | Обратно-текст-нормализованная форма признанного текста. Применяются аббревиатуры ("доктор Смит" к "доктор укразить), номера телефонов и другие преобразования. |
| `MaskedITN` | Применена форма ITN с ненормативной лексикой.                                                                                                             |
| `Display`   | Форма отображения признанного текста. Включены добавленные знаки препинания и капитализация.                                                             |

## <a name="speaker-separation-diarization"></a>Разделение динамиков (Диаризация)

Диаверсия — это процесс разделения динамиков в аудио. Наш конвейер Batch поддерживает диаринацию и способен распознавать два динамика на записях моно-каналов. Функция недоступна на стереозаписях.

Все транскрипции `SpeakerId`выход содержит . Если диаринизация не используется, она отображается `"SpeakerId": null` на выходе JSON. Для диаринизации мы поддерживаем два голоса, `"1"` поэтому `"2"`динамики идентифицируются как или .

Чтобы запросить диаринацию, необходимо просто добавить соответствующий параметр в запрос http, как показано ниже.

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

Отметки времени на уровне слов также должны быть "включены", как указывают параметры в вышеуказанном запросе.

## <a name="sentiment-analysis"></a>Анализ тональности

Функция настроения оценивает настроения, выраженные в аудио. Чувство выражается значением между 0 `Negative`и `Neutral`1 `Positive` для , и настроения. Например, анализ настроений может быть использован в сценариях колл-центра:

- Получить представление об удовлетворенности клиентов
- Получить представление о производительности агентов (команда, принимая звонки)
- Найдите точную точку во времени, когда вызов принял поворот в негативном направлении
- Что прошло хорошо при превращении отрицательного вызова в позитивное направление
- Определите, что нравится клиентам и что им не нравится в продукте или услуге

Sentiment is scored per audio segment based on the lexical form. Весь текст в этом аудио сегменте используется для расчета настроения. Для всей транскрипции не рассчитывается агрегированное настроение. В настоящее время анализ настроений доступен только для английского языка.

Образец выхода JSON выглядит ниже:

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

## <a name="best-practices"></a>Рекомендации

Служба транскрипции может обрабатывать большое количество представленных транскрипций. Вы можете заразить состояние вашей транскрипции через `GET` метод [транскрипции.](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions) Храните информацию, возвращенную в `take` разумный размер, указав параметр (несколько сотен). [Регулярно удаляйте транскрипции](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription) из службы после получения результатов. Это гарантирует быстрые ответы от вызовов управления транскрипцией.

## <a name="sample-code"></a>Образец кода

Полные образцы доступны в [репозитории образцов GitHub](https://aka.ms/csspeech/samples) внутри `samples/batch` субдиректории.

> [!NOTE]
> Функциональность транскрипции пакетов разоблачается через описанный выше API REST. Таким образом, транскрипция пакета может быть использована практически из любого языка программирования или среды, которая поддерживает REST. Приведенные ниже примеры и примеры в GitHub являются лишь репрезентативными и **не** означают ограничений на то, где можно использовать API.

Чтобы использовать настраиваемую акустическую или языковую модель, необходимо добавить в пример кода сведения о подписке, регион службы, URI SAS с указанием на аудиофайл для транскрибирования и идентификаторы моделей.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

Образец кода настраивает клиента и отправляет запрос на транскрипцию. Затем он опросы для информации о состоянии и распечатать подробную информацию о ходе транскрипции.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Подробные сведения о предыдущих вызовах см. в [документации по Swagger](https://westus.cris.ai/swagger/ui/index). Полная версия показанного здесь примера находится на [GitHub](https://aka.ms/csspeech/samples) в подкаталоге `samples/batch`.

Обратите внимание на асинхронную настройку для отправки аудио и получения состояния транскрибирования. Клиент, который вы создаете, является клиентом .NET HTTP. Существует метод `PostTranscriptions` для отправки сведений об аудиофайле и метод `GetTranscriptions` для получения результатов. `PostTranscriptions` возвращает дескриптор, который `GetTranscriptions` использует для создания дескриптора, чтобы получить состояние транскрибирования.

Текущий пример кода не определяет какие-либо пользовательские модели. Служба использует базовые модели для расшифровки файлов. Чтобы указать модели, можно передать с тем же методом идентификаторы акустической и языковой моделей.

> [!NOTE]
> Для базового транскрибирования не нужно объявлять идентификаторы базовых моделей. Если вы указали только идентификатор языковой модели (без идентификатора акустической модели), соответствующая акустическая модель выбирается автоматически. Если вы указали только идентификатор акустической модели, соответствующая языковая модель выбирается автоматически.

## <a name="download-the-sample"></a>Скачивание примера приложения

Пример можно скачать в [репозитории с примерами GitHub](https://aka.ms/csspeech/samples) в каталоге `samples/batch`.

## <a name="next-steps"></a>Дальнейшие действия

- [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/)
