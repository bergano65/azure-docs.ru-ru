---
title: Что такое транскрипция пакетной службы — речь
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
ms.openlocfilehash: 46bfabfb2ccf091fd5dc0fcf0e9b447bad7c34d1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82208624"
---
# <a name="what-is-batch-transcription"></a>Что такое Пакетная транскрипция?

Запись пакетов — это набор REST API операций, позволяющих транскрипция большой объем аудио в хранилище. Вы можете указывать на звуковые файлы с помощью URI подписанного URL-кода (SAS) и асинхронно получать результаты транскрипции.

Асинхронное преобразование речи в текст — лишь одна из функций. Для вызова следующих методов можно использовать API-интерфейсы RESTFUL для записи пакетов.



|    Операция записи пакетов                                             |    Метод    |    Вызов REST API                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    Создает новую транскрипцию.                                              |    POST      |    API/спичтотекст/v 2.0/транскрипции            |
|    Извлекает список транскрипций для подписки, прошедшей проверку подлинности.    |    GET       |    API/спичтотекст/v 2.0/транскрипции            |
|    Возвращает список поддерживаемых языковых стандартов для автономных транскрипций.              |    GET       |    API/спичтотекст/v 2.0/транскрипции/языковые стандарты    |
|    Обновляет изменяемые сведения о транскрипции, определяемые его ИДЕНТИФИКАТОРом.    |    PATCH     |    API/спичтотекст/v 2.0/транскрипции/{ID}       |
|    Удаляет указанную задачу транскрипции.                                 |    DELETE    |    API/спичтотекст/v 2.0/транскрипции/{ID}       |
|    Возвращает транскрипцию, определяемую заданным ИДЕНТИФИКАТОРом.                        |    GET       |    API/спичтотекст/v 2.0/транскрипции/{ID}       |




Вы можете просматривать и тестировать подробный API, доступный в виде [документа Swagger](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A), под заголовком `Custom Speech transcriptions`.

Задания записи пакетов планируются в соответствии с оптимальными усилиями. В настоящее время не существует оценки, когда задание изменяется в состояние выполнения. В условиях обычной загрузки системы это должно происходить в течение нескольких минут. В состоянии выполнения фактическая транскрипция обрабатываются быстрее, чем в режиме реального времени.

Рядом с простым в использовании API не нужно развертывать пользовательские конечные точки, и у вас нет требований к параллелизму.

## <a name="prerequisites"></a>Предварительные требования

### <a name="subscription-key"></a>Ключ подписки

