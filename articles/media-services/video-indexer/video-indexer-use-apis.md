---
title: Использование API Индексатора видео
titleSuffix: Azure Media Services
description: В этой статье описывается, как начать работу с API Video Indexer Media Services Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/03/2020
ms.author: juliako
ms.openlocfilehash: 8b6d160f71bfe8b2e5c447296d511b54ce6542c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245854"
---
# <a name="tutorial-use-the-video-indexer-api"></a>Руководство. Использование API Индексатора видео

Video Indexer объединяет различные технологии искусственного интеллекта (ИИ) аудио- и видео, предлагаемые корпорацией Майкрософт, в один интегрированный сервис, что упрощает разработку. ABI предназначены для того, чтобы разработчики могли сосредоточиться на потреблении технологий Media AI, не беспокоясь о масштабе, глобальном охвате, доступности и надежности облачных платформ. Вы можете использовать API для загрузки файлов, получить подробную информацию о видео, получить URL-адреса встраиваемых понимание и виджеты игрока, и многое другое.

При создании учетной записи Video Indexer вы можете выбрать бесплатную пробную учетную запись (где вы получаете определенное количество бесплатных минут индексации) или платный вариант (где вы не ограничены квотой). С бесплатной пробной версией, Video Indexer предоставляет до 600 минут бесплатной индексации для пользователей веб-сайта и до 2400 минут бесплатной индексации для пользователей API. С помощью платной опции создается учетная запись Video Indexer, [подключенная к подписке Azure, и учетная запись Media Services Azure.](connect-to-azure.md) Вы оплачиваете проиндексированные минуты, а также несете сопутствующие расходы, связанные с учетной записью Служб мультимедиа Azure.

