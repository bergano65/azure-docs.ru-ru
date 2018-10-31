---
title: API пакетного транскрибирования в Azure
titlesuffix: Azure Cognitive Services
description: Примеры для транскрибирования больших объемов аудиосодержимого.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: e7523bf97d6252422ebb853b818453c935640f50
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49648808"
---
# <a name="batch-transcription"></a>Пакетное транскрибирование

Пакетное транскрибирование идеально подходит для использования с большим объемом аудиосодержимого в хранилище. Используя Rest API, вы можете указывать на звуковые файлы с помощью универсального кода ресурса (URI) SAS и получать транскрибирование в асинхронном режиме.

## <a name="batch-transcription-api"></a>API пакетного транскрибирования

API пакетного транскрибирования предоставляет асинхронное транскрибирование речи в текст и другие дополнительные возможности. Это REST API, предоставляющий методы для следующих задач:

1. Создание запросов пакетной обработки

2. Состояние запросов 

3. Скачивание транскрибирования

> [!NOTE]
> API пакетного транскрибирования идеально подходит для колл-центров, где обычно накапливаются тысячи часов разговоров. Философия API Fire & Forget (нажать и забыть) упрощает транскрибирование большого объема звукозаписей.

### <a name="supported-formats"></a>Поддерживаемые форматы

API пакетного транскрибирования поддерживает следующие форматы:

ИМЯ| Канал  |
----|----------|
MP3 |   Mono   |   
MP3 |  Stereo  | 
WAV |   Mono   |
WAV |  Stereo  |

Пакетное транскрибирование разделяет левый и правый каналы во время транскрибирования стереопотоков. Два файла JSON с результатом создаются из одного канала. Временные метки каждой фразы позволяют разработчику создавать упорядоченную окончательную расшифровку. В следующем примере JSON показаны выходные данные канала.

```json
       {
        "recordingsUrl": "https://mystorage.blob.core.windows.net/cris-e2e-datasets/TranscriptionsDataset/small_sentence.wav?st=2018-04-19T15:56:00Z&se=2040-04-21T15:56:00Z&sp=rl&sv=2017-04-17&sr=b&sig=DtvXbMYquDWQ2OkhAenGuyZI%2BYgaa3cyvdQoHKIBGdQ%3D",
        "resultsUrls": {
            "channel_0": "https://mystorage.blob.core.windows.net/bestor-87a0286f-304c-4636-b6bd-b3a96166df28/TranscriptionData/24265e4c-e459-4384-b572-5e3e7795221f?sv=2017-04-17&sr=b&sig=IY2qd%2Fkgtz2PwRe2C88BphH4Hv%2F1VCb1UVJ33xsw%2BEY%3D&se=2018-04-23T14:48:24Z&sp=r"
        },
        "statusMessage": "None.",
        "id": "0bb95356-ff06-469d-acc7-81f9144a269a",
        "createdDateTime": "2018-04-20T14:11:57.167",
        "lastActionDateTime": "2018-04-20T14:12:54.643",
        "status": "Succeeded",
        "locale": "en-US"
    },
```

> [!NOTE]
> API пакетного транскрибирования использует службу REST для запроса расшифровок, их состояния и связанных результатов. Вы можете использовать API для любого языка. В следующем разделе описано использование этого API.

## <a name="authorization-token"></a>Маркер авторизации

