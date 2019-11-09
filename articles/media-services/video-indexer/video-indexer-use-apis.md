---
title: Использование API Индексатора видео
titleSuffix: Azure Media Services
description: Руководство по началу работы с API индексатора видео служб мультимедиа Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: e1fcfcd9e6bcb6108573f032d81f10ccc2be1e57
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839619"
---
# <a name="tutorial-use-the-video-indexer-api"></a>Руководство. Использование API Индексатора видео

Индексатор видео объединяет различные технологии искусственного интеллекта и видео, предлагаемые корпорацией Майкрософт, в одну интегрированную службу, упрощая разработку. Эти API-интерфейсы позволяют разработчикам сосредоточиться на использовании технологий мультимедиа AI, не беспокоясь о масштабировании, глобальном доступе, доступности и надежности облачных платформ. Вы можете использовать API для передачи файлов, получения подробных сведений о видео, получения URL-адресов для встраиваемых элементов и других возможностей проигрывателя.

При создании учетной записи индексатора видео можно выбрать бесплатную пробную учетную запись (где вы получаете определенное количество бесплатных минут индексирования) или платный вариант (если квота не ограничивается). С помощью бесплатной пробной версии индексатор видео предоставляет до 600 минут с бесплатной индексацией для пользователей веб-сайтов и до 2400 минут с бесплатной индексацией для пользователей API. С помощью платного варианта вы создадите учетную запись индексатора видео, [подключенную к вашей подписке Azure, и учетную запись служб мультимедиа Azure](connect-to-azure.md). Вы оплачиваете проиндексированные минуты, а также несете сопутствующие расходы, связанные с учетной записью Служб мультимедиа Azure.

