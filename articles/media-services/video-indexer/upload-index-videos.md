---
title: Отправка и индексирование видео с помощью Индексатора видео
titleSuffix: Azure Media Services
description: В этой статье показано, как использовать API для отправки и индексирования видео с помощью Индексатора видео.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/29/2019
ms.author: juliako
ms.openlocfilehash: d06be1b5301889a1fcb8ff1390d8618bbb88c03f
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666483"
---
# <a name="upload-and-index-your-videos"></a>Отправка и индексирование видео  

API Индексатора видео позволяет загружать видео двумя способами: 

* загрузка видео из URL-адреса (предпочтительно);
* отправка видеофайла в виде массива байтов в тексте запроса;
* Используйте существующий ресурс-контейнер служб мультимедиа Azure, указав [идентификатор ресурса](https://docs.microsoft.com/azure/media-services/latest/assets-concept) (поддерживается только в платных учетных записях).

В этой статье показано, как использовать API [отправки видео](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) для отправки и индексирования видео с помощью URL-адреса. В примере в этой статье закомментирована часть кода, где показано, как загрузить байтовый массив. <br/>Кроме того, в этой статье рассматриваются некоторые из параметров, которые можно настроить в API, чтобы изменить процесс и выходные данные API.

После отправки видео при необходимости можно его кодировать с помощью Индексатора видео. При создании учетной записи Индексатора видео можно выбрать бесплатную пробную учетную запись (с определенным количеством бесплатных минут индексирования) или платную (без ограничивающих квот). В бесплатной пробной учетной записи Индексатора видео предоставляется до 600 минут бесплатной индексации для пользователей веб-сайта и до 2400 минут бесплатной индексации для пользователей API. Платный вариант подразумевает создание учетной записи Индексатора видео, [подключенной к подписке Azure и учетной записи Служб мультимедиа Azure](connect-to-azure.md). Вы платите за минуты индексирования, а также за использование учетной записи Служб мультимедиа. 

## <a name="uploading-considerations-and-limitations"></a>Отправка рекомендаций и ограничений
 
- Имя видео должно содержать не более 80 символов.
- При отправке видео на основе URL-адреса (предпочтительно) конечная точка должна быть защищена с помощью TLS 1,2 (или более поздней версии).
- Размер передачи с параметром URL-адреса ограничен 30 ГБ.
- Длина URL-адреса запроса ограничена 6144 символами, в которых длина URL-адреса строки запроса ограничена 4096 символами.
- Размер передачи с параметром массива байтов ограничен 2 ГБ.
- Время ожидания параметра массива байтов истекло через 30 мин.
- URL-адрес, указанный в параметре `videoURL`, должен быть закодирован.
- Индексирование ресурсов служб мультимедиа имеет то же ограничение, что и индексация URL-адреса.
- Максимальный срок действия индексатора видео составляет 4 часа для одного файла.

> [!Tip]
> Рекомендуем использовать .NET Framework 4.6.2 или более поздней версии, так как для более ранних версий .NET Framework протокол TLS 1.2 не используется по умолчанию.
>
> Если нужно использовать более ранние версии .NET Framework, прежде чем вызывать REST API, добавьте в код следующую строку:  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="configurations-and-params"></a>Конфигурации и параметры

В этом разделе описываются некоторые необязательные параметры и случаи, когда может потребоваться их настройка.

### <a name="externalid"></a>externalID 

С помощью этого параметра можно указать идентификатор, который будет связан с видео. Идентификатор может применяться к интеграции с внешней системой управления содержимым видео. Видео на портале Индексатора видео можно найти с помощью указанного внешнего идентификатора.

### <a name="callbackurl"></a>callbackUrl

URL-адрес, используемый для уведомления клиента (с помощью запроса POST) о текущих событиях:

- Изменение состояния индексирования: 
    - Свойства:    
    
        |Name|Описание|
        |---|---|
        |id|Идентификатор видео|
        |state|Состояние видео|  
    - Пример: https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&state=Processed
- Человек, идентифицированный на видео:
  - properties
    
      |Name|Описание|
      |---|---|
      |id| Идентификатор видео|
      |faceId|Идентификатор лица, которое отображается в индексе видео|
      |knownPersonId|Идентификатор человека, который имеет уникальную модель лица|
      |personName|Имя человека|
        
    - Пример: https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&faceid=12&knownPersonId=CCA84350-89B7-4262-861C-3CAC796542A5&personName=Inigo_Montoya 

#### <a name="notes"></a>Заметки

- Индексатор видео возвращает любые существующие параметры, указанные в исходном URL-адресе.
- Указанный URL-адрес должен быть закодирован.

### <a name="indexingpreset"></a>indexingPreset

Используйте этот параметр, если необработанные или внешние записи содержат фоновый шум. Этот параметр используется для настройки процесса индексирования. Можно указать следующие значения:

- `AudioOnly` — индексирование и извлечение аналитических сведений с помощью только аудио (без видео).
- `Default` — индексирование и извлечение аналитических сведений с помощью аудио и видео.
- `DefaultWithNoiseReduction` — индексирование и извлечение аналитических сведений из аудио и видео с применением алгоритмов снижения шума в аудиопотоке.
- `VideoOnly` — индексирование и извлечение аналитических данных с использованием только видео 

Цена зависит от выбранного параметра индексирования.  

### <a name="priority"></a>priority

Видео индексируются службой "Индексатор видео" согласно их приоритету. Используйте параметр **priority**, чтобы указать приоритет индексирования. Допустимы следующие значения: **Low** (Низкий), **Normal** (Нормальный, по умолчанию) и **High** (Высокий).

Параметр **Priority** (Приоритет) поддерживается только для платных учетных записей.

### <a name="streamingpreset"></a>streamingPreset

После отправки видео при необходимости кодируется Индексатором видео. Затем выполняется индексирование и анализ видео. Когда Индексатор видео завершит анализ, вы получите уведомление с идентификатором видео.  

При использовании API [отправки видео](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) или [повторной индексации видео](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) одним из необязательных параметров является `streamingPreset`. Если для параметра `streamingPreset` задать значение `Default`, `SingleBitrate` или `AdaptiveBitrate`, запускается процесс кодирования. После выполнения заданий индексирования и кодирования видео будет опубликовано, и вы также сможете осуществлять его потоковую передачу. Конечная точка потоковой передачи, из которой нужно передавать видео потоком, должна находиться в состоянии **Выполняется**.

Чтобы выполнить задания индексирования и кодирования, [учетной записи служб мультимедиа Azure, подключенной к вашей учетной записи Индексатора видео](connect-to-azure.md), требуются зарезервированные единицы. Дополнительные сведения см. в статье [Обзор масштабирования обработки мультимедиа](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview). Так как эти задания предусматривают ресурсоемкие вычисления, настоятельно рекомендуется использовать единицу типа S3. Количество зарезервированных единиц определяет максимальное количество заданий, которые могут выполняться параллельно. Мы рекомендуем использовать не менее 10 зарезервированных единиц типа S3. 

Если вы собираетесь только проиндексировать свое видео без кодирования, задайте для параметра `streamingPreset` значение `NoStreaming`.

### <a name="videourl"></a>videoUrl

URL-адрес индексируемого видео- или аудиофайла. URL-адрес должен указывать на файл мультимедиа (HTML-страницы не поддерживаются). Файл может быть защищен маркером доступа, предоставленным в составе универсального кода ресурса (URI), а конечная точка, обрабатывающая файл, должна быть защищена с использованием протокола TLS версии 1.2 или выше. URL-адрес нужно закодировать. 

Если параметр `videoUrl` не указан, Индексатор видео ожидает, что вы передадите этот файл в виде содержимого текста формы или составного содержимого.

## <a name="code-sample"></a>Пример кода

В следующем фрагменте кода C# показано, как использовать все API Индексатора видео.

```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var location = "westus2";
    var apiKey = "...";

    System.Net.ServicePointManager.SecurityProtocol =
        System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

    // create the http client
    var handler = new HttpClientHandler();
    handler.AllowAutoRedirect = false;
    var client = new HttpClient(handler);
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);

    // obtain account information and access token
    string queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"generateAccessTokens", "true"},
            {"allowEdit", "true"},
        });
    HttpResponseMessage result = await client.GetAsync($"{apiUrl}/auth/trial/Accounts?{queryParams}");
    var json = await result.Content.ReadAsStringAsync();
    var accounts = JsonConvert.DeserializeObject<AccountContractSlim[]>(json);
    // take the relevant account, here we simply take the first
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Debug.WriteLine("Uploading...");
    // get the video from URL
    var videoUrl = "VIDEO_URL"; // replace with the video URL

    // as an alternative to specifying video URL, you can upload a file.
    // remove the videoUrl parameter from the query params below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =new byte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(new ByteArrayContent(buffer));

    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"name", "video_name"},
            {"description", "video_description"},
            {"privacy", "private"},
            {"partition", "partition"},
            {"videoUrl", videoUrl},
        });
    var uploadRequestResult = await client.PostAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos?{queryParams}", content);
    var uploadResult = await uploadRequestResult.Content.ReadAsStringAsync();

    // get the video ID from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Debug.WriteLine("Uploaded");
    Debug.WriteLine("Video ID:");
    Debug.WriteLine(videoId);

    // wait for the video index to finish
    while (true)
    {
        await Task.Delay(10000);

        queryParams = CreateQueryString(
            new Dictionary<string, string>()
            {
                {"accessToken", accountInfo.AccessToken},
                {"language", "English"},
            });

        var videoGetIndexRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/Index?{queryParams}");
        var videoGetIndexResult = await videoGetIndexRequestResult.Content.ReadAsStringAsync();

        string processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

        Debug.WriteLine("");
        Debug.WriteLine("State:");
        Debug.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Debug.WriteLine("");
            Debug.WriteLine("Full JSON:");
            Debug.WriteLine(videoGetIndexResult);
            break;
        }
    }

    // search for the video
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"id", videoId},
        });

    var searchRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/Search?{queryParams}");
    var searchResult = await searchRequestResult.Content.ReadAsStringAsync();
    Debug.WriteLine("");
    Debug.WriteLine("Search:");
    Debug.WriteLine(searchResult);

    // Generate video access token (used for get widget calls)
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    var videoTokenRequestResult = await client.GetAsync($"{apiUrl}/auth/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/AccessToken?allowEdit=true");
    var videoAccessToken = (await videoTokenRequestResult.Content.ReadAsStringAsync()).Replace("\"", "");
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // get insights widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
            {"widgetType", "Keywords"},
            {"allowEdit", "true"},
        });
    var insightsWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/InsightsWidget?{queryParams}");
    var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
    Debug.WriteLine("Insights Widget url:");
    Debug.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
    Debug.WriteLine("");
    Debug.WriteLine("Player Widget url:");
    Debug.WriteLine(playerWidgetLink);
}

private string CreateQueryString(IDictionary<string, string> parameters)
{
    var queryParameters = HttpUtility.ParseQueryString(string.Empty);
    foreach (var parameter in parameters)
    {
        queryParameters[parameter.Key] = parameter.Value;
    }

    return queryParameters.ToString();
}

public class AccountContractSlim
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public string Location { get; set; }
    public string AccountType { get; set; }
    public string Url { get; set; }
    public string AccessToken { get; set; }
}
```
## <a name="common-errors"></a>Распространенные ошибки

После операции отправки могут возвращаться коды состояния, перечисленные в приведенной ниже таблице.

|Код состояния|ErrorType (в тексте ответа)|Описание|
|---|---|---|
|400|VIDEO_ALREADY_IN_PROGRESS|То же самое видео уже обрабатывается в указанной учетной записи.|
|400|VIDEO_ALREADY_FAILED|Менее 2 часов назад обработка того же самого видео завершилась сбоем. Клиентам API следует подождать не менее 2 часов, прежде чем повторно отправлять видео.|

## <a name="next-steps"></a>Дальнейшие действия

[Изучите выходные данные индексатора видео Azure, созданные API](video-indexer-output-json-v2.md)