Как и для всех функций службы обработки речи, вам необходимо создать ключ подписки на[портале Azure](https://portal.azure.com), следуя [руководству по началу работы](get-started.md). Если вы планируете получать расшифровки из наших базовых моделей, то больше ничего делать не потребуется. 

Если вы планируете настроить и применить пользовательскую модель, необходимо добавить этот ключ подписки на портал распознавания речи следующим образом.

1. Войдите в службу [Пользовательское распознавание речи](https://customspeech.ai).

2. Выберите **Подписки**.

3. Выберите **Connect Existing Subscription** (Подключение к имеющейся подписке).

4. В открывшемся представлении добавьте ключ подписки и введите псевдоним.

    ![Снимок экрана подписок Пользовательской службы распознавания речи](media/stt/Subscriptions.jpg)

5. Скопируйте и вставьте ключ в клиентском коде, как показано в примере ниже.

> [!NOTE]
> Если вы планируете использовать пользовательскую модель, вам также потребуется ее идентификатор. Обратите внимание на то, что это не идентификатор конечной точки, который показан в представлении сведений о конечной точке. Это идентификатор модели, который можно получить, щелкнув пункт сведений об этой модели.

## <a name="sample-code"></a>Пример кода

В следующем примере кода нужно указать ключ подписки и ключ API. Этот пример позволяет получить токен носителя.

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

После получения этого токена следует указать универсальный код ресурса (URI) SAS, указывающий на звуковой файл, который нужно транскрибировать. Остальная часть кода итеративно получает состояние и отображает результаты. Изначально требуется настроить ключ, регион, используемые модели и сопоставление безопасности, как показано во фрагменте кода ниже. Затем создается экземпляр клиента и выполнение запроса POST. 

```cs
            private const string SubscriptionKey = "<your Speech subscription key>";
            private const string HostName = "westus.cris.ai";
            private const int Port = 443;
    
            // SAS URI 
            private const string RecordingsBlobUri = "some SAS URI";

            // adapted model Ids
            private static Guid AdaptedAcousticId = new Guid("some guid");
            private static Guid AdaptedLanguageId = new Guid("some guid");

            // Creating a Batch transcription API Client
            var client = CrisClient.CreateApiV2Client(SubscriptionKey, HostName, Port);
            
            var transcriptionLocation = await client.PostTranscriptionAsync(Name, Description, Locale, new Uri(RecordingsBlobUri), new[] { AdaptedAcousticId, AdaptedLanguageId }).ConfigureAwait(false);
```

Теперь, когда запрос выполнен, пользователь может запросить и скачать результаты транскрибирования, как показано во фрагменте кода.

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
                            // not creted form here, continue
                            continue;
                        }
                            
                        completed++;
                            
                        // if the transcription was successfull, check the results
                        if (transcription.Status == "Succeeded")
                        {
                            var resultsUri = transcription.ResultsUrls["channel_0"];
                            WebClient webClient = new WebClient();
                            var filename = Path.GetTempFileName();
                            webClient.DownloadFile(resultsUri, filename);
                            var results = File.ReadAllText(filename);
                            Console.WriteLine("Transcription succedded. Results: ");
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

Наш [документ Swagger](https://westus.cris.ai/swagger/ui/index) содержит подробные сведения об указанных выше вызовах. Полная версия показанного здесь примера находится на [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> Ключ подписки, указанный в предыдущем фрагменте кода, — это ключ из ресурса службы "Речь", который создается на портале Azure. Ключи, полученные из ресурса "Пользовательская служба распознавания речи", не будут работать.

Обратите внимание на асинхронную настройку для отправки аудио и получения состояния транскрибирования. Создан клиент .Net HTTP. Существует метод `PostTranscriptions` для отправки сведений об аудиофайле и метод `GetTranscriptions` для получения результатов. `PostTranscriptions` возвращает дескриптор, который `GetTranscriptions` использует для получения состояния транскрибирования.

Текущий пример кода не определяет какие-либо пользовательские модели. Служба использует базовые модели для расшифровки файлов. Чтобы указать модели, можно передать с тем же методом идентификаторы акустической и языковой моделей. 

Если вы не хотите использовать базовые модели, необходимо передать идентификаторы нужных моделей.

> [!NOTE]
> Для базового транскрибирования не нужно объявлять конечные точки базовых моделей. Если необходимо использовать пользовательские модели, нужно предоставить идентификаторы конечных точек, как в [примере](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI). Если необходимо использовать акустическую базовую модель с базовой языковой моделью, тогда нужно объявить только идентификатор конечной точки пользовательской модели. Система Майкрософт определит базовую модель партнера (акустическая или языковая) и использует ее для выполнения запроса на транскрибирование.

### <a name="supported-storage"></a>Поддерживаемое хранилище

В настоящее время поддерживается только хранилище BLOB-объектов Azure.

## <a name="downloading-the-sample"></a>Скачивание примера

Показанный здесь пример находится на [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> Обычно для аудиотранскрибирования требуется временной интервал равный длительности аудиофайла плюс 2–3 минуты.

## <a name="next-steps"></a>Дополнительная информация

* [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/)
