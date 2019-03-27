---
title: Использование API Индексатора видео Azure
titlesuffix: Azure Media Services
description: В этой статье объясняется, как приступить к работе с API Индексатора видео.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 03/25/2019
ms.author: juliako
ms.openlocfilehash: 41c665a2a1aec56cc07d5465742d01e41e6adfff
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58443584"
---
# <a name="tutorial-use-the-video-indexer-api"></a>Руководство по Использование API Индексатора видео

> [!Note]
> 1 августа 2018 г. API Индексатора видео версии 1 был объявлен нерекомендуемым. Теперь следует использовать API Индексатора видео версии 2. <br/>См. [инструкции по разработке с помощью API Индексатора видео версии 2](https://api-portal.videoindexer.ai/). 

Индексатор видео — это интегрируемая служба на основе разных технологий искусственного интеллекта (ИИ) для анализа аудио и видео, предлагаемые корпорацией Майкрософт, которые упрощает разработку. API-интерфейсы предназначены для того, чтобы разработчики могли сосредоточиться на использовании технологий ИИ для анализа мультимедиа, не беспокоясь о масштабировании, глобальном охвате, доступности и надежности облачной платформы. API можно использовать для отправки файлов, получения аналитических сведений из видео и внедряемых в приложения URL-адресов мини-приложений для анализа и воспроизведения, а также других задач.

При создании учетной записи Индексатора видео можно выбрать бесплатную пробную учетную запись (с определенным количеством бесплатных минут индексирования) или платную (без ограничивающих квот). В бесплатной пробной учетной записи Индексатора видео предоставляется до 600 минут бесплатной индексации для пользователей веб-сайта и до 2400 минут бесплатной индексации для пользователей API. С платный вариант, создать учетную запись индексатора видео, которая является [подключены к подписке Azure и учетную запись служб мультимедиа Azure](connect-to-azure.md). Вы оплачиваете проиндексированные минуты, а также несете сопутствующие расходы, связанные с учетной записью Служб мультимедиа Azure. 

В этой статье объясняется, как разработчики могут воспользоваться преимуществами [API Индексатора видео](https://api-portal.videoindexer.ai/).

## <a name="subscribe-to-the-api"></a>Оформление подписки на API

1. Войдите на [портал разработчика Индексатора видео](https://api-portal.videoindexer.ai/).
    
    ![Вход](./media/video-indexer-use-apis/video-indexer-api01.png)

   > [!Important]
   > * Укажите того же поставщика, который вы использовали при регистрации в Индексаторе видео.
   > * Личные учетные записи Google и Майкрософт (Outlook и Live) можно использовать только в качестве пробных учетных записей. Для учетных записей, подключенных к Azure, требуется Azure AD.
   > * На один адрес электронной почты может быть зарегистрирована только одна активная учетная запись. Если пользователь пытается войти с помощью учетной записи user@gmail.com для LinkedIn, а после этого с помощью учетной записи user@gmail.com для Google, отобразится страница с ошибкой о том, что такой пользователь уже существует.

2. Оформите подписку.

    Выберите вкладку [Продукты](https://api-portal.videoindexer.ai/products). Затем щелкните Authorization и подпишитесь. 
    
    ![Регистрация](./media/video-indexer-use-apis/video-indexer-api02.png)

    > [!NOTE]
    > Новые пользователи автоматически подписываются на API Authorization.
    
    После оформления подписки вы сможете просмотреть сведения о ней, а также о первичном и вторичном ключах. Ключи должны быть защищены. Их нужно использовать только в серверном коде. Они не должны быть доступны на стороне клиента (в файлах с расширением .js, .html и т. д.).

    ![Регистрация](./media/video-indexer-use-apis/video-indexer-api03.png)

> [!TIP]
> Пользователь Индексатора видео может использовать один ключ подписки для доступа к нескольким учетным записям службы. Затем эти учетные записи Индексатора видео можно связать с различными учетными записями Служб мультимедиа.

## <a name="obtain-access-token-using-the-authorization-api"></a>Получение маркера доступа с помощью API Authorization

Как только вы подпишитесь на API Authorization, вы сможете получать маркеры доступа. Эти маркеры доступа используются для аутентификации в API Operations. 

Каждый вызов к API Operations должен быть связан с маркером доступа, соответствующим области авторизации вызова.

- Уровень пользователя — маркеры доступа этого уровня позволяют выполнять операции на уровне **пользователя**. Например, получить сведения о соответствующих учетных записях.
- Уровень учетной записи — маркеры доступа этого уровня позволяют выполнять операции на уровне **учетной записи** или **видео**. Например, отправить видео, вывести список всех видео, получить аналитические сведения о видео и т. д.
- Уровень видео — маркеры доступа этого уровня позволяют выполнять операции с определенным **видео**. Например, получить аналитические сведения о видео, скачать субтитры, получить мини-приложения и т. д. 

Вы можете выбрать, предоставляют ли эти маркеры доступ только на чтение или изменение, указав для параметра **allowEdit** значение true или false.

Для большинства сценариев взаимодействия между серверами, скорее всего, потребуется один маркер **учетной записи**, так как он распространяется на операции с **учетной записью** и **видео**. Но если вы планируете выполнять клиентские вызовы к Индексатору видео (например, из JavaScript), следует использовать маркер доступа уровня **видео**, чтобы запретить клиентам доступ к учетной записи в целом. Также его следует использовать, так как при внедрении клиентского кода Индексатора видео в свое клиентское приложение (например, с помощью мини-приложения **Get Insights Widget** или **Get Player Widget**) необходимо предоставить маркер доступа уровня **видео**.

Чтобы облегчить задачу, выберите API **Authorization** > **GetAccounts**, чтобы сначала получить список учетных записей без маркера доступа уровня пользователя. Также можно отправить запрос на получение учетных записей с действующими маркерами, чтобы не выполнять дополнительный вызов на получение токена учетной записи.

Срок действия маркеров доступа истекает через 1 час. Убедитесь, что маркер доступа действителен, прежде чем использовать API Operations. Если срок действия истек, вызовите API Authorization еще раз, чтобы получить новый маркер доступа.
 
Все готово к началу интеграции с API. См. [подробное описание каждого API REST Индексатора видео](https://api-portal.videoindexer.ai/).

## <a name="account-id"></a>Идентификатор учетной записи 

Параметр идентификатора учетной записи является обязательным во всех вызовах к API для операций. Идентификатор учетной записи — это GUID, который можно получить так:

* Воспользуйтесь веб-сайтом **Индексатора видео**, чтобы получить идентификатор учетной записи:

    1. Откройте веб-сайт [Индексатора видео](https://www.videoindexer.ai/) и выполните вход.
    2. Перейдите на страницу **Параметры**.
    3. Скопируйте идентификатор учетной записи.

        ![Идентификатор учетной записи](./media/video-indexer-use-apis/account-id.png)

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

- Если вы планируете отправить видео, рекомендуется разместить файл в общедоступном расположении в сети (например, OneDrive). Получите ссылку на видео и укажите URL-адрес в качестве значения для параметра отправки файла. 

    URL-адрес, предоставляемый Индексатору видео, должен указывать на файл мультимедиа (аудио или видео). Некоторые ссылки, создаваемые OneDrive, указывают на HTML-страницу, содержащую файл. Чтобы проверить URL-адрес, просто вставьте его в адресную строку в браузере. Если начинается скачивание файла, вероятно, этот URL-адрес подходит. Если в браузере выполняется рендеринг визуализаций, скорее всего, это ссылка не на файл, а на HTML-страницу.
    
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

## <a name="see-also"></a>См. также

- [Общие сведения об Индексаторе видео](video-indexer-overview.md)
- [Регионы](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>Дальнейшие действия

[Анализ сведений о возвращаемом содержимом JSON](video-indexer-output-json-v2.md)