В этой статье объясняется, как разработчики могут воспользоваться преимуществами [API Индексатора видео](https://api-portal.videoindexer.ai/).

## <a name="subscribe-to-the-api"></a>Оформление подписки на API

1. Войдите на [портал разработчика Индексатора видео](https://api-portal.videoindexer.ai/).
    
    ![Вход на портал разработчика индексатора видео](./media/video-indexer-use-apis/video-indexer-api01.png)

   > [!Important]
   > * Укажите того же поставщика, который вы использовали при регистрации в Индексаторе видео.
   > * Личные учетные записи Google и Microsoft (Outlook/Live) можно использовать только для пробных учетных записей. Для учетных записей, подключенных к Azure, требуется Azure AD.
   > * На одну электронную почту может быть только одна активная учетная запись. Если пользователь пытается войти с помощью user@gmail.com для LinkedIn и более поздних версий с user@gmail.com для Google, в последнем случае будет отображаться страница ошибки, говорящая, что пользователь уже существует.

2. Оформите подписку.

    Перейдите на вкладку [продукты](https://api-portal.videoindexer.ai/products) . Затем выберите авторизация и подписка.
    
    ![Вкладка "продукты" на портале разработчика индексатора видео](./media/video-indexer-use-apis/video-indexer-api02.png)

    > [!NOTE]
    > Новые пользователи автоматически подписываются на API Authorization.
    
    После подписки вы увидите подписку, а также первичные и вторичные ключи. Ключи должны быть защищены. Их нужно использовать только в серверном коде. Они не должны быть доступны на стороне клиента (. js,. HTML и т. д.).

    ![Подписка и ключи в портале разработчика индексатора видео](./media/video-indexer-use-apis/video-indexer-api03.png)

> [!TIP]
> Пользователь Индексатора видео может использовать один ключ подписки для доступа к нескольким учетным записям службы. Затем эти учетные записи Индексатора видео можно связать с различными учетными записями Служб мультимедиа.

## <a name="obtain-access-token-using-the-authorization-api"></a>Получение маркера доступа с помощью API Authorization

После подписки на API авторизации можно получить маркеры доступа. Эти маркеры доступа используются для аутентификации в API Operations.

Каждый вызов к API Operations должен быть связан с маркером доступа, соответствующим области авторизации вызова.

- Уровень пользователя: маркеры доступа уровня пользователя позволяют выполнять операции на уровне **пользователя** . Например, получить сведения о соответствующих учетных записях.
- Уровень учетной записи: маркеры доступа на уровне учетной записи позволяют выполнять операции на уровне **учетной записи** или на уровне **видео** . Например, отправьте видео, перечислите все видео, получите информацию о видео и т. д.
- Уровень видео. маркеры доступа на уровне видео позволяют выполнять операции с конкретным **видео**. Например, получите сведения о видео, скачайте заголовки, получите мини-приложения и т. д.

Вы можете управлять тем, доступны ли эти маркеры только для чтения, или же разрешить редактирование, указав **allowEdit = true/false**.

Для большинства сценариев "сервер-сервер" вы, вероятно, используете тот же маркер **учетной записи** , так как он охватывает как операции с **учетными записями** , так и операции с **видео** . Однако если вы планируете выполнять вызовы индексатора видео на стороне клиента (например, из JavaScript), вам нужно будет использовать маркер доступа к **видео** , чтобы запретить клиентам доступ ко всей учетной записи. Это также объясняется тем, что при внедрении клиентского кода индексатора видео в клиенте (например, с помощью мини-приложения **Get Insights** или при **получении графического элемента Player**) необходимо предоставить маркер доступа к **видео** .

Чтобы облегчить задачу, выберите API **Authorization** > **GetAccounts**, чтобы сначала получить список учетных записей без маркера доступа уровня пользователя. Также можно отправить запрос на получение учетных записей с действующими маркерами, чтобы не выполнять дополнительный вызов на получение токена учетной записи.

Срок действия маркеров доступа истекает через 1 час. Убедитесь, что маркер доступа действителен, прежде чем использовать API Operations. Если срок действия истекает, снова вызовите API авторизации, чтобы получить новый маркер доступа.

Вы готовы приступить к интеграции с API. См. [подробное описание каждого API REST Индексатора видео](https://api-portal.videoindexer.ai/).

## <a name="account-id"></a>Идентификатор учетной записи

Параметр идентификатора учетной записи является обязательным во всех вызовах к API для операций. Идентификатор учетной записи — это GUID, который можно получить так:

* Воспользуйтесь веб-сайтом **Индексатора видео**, чтобы получить идентификатор учетной записи:

    1. Откройте веб-сайт [Индексатора видео](https://www.videoindexer.ai/) и выполните вход.
    2. Перейдите на страницу **Параметры**.
    3. Скопируйте идентификатор учетной записи.

        ![Параметры индексатора видео и идентификатор учетной записи](./media/video-indexer-use-apis/account-id.png)

* Получите идентификатор учетной записи с помощью программных средств на **портале разработка Индексатора видео**.

    Используйте API [Get accounts](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Accounts?).

    > [!TIP]
    > Вы можете создать маркеры доступа для учетных записей, определив `generateAccessTokens=true`.

* Получите идентификатор учетной записи из URL-адреса страницы проигрывателя в своей учетной записи.

    При просмотре видео идентификатор отображается после раздела `accounts` и перед разделом `videos`.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Рекомендации

В этом разделе приводятся некоторые рекомендации по использованию API Индексатора видео.

- Если вы планируете отправить видео, рекомендуется поместить файл в какое-либо общедоступное сетевое расположение (например, OneDrive). Получите ссылку на видео и укажите URL-адрес в качестве значения для параметра отправки файла.

    URL-адрес, предоставляемый Индексатору видео, должен указывать на файл мультимедиа (аудио или видео). Некоторые ссылки, создаваемые OneDrive, указывают на HTML-страницу, содержащую файл. Простая проверка URL-адреса заключается в вставке его в браузер — если файл начнет загрузку, скорее всего, это хороший URL-адрес. Если браузер выдает некоторую визуализацию, скорее всего, это не ссылка на файл, а на HTML-страницу.

- Если вы вызываете API, чтобы получить аналитические сведения для выбранного видео, вы получите подробные выходные данные в формате JSON в виде содержимого ответа. См. дополнительные сведения о [возвращаемом содержимом JSON](video-indexer-output-json-v2.md).

## <a name="code-sample"></a>Пример кода

В следующем фрагменте кода C# показано, как использовать все API Индексатора видео.

```csharp
var apiUrl = "https://api.videoindexer.ai";
var accountId = "..."; 
var location = "westus2";
var apiKey = "..."; 

System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

// create the http client
var handler = new HttpClientHandler(); 
handler.AllowAutoRedirect = false; 
var client = new HttpClient(handler);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey); 

// obtain account access token
var accountAccessTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/AccessToken?allowEdit=true").Result;
var accountAccessToken = accountAccessTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// upload a video
var content = new MultipartFormDataContent();
Debug.WriteLine("Uploading...");
// get the video from URL
var videoUrl = "VIDEO_URL"; // replace with the video URL

// as an alternative to specifying video URL, you can upload a file.
// remove the videoUrl parameter from the query string below and add the following lines:
  //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
  //byte[] buffer =newbyte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(newByteArrayContent(buffer));

var uploadRequestResult = client.PostAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos?accessToken={accountAccessToken}&name=some_name&description=some_description&privacy=private&partition=some_partition&videoUrl={videoUrl}", content).Result;
var uploadResult = uploadRequestResult.Content.ReadAsStringAsync().Result;

// get the video id from the upload result
var videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
Debug.WriteLine("Uploaded");
Debug.WriteLine("Video ID: " + videoId);           

// obtain video access token
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
var videoTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/Videos/{videoId}/AccessToken?allowEdit=true").Result;
var videoAccessToken = videoTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// wait for the video index to finish
while (true)
{
  Thread.Sleep(10000);

  var videoGetIndexRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/Index?accessToken={videoAccessToken}&language=English").Result;
  var videoGetIndexResult = videoGetIndexRequestResult.Content.ReadAsStringAsync().Result;

  var processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

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
var searchRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/Search?accessToken={accountAccessToken}&id={videoId}").Result;
var searchResult = searchRequestResult.Content.ReadAsStringAsync().Result;
Debug.WriteLine("");
Debug.WriteLine("Search:");
Debug.WriteLine(searchResult);

// get insights widget url
var insightsWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?accessToken={videoAccessToken}&widgetType=Keywords&allowEdit=true").Result;
var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
Debug.WriteLine("Insights Widget url:");
Debug.WriteLine(insightsWidgetLink);

// get player widget url
var playerWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?accessToken={videoAccessToken}").Result;
var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
Debug.WriteLine("");
Debug.WriteLine("Player Widget url:");
Debug.WriteLine(playerWidgetLink);

```

## <a name="see-also"></a>Дополнительные материалы

- [Общие сведения об Индексаторе видео](video-indexer-overview.md)
- [Регионы](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>Дальнейшие действия

[Анализ сведений о возвращаемом содержимом JSON](video-indexer-output-json-v2.md)