В этой статье объясняется, как разработчики могут воспользоваться преимуществами [API Индексатора видео](https://api-portal.videoindexer.ai/).

## <a name="subscribe-to-the-api"></a>Оформление подписки на API

1. Войдите на [портал разработчика Индексатора видео](https://api-portal.videoindexer.ai/).
    
    ![Войти на портал разработчика видеоиндекса](./media/video-indexer-use-apis/video-indexer-api01.png)

   > [!Important]
   > * Укажите того же поставщика, который вы использовали при регистрации в Индексаторе видео.
   > * Личные учетные записи Google и Microsoft (Outlook/Live) могут использоваться только для пробных учетных записей. Для учетных записей, подключенных к Azure, требуется Azure AD.
   > * На электронную почту может приходиться только одна активная учетная запись. Если пользователь пытается войти user@gmail.com в LinkedIn, user@gmail.com а затем в Google, последний будет отображать страницу ошибки, говоря, что пользователь уже существует.

2. Оформите подписку.

    Выберите вкладку [«Продукты».](https://api-portal.videoindexer.ai/products) Затем выберите Авторизацию и подпишитесь.
    
    ![Вкладка продуктов в портале разработчиков видеоиндексов](./media/video-indexer-use-apis/video-indexer-api02.png)

    > [!NOTE]
    > Новые пользователи автоматически подписываются на API Authorization.
    
    После подписки вы можете увидеть подписку и ваши основные и вторичные ключи. Ключи должны быть защищены. Их нужно использовать только в серверном коде. Они не должны быть доступны на стороне клиента (.js, .html, и так далее).

    ![Подписка и ключи в портале разработчика видеоиндекса](./media/video-indexer-use-apis/video-indexer-api03.png)

> [!TIP]
> Пользователь Индексатора видео может использовать один ключ подписки для доступа к нескольким учетным записям службы. Затем эти учетные записи Индексатора видео можно связать с различными учетными записями Служб мультимедиа.

## <a name="obtain-access-token-using-the-authorization-api"></a>Получение маркера доступа с помощью API Authorization

После того, как вы подпишитесь на API авторизации, вы можете получить токены доступа. Эти маркеры доступа используются для аутентификации в API Operations.

Каждый вызов к API Operations должен быть связан с маркером доступа, соответствующим области авторизации вызова.

- Уровень пользователя: токены доступа уровня пользователя позволяют выполнять операции на уровне **пользователя.** Например, получить сведения о соответствующих учетных записях.
- Уровень учетной записи: токены уровня учетной записи позволяют выполнять операции на уровне **учетной записи** или на уровне **видео.** Например, загрузить видео, перечислить все видео, получить видео идеи, и так далее.
- Уровень видео: токены доступа уровня видео позволяют выполнять операции на определенном **видео.** Например, получить видео идеи, скачать подписи, получить виджеты, и так далее.

Вы можете контролировать, являются ли эти токены только для чтения или позволяют редактировать, указывая **allowEdit'true/false**.

Для большинства сценариев от сервера к серверу вы, вероятно, будете использовать тот же маркер **учетной записи,** поскольку он охватывает как операции **учетной записи,** так и **видеооперации.** Однако, если вы планируете совершать звонки на Video Indexer (например, с JavaScript), необходимо использовать токен доступа **к видео,** чтобы предотвратить доступ клиентов ко всему счету. Это также причина того, что при встраивании кода клиента Video Indexer в клиента (например, с помощью **Get Insights Widget** или **Get Player Widget),** вы должны предоставить токен доступа к **видео.**

Чтобы облегчить задачу, выберите API **Authorization** > **GetAccounts**, чтобы сначала получить список учетных записей без маркера доступа уровня пользователя. Также можно отправить запрос на получение учетных записей с действующими маркерами, чтобы не выполнять дополнительный вызов на получение токена учетной записи.

Срок действия маркеров доступа истекает через 1 час. Убедитесь, что маркер доступа действителен, прежде чем использовать API Operations. Если срок действия истекает, позвоните в API авторизации, чтобы получить новый токен доступа.

Вы готовы начать интеграцию с API. См. [подробное описание каждого API REST Индексатора видео](https://api-portal.videoindexer.ai/).

## <a name="account-id"></a>Идентификатор учетной записи

Параметр идентификатора учетной записи является обязательным во всех вызовах к API для операций. Идентификатор учетной записи — это GUID, который можно получить так:

* Воспользуйтесь веб-сайтом **Индексатора видео**, чтобы получить идентификатор учетной записи:

    1. Откройте веб-сайт [Индексатора видео](https://www.videoindexer.ai/) и выполните вход.
    2. Перейдите на страницу **Параметры**.
    3. Скопируйте идентификатор учетной записи.

        ![Настройки индекса видео и идентификатор учетной записи](./media/video-indexer-use-apis/account-id.png)

* Получите идентификатор учетной записи с помощью программных средств на **портале разработка Индексатора видео**.

    Используйте API [учетной записи Get.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Account?)

    > [!TIP]
    > Вы можете создать маркеры доступа для учетных записей, определив `generateAccessTokens=true`.

* Получите идентификатор учетной записи из URL-адреса страницы проигрывателя в своей учетной записи.

    При просмотре видео идентификатор отображается после раздела `accounts` и перед разделом `videos`.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Рекомендации

В этом разделе приводятся некоторые рекомендации по использованию API Индексатора видео.

- Если вы планируете загрузить видео, рекомендуется разместить файл в общественном месте расположение сети (например, OneDrive). Получите ссылку на видео и укажите URL-адрес в качестве значения для параметра отправки файла.

    URL-адрес, предоставляемый Индексатору видео, должен указывать на файл мультимедиа (аудио или видео). Некоторые ссылки, создаваемые OneDrive, указывают на HTML-страницу, содержащую файл. Простая проверка для URL-адреса заключается в том, чтобы вставить его в браузер, если файл начинает загрузку, это, вероятно, хороший URL. Если браузер визуализировать некоторую визуализацию, это, скорее всего, не ссылка на файл, а страница HTML.

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
- [Регионах](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>Дальнейшие действия

- [Изучите детали вывода JSON](video-indexer-output-json-v2.md)
- Ознакомьтесь с [примером кода,](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/API) демонстрирующего важный аспект загрузки и индексации видео. Следуя коду Wil дать вам хорошее представление о том, как использовать наш API для основных функций. Убедитесь в том, чтобы прочитать внеочередные комментарии и заметить наши рекомендации передовой практики.