Как и для всех функций службы "Речь", вам необходимо создать ключ подписки на [портале Azure](https://portal.azure.com), следуя [руководству по началу работы](get-started.md).

>[!NOTE]
> Для использования записи пакетов требуется стандартная подписка (S0) для службы речи. Ключи бесплатной подписки (F0) не работают. Дополнительные сведения см. в разделе [цены и ограничения](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Настраиваемые модели

Если вы планируете настроить акустические или языковые модели, выполните действия, описанные в разделе [Настройка](how-to-customize-acoustic-models.md) моделей и модели [языка настройки](how-to-customize-language-model.md). Для использования созданных моделей в записи пакетной службы необходимы их идентификаторы моделей. Идентификатор модели можно получить при проверке подробностей модели. Развернутая Пользовательская конечная точка не требуется для службы транскрипции пакетов.

## <a name="the-batch-transcription-api"></a>API пакетного транскрибирования

### <a name="supported-formats"></a>Поддерживаемые форматы

API пакетного транскрибирования поддерживает следующие форматы:

| Формат | Кодек | Bitrate | Частота выборки                     |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16-разрядный  | 8 кГц или 16 кГц, моно или стерео |
| MP3    | PCM   | 16-разрядный  | 8 кГц или 16 кГц, моно или стерео |
| OGG    | OPUS  | 16-разрядный  | 8 кГц или 16 кГц, моно или стерео |

Для звуковых потоков стереозвука левый и правый каналы разбиваются во время транскрипции. Для каждого канала создается файл результатов JSON. Метки времени, созданные для каждого utterance, позволяют разработчику создавать упорядоченную окончательную запись.

### <a name="configuration"></a>Конфигурация

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
      `ProfanityFilterMode`
   :::column-end:::
   :::column span="2":::
      Указывает, как обрабатывать ненормативную лексику в результатах распознавания. Допустимые значения `None` : отключить фильтрацию ненормативных слов, `Masked` заменить ненормативную лексику звездочками, `Removed` удалить всю ненормативную лексику из результата или `Tags` добавить теги "Ненормативная лексика". Значение по умолчанию равно `Masked`.
:::row-end:::
:::row:::
   :::column span="1":::
      `PunctuationMode`
   :::column-end:::
   :::column span="2":::
      Указывает, как обрабатывать знаки препинания в результатах распознавания. Допустимые значения `None` — отключить пунктуацию `Dictated` , чтобы указать явный (произнесенный) `Automatic` знак пунктуации, разрешить декодеру использовать знаки `DictatedAndAutomatic` препинания или задиктовать и автоматически применять пунктуацию. Значение по умолчанию равно `DictatedAndAutomatic`.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddWordLevelTimestamps`
   :::column-end:::
   :::column span="2":::
      Указывает, следует ли добавлять к выводимым данным метки времени на уровне слова. Допустимые значения `true` : включение меток времени на уровне Word `false` и (значение по умолчанию) для отключения.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddSentiment`
   :::column-end:::
   :::column span="2":::
      Указывает, следует ли применять анализ тональности к utterance. Допустимые значения `true` : включить и `false` (значение по умолчанию), чтобы отключить его. Дополнительные сведения см. в разделе [Анализ тональности](#sentiment-analysis) .
:::row-end:::
:::row:::
   :::column span="1":::
      `AddDiarization`
   :::column-end:::
   :::column span="2":::
      Указывает, что анализ диаризатион должен выполняться во входных данных, который должен быть каналом Mono, содержащим два голоса. Допустимые значения `true` : включение диаризатион `false` и (значение по умолчанию) для отключения. Также `AddWordLevelTimestamps` необходимо установить значение true.
:::row-end:::
:::row:::
   :::column span="1":::
      `TranscriptionResultsContainerUrl`
   :::column-end:::
   :::column span="2":::
      Необязательный URL-адрес со [службой SAS](../../storage/common/storage-sas-overview.md) для записываемого контейнера в Azure. Результат сохраняется в этом контейнере.
:::row-end:::

### <a name="storage"></a>Память

Транскрипция пакетов поддерживает [хранилище BLOB-объектов Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) для чтения звука и записи записанных данных в хранилище.

## <a name="the-batch-transcription-result"></a>Результат транскрипции пакета

Для входного звука Mono создается один файл результатов для транскрипции. Для стерео входного аудио создаются два файла результатов для транскрипции. Каждый из них имеет следующую структуру:

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

:::row:::
   :::column span="1":::
      **Form**
   :::column-end:::
   :::column span="2":::
      **Содержимое**
:::row-end:::
:::row:::
   :::column span="1":::
      `Lexical`
   :::column-end:::
   :::column span="2":::
      Фактические слова распознаны.
:::row-end:::
:::row:::
   :::column span="1":::
      `ITN`
   :::column-end:::
   :::column span="2":::
      Обратная текстовая Нормализованная форма распознанного текста. Аббревиатуры ("врач Смит" в "Dr Smith"), Номера телефонов и другие преобразования применяются.
:::row-end:::
:::row:::
   :::column span="1":::
      `MaskedITN`
   :::column-end:::
   :::column span="2":::
      Форма восстановление с применением маскировки ненормативной лексики.
:::row-end:::
:::row:::
   :::column span="1":::
      `Display`
   :::column-end:::
   :::column span="2":::
      Отображаемая форма распознанного текста. Добавлены знаки препинания и прописные буквы.
:::row-end:::

## <a name="speaker-separation-diarization"></a>Разделение докладчика (Диаризатион)

Диаризатион — это процесс отделения динамиков от звука. Наш пакетный конвейер поддерживает диаризатион и способен распознать два динамика в записях каналов Mono. Эта функция недоступна в стерео записях.

Все выходные данные транскрипции содержат `SpeakerId`. Если диаризатион не используется, он отображается `"SpeakerId": null` в выходных данных JSON. Для диаризатион мы поддерживаем два голоса, поэтому динамики обозначены как `"1"` или `"2"`.

Чтобы запросить диаризатион, необходимо просто добавить соответствующий параметр в HTTP-запрос, как показано ниже.

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

Метки времени на уровне слов также должны быть включены в качестве параметров в приведенном выше запросе.

## <a name="sentiment-analysis"></a>Анализ мнений

Функция тональности оценивает тональности, выраженный в аудио. Тональности выражается значением от 0 до 1 для `Negative`, `Neutral`и `Positive` тональности. Например, анализ тональности можно использовать в сценариях центра обработки вызовов:

- Получите представление о удовлетворенности клиентов
- Получение сведений о производительности агентов (команда, принимающая вызовы)
- Найти точную точку во времени, когда вызов потратил отрицательное направление
- Что хорошо, когда отрицательный вызов переключается в положительное направление
- Указание клиентов и их отличий о продукте или службе

Тональности оценивается на каждом сегменте звука на основе лексической формы. Весь текст в этом сегменте аудио используется для вычисления тональности. Для всей транскрипции вычисление агрегатных тональности не выполняется. Анализ тональности в настоящее время доступен только на английском языке.

> [!NOTE]
> Вместо этого рекомендуется использовать API анализа текста Майкрософт. Она предоставляет более широкие возможности, помимо анализа тональности, таких как извлечение ключевых фраз, автоматическое определение языка и многое другое. Сведения и примеры можно найти в документации по [анализ текста](https://azure.microsoft.com/services/cognitive-services/text-analytics/).
>

Пример выходных данных JSON выглядит следующим образом:

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

Служба транскрипции может работать с большим количеством отправленных транскрипций. Вы можете запросить состояние транскрипций `GET` в [методе транскрипции](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions). Чтобы данные возвращались к разумному размеру, укажите `take` параметр (несколько сотен). Регулярно [удаляйте](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription) записи из службы после получения результатов. Это гарантирует быстрый ответ от вызовов управления транскрипциями.

## <a name="sample-code"></a>Образец кода

Полные примеры доступны в [репозитории примера GitHub](https://aka.ms/csspeech/samples) внутри `samples/batch` подкаталога.

Чтобы использовать настраиваемую акустическую или языковую модель, необходимо добавить в пример кода сведения о подписке, регион службы, URI SAS с указанием на аудиофайл для транскрибирования и идентификаторы моделей.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

В примере кода настраивается клиент и отправляется запрос на расшифровку. Затем он опрашивает сведения о состоянии и выводит сведения о ходе процесса транскрипции.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Подробные сведения о предыдущих вызовах см. в [документации по Swagger](https://westus.cris.ai/swagger/ui/index). Полная версия показанного здесь примера находится на [GitHub](https://aka.ms/csspeech/samples) в подкаталоге `samples/batch`.

Обратите внимание на асинхронную настройку для отправки аудио и получения состояния транскрибирования. Клиент, который вы создаете, является клиентом .NET HTTP. Существует метод `PostTranscriptions` для отправки сведений об аудиофайле и метод `GetTranscriptions` для получения результатов. `PostTranscriptions` возвращает дескриптор, который `GetTranscriptions` использует для создания дескриптора, чтобы получить состояние транскрибирования.

Текущий пример кода не определяет какие-либо пользовательские модели. Служба использует базовые модели для расшифровки файлов. Чтобы указать модели, можно передать с тем же методом идентификаторы акустической и языковой моделей.

> [!NOTE]
> Для базового транскрибирования не нужно объявлять идентификаторы базовых моделей. Если вы указали только идентификатор языковой модели (без идентификатора акустической модели), соответствующая акустическая модель выбирается автоматически. Если вы указали только идентификатор акустической модели, соответствующая языковая модель выбирается автоматически.

## <a name="download-the-sample"></a>Скачивание примера приложения

Пример можно скачать в [репозитории с примерами GitHub](https://aka.ms/csspeech/samples) в каталоге `samples/batch`.

## <a name="next-steps"></a>Следующие шаги

- [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/)
