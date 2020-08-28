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
ms.date: 02/18/2020
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 153540ce0bf49740d0b3387715d83c8efd7af2cf
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2020
ms.locfileid: "89011877"
---
# <a name="upload-and-index-your-videos"></a>Отправка и индексирование видео  

API Индексатора видео позволяет загружать видео двумя способами: 

* загрузка видео из URL-адреса (предпочтительно);
* отправка видеофайла в виде массива байтов в тексте запроса;
* использование существующего ресурса Служб мультимедиа Azure с указанием [идентификатора ресурса](../latest/assets-concept.md) (поддерживается только в платных учетных записях).

После отправки видео индексатор видео (необязательно) кодирует видео (см. статью). При создании учетной записи Индексатора видео можно выбрать бесплатную пробную учетную запись (с определенным количеством бесплатных минут индексирования) или платную (без ограничивающих квот). В бесплатной пробной учетной записи Индексатора видео предоставляется до 600 минут бесплатной индексации для пользователей веб-сайта и до 2400 минут бесплатной индексации для пользователей API. Платный вариант подразумевает создание учетной записи Индексатора видео, [подключенной к подписке Azure и учетной записи Служб мультимедиа Azure](connect-to-azure.md). Вы платите за минуты индексирования, а также за использование учетной записи Служб мультимедиа. 

В этой статье показано, как отправлять и индексировать видео с помощью следующих параметров:

