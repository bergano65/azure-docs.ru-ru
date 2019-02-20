---
title: Использование пакетного транскрибирования — Службы речи
titlesuffix: Azure Cognitive Services
description: Пакетное транскрибирование идеально подходит, если вам нужно расшифровать большой объем аудиоматериала в хранилище, например в хранилище BLOB-объектов Azure. Используя выделенный REST API, вы можете указывать аудиофайлы с помощью URI подписанного URL-адреса (SAS) и асинхронно получать транскрипции.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 0e03c388dac4a70fc45150287154406551ac2672
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867126"
---
# <a name="why-use-batch-transcription"></a>Преимущества пакетного транскрибирования

Пакетное транскрибирование идеально подходит, если вам нужно расшифровать большой объем аудиоматериала в хранилище, например в хранилище BLOB-объектов Azure. Используя выделенный REST API, вы можете указывать аудиофайлы с помощью URI подписанного URL-адреса (SAS) и асинхронно получать транскрипции.

>[!NOTE]
> Для использования пакетного транскрибирования необходима стандартная подписка (S0) для Служб речи. Ключи бесплатной подписки (F0) не подходят. Дополнительные сведения см. на странице с [ценами и ограничениями](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/speech-services/).

## <a name="the-batch-transcription-api"></a>API пакетного транскрибирования

API пакетного транскрибирования предоставляет асинхронное транскрибирование речи в текст и другие дополнительные возможности. Это REST API, предоставляющий методы для следующих задач:

1. Создание запросов пакетной обработки
1. Состояние запросов
1. Скачивание транскрипций

> [!NOTE]
> API пакетного транскрибирования идеально подходит для колл-центров, где обычно накапливаются тысячи часов аудио. Философия API Fire & Forget (нажать и забыть) упрощает транскрибирование большого объема звукозаписей.

### <a name="supported-formats"></a>Поддерживаемые форматы

API пакетного транскрибирования поддерживает следующие форматы:

| Формат | Кодек | Bitrate | Частота выборки |
|--------|-------|---------|-------------|
| WAV | PCM | 16-разрядный | 8 или 16 кГц, моно, стерео |
| MP3 | PCM | 16-разрядный | 8 или 16 кГц, моно, стерео |
| OGG | OPUS | 16-разрядный | 8 или 16 кГц, моно, стерео |

> [!NOTE]
> Для работы с API пакетного транскрибирования требуется ключ S0 (платная категория). Ключ f0 (бесплатная категория) не поддерживается.

API пакетного транскрибирования разделяет левый и правый каналы во время обработки стереопотоков. Два файла JSON с результатом создаются из одного канала. Временные метки каждой фразы позволяют разработчику создавать упорядоченную окончательную расшифровку. В следующем примере JSON показан пример запроса, включая свойства для настройки фильтра ненормативной лексики, знаки препинания и метки времени словесного уровня.

```json
{
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "models": [],
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "description": "An optional description of the transcription.",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True"
  },
```

> [!NOTE]
> API пакетного транскрибирования использует службу REST для запроса расшифровок, их состояния и связанных результатов. Вы можете использовать API для любого языка. В следующем разделе описывается, как используется API.

### <a name="query-parameters"></a>Параметры запроса

Следующие параметры можно включать в строку запроса REST.

| Параметр | ОПИСАНИЕ | Обязательный или необязательный |
|-----------|-------------|---------------------|
| `ProfanityFilterMode` | Указывает, как обрабатывать ненормативную лексику в результатах распознавания. Допустимые значения: `none` — отключает фильтрацию ненормативной лексики, `masked` — заменяет ненормативную лексику звездочками, `removed` — удаляет всю ненормативную лексику из результата, `tags` — добавляет теги, указывающие на ненормативную лексику. Значение по умолчанию — `masked`. | Необязательно |
| `PunctuationMode` | Указывает, как обрабатывать знаки препинания в результатах распознавания. Допустимые значения: `none` — отключает знаки препинания, `dictated` — обозначает явные знаки препинания, `automatic` — передает знаки препинания для определения декодеру, `dictatedandautomatic` — определяет продиктованные знаки препинания или передает их декодеру. | Необязательно |


## <a name="authorization-token"></a>Маркер авторизации