* [веб-сайт Индексатора видео](#website); 
* [интерфейсы API Индексатора видео](#apis).

## <a name="uploading-considerations-and-limitations"></a>Рекомендации по передаче и связанные ограничения
 
- Имя видео должно содержать не более 80 символов.
- При передаче видео на основе URL-адреса (предпочтительно) конечная точка должна быть защищена с использованием протокола TLS версии 1.2 (или выше).
- Размер видео, передаваемого с использованием его URL-адреса, ограничен 30 ГБ.
- Длина URL-адреса запроса ограничена 6144 символами, при этом длина URL-адреса строки запроса ограничена 4096 символами.
- Размер видео, передаваемого с использованием массива байтов, ограничено 2 ГБ.
- При использовании такого варианта время ожидания передачи истекает через 30 мин.
- URL-адрес, указанный в `videoURL` параметре, должен быть закодирован.
- К индексированию ресурсов Служб мультимедиа применяется то же ограничение, что и при индексировании на основе URL-адреса.
- Максимальное время работы Индексатора видео с одним файлом составляет 4 часа.
- URL-адрес должен быть доступным (например, являться общедоступным URL-адресом). 

    Если используется частный URL-адрес, в запросе необходимо указать маркер доступа.
- URL-адрес должен указывать на допустимый файл мультимедиа, а не на веб-страницу, например ссылку на `www.youtube.com` страницу.
- С платной учетной записью вы можете отправлять до 50 видео в минуту, а с пробной — до 5 видео в минуту.

> [!Tip]
> Рекомендуем использовать .NET Framework 4.6.2 или более поздней версии, так как для более ранних версий .NET Framework протокол TLS 1.2 не используется по умолчанию.
>
> Если нужно использовать более ранние версии .NET Framework, прежде чем вызывать REST API, добавьте в код следующую строку:  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="supported-file-formats-for-video-indexer"></a>Поддерживаемые форматы файлов для Индексатора видео

Список форматов файлов, которые можно использовать с Индексатором видео, см. в подразделе [Контейнер ввода и форматы файлов](../latest/media-encoder-standard-formats.md#input-containerfile-formats).

## <a name="video-files-storage"></a>Хранилище видеофайлов

- Используя платную учетную запись индексатора видео, вы создадите учетную запись индексатора видео, которая подключена к вашей подписке Azure и учетной записи служб мультимедиа Azure. Дополнительные сведения см. в статье [Создание учетной записи индексатора видео, подключенной к Azure](connect-to-azure.md).
- Видеофайлы хранятся в службе хранилища Azure службой мультимедиа Azure. Ограничений по времени нет.
- Вы всегда можете удалить видео и звуковые файлы, а также любые метаданные и аналитические данные, извлеченные из них с помощью индексатора видео. При удалении файла из Индексатора видео сам этот файл, все его метаданных и извлеченные аналитические сведения удаляются из Индексатора видео без возможности восстановления. Но если вы настроили собственное решение резервного копирования в службе хранилища Azure, файл сохраняется в службе хранилища Azure.
- Сохраняемость видео идентична, независимо от того, выполняется ли отправка на веб-сайт индексатора видео или через API передачи.
   
## <a name="upload-and-index-a-video-using-the-video-indexer-website"></a><a name="website"></a>Отправка и индексирование видео с помощью веб-сайта индексатора видео

> [!NOTE]
> Имя видео должно содержать не более 80 символов.

1. Войдите на веб-сайт [Индексатора видео](https://www.videoindexer.ai/).
2. Чтобы отправить видео, нажмите кнопку **Отправить** или щелкните ссылку.

    ![Передать](./media/video-indexer-get-started/video-indexer-upload.png)

    После отправки видео Индексатор видео запускает индексирование и анализ видео.

    ![Отправка](./media/video-indexer-get-started/video-indexer-uploaded.png) 

    Когда Индексатор видео завершит анализ, вы получите уведомление, содержащее ссылку на ваше видео и краткое описание того, что в нем обнаружено. Например, люди, разделы, распознавание текста.

## <a name="upload-and-index-with-api"></a><a name="apis"></a>Передача и индексирование с помощью API

Используйте API [передачи видео](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) для отправки и индексирования видео на основе URL-адреса. Следующий пример кода включает в себя код с комментарием, который показывает, как передать массив байтов. 

### <a name="configurations-and-params"></a>Конфигурации и параметры

В этом разделе описываются некоторые необязательные параметры и случаи, когда может потребоваться их настройка.

#### <a name="externalid"></a>externalID 

С помощью этого параметра можно указать идентификатор, который будет связан с видео. Идентификатор может применяться к интеграции с внешней системой управления содержимым видео. Видео на портале Индексатора видео можно найти с помощью указанного внешнего идентификатора.

#### <a name="callbackurl"></a>callbackUrl

URL-адрес, используемый для уведомления клиента (с помощью запроса POST) о текущих событиях:

- Изменение состояния индексирования: 
    - Свойства    
    
        |Имя|Описание|
        |---|---|
        |идентификатор|Идентификатор видео|
        |Состояние|Состояние видео|  
    - Пример: https: \/ /test.com/notifyme?projectName=MyProject&ID = 1234abcd&State = обработано
- Человек, идентифицированный на видео:
  - Свойства
    
      |Имя|Описание|
      |---|---|
      |идентификатор| Идентификатор видео|
      |faceId|Идентификатор лица, которое отображается в индексе видео|
      |knownPersonId|Идентификатор человека, который имеет уникальную модель лица|
      |personName|Имя человека|
        
    - Пример: https: \/ /test.com/notifyme?projectName=MyProject&ID = 1234abcd&FaceId = 12&кновнперсонид = CCA84350-89B7-4262-861C-3CAC796542A5&персоннаме = Inigo_Montoya 

##### <a name="notes"></a>Примечания

- Индексатор видео возвращает любые существующие параметры, указанные в исходном URL-адресе.
- Указанный URL-адрес должен быть закодирован.

#### <a name="indexingpreset"></a>indexingPreset

Используйте этот параметр, если необработанные или внешние записи содержат фоновый шум. Этот параметр используется для настройки процесса индексирования. Можно указать следующие значения:

- `AudioOnly` — индексирование и извлечение аналитических сведений с помощью только аудио (без видео).
- `VideoOnly` — Индексирование и извлечение аналитических данных с использованием только видео (без звука)
- `Default` — индексирование и извлечение аналитических сведений с помощью аудио и видео.
- `DefaultWithNoiseReduction` — индексирование и извлечение аналитических сведений из аудио и видео с применением алгоритмов снижения шума в аудиопотоке.

> [!NOTE]
> Индексатор видео охватывает до двух дорожек звука. Если в файле больше звуковых дорожек, они будут рассматриваться как одна дорожка.<br/>
Если вы хотите индексировать записи отдельно, необходимо извлечь соответствующий звуковой файл и индексировать его как `AudioOnly` .

Цена зависит от выбранного параметра индексирования.  

#### <a name="priority"></a>priority

Видео индексируются службой "Индексатор видео" согласно их приоритету. Используйте параметр **priority**, чтобы указать приоритет индексирования. Допустимы следующие значения: **Low** (Низкий), **Normal** (Нормальный, по умолчанию) и **High** (Высокий).

Параметр **Priority** (Приоритет) поддерживается только для платных учетных записей.

#### <a name="streamingpreset"></a>streamingPreset

После отправки видео при необходимости кодируется Индексатором видео. Затем выполняется индексирование и анализ видео. Когда Индексатор видео завершит анализ, вы получите уведомление с идентификатором видео.  

При использовании API [отправки видео](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) или [повторной индексации видео](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) одним из необязательных параметров является `streamingPreset`. Если для параметра `streamingPreset` задать значение `Default`, `SingleBitrate` или `AdaptiveBitrate`, запускается процесс кодирования. После выполнения заданий индексирования и кодирования видео будет опубликовано, и вы также сможете осуществлять его потоковую передачу. Конечная точка потоковой передачи, из которой нужно передавать видео потоком, должна находиться в состоянии **Выполняется**.

Для Синглебитрате на выходе будет применяться Стандартная стоимость кодировщика. Если высота видео больше или равна 720, индексатор видео кодирует его как 1280 x 720. В противном случае — как 640x468.
Значение по умолчанию — [кодировка с учетом содержимого](../latest/content-aware-encoding.md).

Чтобы выполнить задания индексирования и кодирования, [учетной записи служб мультимедиа Azure, подключенной к вашей учетной записи Индексатора видео](connect-to-azure.md), требуются зарезервированные единицы. Дополнительные сведения см. в статье [Обзор масштабирования обработки мультимедиа](../previous/media-services-scale-media-processing-overview.md). Так как эти задания предусматривают ресурсоемкие вычисления, настоятельно рекомендуется использовать единицу типа S3. Количество зарезервированных единиц определяет максимальное количество заданий, которые могут выполняться параллельно. Мы рекомендуем использовать не менее 10 зарезервированных единиц типа S3. 

Если вы собираетесь только проиндексировать свое видео без кодирования, задайте для параметра `streamingPreset` значение `NoStreaming`.

#### <a name="videourl"></a>videoUrl

URL-адрес индексируемого видео- или аудиофайла. URL-адрес должен указывать на файл мультимедиа (HTML-страницы не поддерживаются). Файл может быть защищен маркером доступа, предоставленным в составе универсального кода ресурса (URI), а конечная точка, обрабатывающая файл, должна быть защищена с использованием протокола TLS версии 1.2 или выше. URL-адрес нужно закодировать. 

Если параметр `videoUrl` не указан, Индексатор видео ожидает, что вы передадите этот файл в виде содержимого текста формы или составного содержимого.

### <a name="code-sample"></a>Пример кода

В следующем фрагменте кода C# показано, как использовать все API Индексатора видео.

#### <a name="instructions-for-running-this-code-sample"></a>Инструкции по запуску этого примера кода

После копирования этого кода в платформу разработки необходимо указать два параметра: ключ проверки подлинности управления API и URL-адрес видео.

* Ключ API — ключ API — это ваш личный ключ подписки управления API, который позволит получить маркер доступа для выполнения операций с учетной записью индексатора видео. 

    Чтобы получить ключ API, пройдите по этой последовательности:

    * Перейдите на страницу https://api-portal.videoindexer.ai/.
    * Вход
    * Последовательно выберите **продукты**  ->  **Authorization**  ->  **Подписка авторизация** авторизации.
    * Копирование **первичного ключа**
* URL-адрес видео — URL-адрес видео-или аудиофайла, который необходимо проиндексировать. URL-адрес должен указывать на файл мультимедиа (HTML-страницы не поддерживаются). Файл может быть защищен маркером доступа, предоставленным в составе универсального кода ресурса (URI), а конечная точка, обрабатывающая файл, должна быть защищена с использованием протокола TLS версии 1.2 или выше. URL-адрес нужно закодировать.

В результате успешного выполнения примера кода будут содержаться URL-адрес элемента "аналитика" и URL мини-приложения проигрывателя, которые позволят вам исследовать ценные сведения и видео, отправленные соответственно. 


```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var apiKey = "..."; // replace with API key taken from https://aka.ms/viapi

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
    
    // take the relevant account, here we simply take the first, 
    // you can also get the account via accounts.First(account => account.Id == <GUID>);
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Console.WriteLine("Uploading...");
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
    Console.WriteLine("Uploaded");
    Console.WriteLine("Video ID:");
    Console.WriteLine(videoId);

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

        Console.WriteLine("");
        Console.WriteLine("State:");
        Console.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Console.WriteLine("");
            Console.WriteLine("Full JSON:");
            Console.WriteLine(videoGetIndexResult);
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
    Console.WriteLine("");
    Console.WriteLine("Search:");
    Console.WriteLine(searchResult);

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
    Console.WriteLine("Insights Widget url:");
    Console.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
     Console.WriteLine("");
     Console.WriteLine("Player Widget url:");
     Console.WriteLine(playerWidgetLink);
     Console.WriteLine("\nPress Enter to exit...");
     String line = Console.ReadLine();
     if (line == "enter")
     {
         System.Environment.Exit(0);
     }

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

### <a name="common-errors"></a>Распространенные ошибки

После операции отправки могут возвращаться коды состояния, перечисленные в приведенной ниже таблице.

|Код состояния|ErrorType (в тексте ответа)|Описание|
|---|---|---|
|409|VIDEO_INDEXING_IN_PROGRESS|То же самое видео уже обрабатывается в указанной учетной записи.|
|400|VIDEO_ALREADY_FAILED|Менее 2 часов назад обработка того же самого видео завершилась сбоем. Клиентам API следует подождать не менее 2 часов, прежде чем повторно отправлять видео.|
|429||Пробные учетные записи разрешены 5 отправок в минуту. Платные учетные записи разрешены 50 отправок в минуту.|

## <a name="next-steps"></a>Дальнейшие действия

[Изучите выходные данные индексатора видео Azure, созданные API](video-indexer-output-json-v2.md)