Как и для всех функций службы "Речь", вам необходимо создать ключ подписки на [портале Azure](https://portal.azure.com), следуя [руководству по началу работы](get-started.md). Если вы планируете получать расшифровки из наших базовых моделей, то вам нужно всего лишь создать ключ.

Если вы планируете настроить и применить пользовательскую модель, добавьте этот ключ подписки на портал распознавания речи следующим образом.

1. Войдите в службу [Пользовательское распознавание речи](https://customspeech.ai).

2. В правом верхнем углу щелкните **Подписки**.

3. Выберите **Connect existing subscription** (Подключение к имеющейся подписке).

4. Во всплывающем окне добавьте ключ подписки и введите псевдоним.

    ![Окно "Добавление подписки"](media/stt/Subscriptions.jpg)

5. Скопируйте и вставьте ключ в клиентском коде, как показано в примере ниже.

> [!NOTE]
> Если вы планируете использовать пользовательскую модель, вам также потребуется ее идентификатор. Это не идентификатор конечной точки, который показан в представлении сведений о конечной точке. Это идентификатор модели, который можно получить, щелкнув пункт сведений об этой модели.

## <a name="sample-code"></a>Пример кода

В следующем примере кода нужно указать ключ подписки и ключ API. Это действие позволит вам получить токен носителя.

```cs
     public static CrisClient CreateApiV2Client(string key, string hostName, int port)

        {
            var client = new HttpClient();
            client.Timeout = TimeSpan.FromMinutes(25);
            client.BaseAddress = new UriBuilder(Uri.UriSchemeHttps, hostName, port).Uri;
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

            return new CrisClient(client);
        }
```

Получив токен, укажите URI SAS, который указывает на звуковой файл, который требует транскрибирования. Остальная часть кода итеративно получает состояние и отображает результаты. Сначала настраиваются ключ, регион, модели и сопоставления безопасности, как показано в следующем фрагменте кода. Затем вы создаете экземпляр клиента и запрос POST.

```cs
            private const string SubscriptionKey = "<your Speech subscription key>";
            private const string HostName = "westus.cris.ai";
            private const int Port = 443;

            // SAS URI
            private const string RecordingsBlobUri = "SAS URI pointing to the file in Azure Blob Storage";

            // adapted model Ids
            private static Guid AdaptedAcousticId = new Guid("guid of the acoustic adaptation model");
            private static Guid AdaptedLanguageId = new Guid("guid of the language model");

            // Creating a Batch Transcription API Client
            var client = CrisClient.CreateApiV2Client(SubscriptionKey, HostName, Port);

            var transcriptionLocation = await client.PostTranscriptionAsync(Name, Description, Locale, new Uri(RecordingsBlobUri), new[] { AdaptedAcousticId, AdaptedLanguageId }).ConfigureAwait(false);
```

Сделав запрос, вы можете запросить и загрузить результаты транскрибирования, как показано в следующем фрагменте кода:

```cs

            // get all transcriptions for the user
            transcriptions = await client.GetTranscriptionAsync().ConfigureAwait(false);

            // for each transcription in the list we check the status
            foreach (var transcription in transcriptions)
            {
                switch(transcription.Status)
                {
                    case "Failed":
                    case "Succeeded":

                            // we check to see if it was one of the transcriptions we created from this client.
                        if (!createdTranscriptions.Contains(transcription.Id))
                        {
                            // not created from here, continue
                            continue;
                        }

                        completed++;

                        // if the transcription was successful, check the results
                        if (transcription.Status == "Succeeded")
                        {
                            var resultsUri = transcription.ResultsUrls["channel_0"];
                            WebClient webClient = new WebClient();
                            var filename = Path.GetTempFileName();
                            webClient.DownloadFile(resultsUri, filename);
                            var results = File.ReadAllText(filename);
                            Console.WriteLine("Transcription succeeded. Results: ");
                            Console.WriteLine(results);
                        }

                    break;
                    case "Running":
                    running++;
                     break;
                    case "NotStarted":
                    notStarted++;
                    break;

                    }
                }
            }
        }
```

Подробные сведения о предыдущих вызовах см. на странице [документа swagger](https://westus.cris.ai/swagger/ui/index). Полная версия показанного здесь примера находится на [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> Ключ подписки, указанный в предыдущем фрагменте кода, — это ключ из ресурса службы "Речь", который создается на портале Azure. Ключи, полученные из ресурса "Пользовательское распознавание речи", не будут работать.

Обратите внимание на асинхронную настройку для отправки аудио и получения состояния транскрибирования. Клиент, который вы создаете, является клиентом .NET HTTP. Существует метод `PostTranscriptions` для отправки сведений об аудиофайле и метод `GetTranscriptions` для получения результатов. `PostTranscriptions` возвращает дескриптор, который `GetTranscriptions` использует для создания дескриптора, чтобы получить состояние транскрибирования.

Текущий пример кода не определяет какие-либо пользовательские модели. Служба использует базовые модели для расшифровки файлов. Чтобы указать модели, можно передать с тем же методом идентификаторы акустической и языковой моделей.

Если вы не хотите использовать базовые модели, передайте идентификаторы нужных моделей.

> [!NOTE]
> Для базового транскрибирования не нужно объявлять конечные точки базовых моделей. Если необходимо использовать пользовательские модели, нужно предоставить идентификаторы конечных точек, как в [примере](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI). Если необходимо использовать акустическую базовую модель с базовой языковой моделью, тогда нужно только объявить идентификатор конечной точки пользовательской модели. Система Майкрософт определит базовую модель партнера (акустическая или языковая) и использует ее для выполнения запроса на транскрибирование.

### <a name="supported-storage"></a>Поддерживаемое хранилище

В настоящее время поддерживается только хранилище BLOB-объектов Azure.

## <a name="download-the-sample"></a>Скачивание примера приложения

Вы можете найти пример в этой статье на [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> МЫ не предоставляем временное Соглашение об уровне обслуживания для аудиотранскрипции с помощью пакета. Тем не менее, как только задание транскрипции запущено (переведено в рабочее состояние), оно выполняется быстрее, нежели в режиме реального времени.

## <a name="next-steps"></a>Дополнительная информация

* [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/)
