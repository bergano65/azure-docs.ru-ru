---
title: Создание проверок с помощью .NET — Content Moderator
titlesuffix: Azure Cognitive Services
description: Как создавать проверки с помощью пакета SDK Azure Content Moderator для .NET.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/19/2019
ms.author: sajagtap
ms.openlocfilehash: 153d42bf4ce4322536d6837be3058d1f9bfb49a2
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758646"
---
# <a name="create-human-reviews-net"></a>Создание пользовательской проверки (.NET)

Обзоры, хранения и отображения содержимого для человека модераторов для оценки. Когда пользователь завершает проверку, результаты отправляются в конечную точку заданный обратный вызов. Это руководство содержит сведения и примеры кода, которые помогут вам приступить к использованию [содержимого модератора пакет SDK для .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) для:

- создание набора проверок для модераторов-пользователей;
- получение состояния существующих проверок для модераторов-пользователей.

## <a name="prerequisites"></a>Технические условия

- Войдите или создайте учетную запись на Content Moderator [средство проверки](https://contentmoderator.cognitive.microsoft.com/) сайта.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Проверка, может ли ключ API вызвать API проверки для создания соответствующих заданий

Если вы начали с портала Azure, после выполнения предыдущих шагов у вас может получиться два ключа Content Moderator.

Если в своем примере пакета SDK вы планируете использовать ключ API, предоставленный платформой Azure, выполните [эти действия](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis), чтобы разрешить приложению вызывать API проверки и создавать соответствующие задания.

Если вы используете бесплатный пробный ключ, сгенерированный средством проверки, ваша учетная запись средства проверки уже знает об этом ключе, поэтому никакие дополнительные действия не требуются.

## <a name="create-your-visual-studio-project"></a>Создание проекта Visual Studio

1. Добавьте новый проект **Консольное приложение (.NET Framework)** в свое решение.

   В примере кода назовите проект **CreateReviews**.

1. Выберите этот проект в качестве единственного запускаемого проекта для решения.

### <a name="install-required-packages"></a>Установка необходимых пакетов

Установите следующие пакеты NuGet:

- Microsoft.Azure.CognitiveServices.ContentModerator;
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json.

### <a name="update-the-programs-using-statements"></a>Обновление инструкций using программы

Измените инструкции using в вашей программе.


```csharp
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
```

### <a name="create-the-content-moderator-client"></a>Создание клиента Content Moderator

Добавьте следующий фрагмент кода, чтобы создать клиент Content Moderator для своей подписки.

> [!IMPORTANT]
> Укажите в полях **AzureRegion** и **CMSubscriptionKey** значения идентификатора региона и ключа подписки соответственно.

```csharp
/// <summary>
/// Wraps the creation and configuration of a Content Moderator client.
/// </summary>
/// <remarks>This class library contains insecure code. If you adapt this
/// code for use in production, use a secure method of storing and using
/// your Content Moderator subscription key.</remarks>
public static class Clients
{
    /// <summary>
    /// The region/location for your Content Moderator account,
    /// for example, westus.
    /// </summary>
    private static readonly string AzureRegion = "YOUR API REGION";

    /// <summary>
    /// The base URL fragment for Content Moderator calls.
    /// </summary>
    private static readonly string AzureBaseURL =
        $"https://{AzureRegion}.api.cognitive.microsoft.com";

    /// <summary>
    /// Your Content Moderator subscription key.
    /// </summary>
    private static readonly string CMSubscriptionKey = "YOUR API KEY";

    /// <summary>
    /// Returns a new Content Moderator client for your subscription.
    /// </summary>
    /// <returns>The new client.</returns>
    /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
    /// When you have finished using the client,
    /// you should dispose of it either directly or indirectly. </remarks>
    public static ContentModeratorClient NewClient()
    {
        // Create and initialize an instance of the Content Moderator API wrapper.
        ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

        client.Endpoint = AzureBaseURL;
        return client;
    }
}
```

## <a name="create-a-class-to-associate-internal-content-information-with-a-review-id"></a>Создание класса для связывания внутренних сведений о содержимом с идентификатором проверки

Добавьте следующий класс в класс **Program**. Этот класс используется, чтобы связать идентификатор проверки с внутренним идентификатором содержимого для элемента.

```csharp
/// <summary>
    /// Associates the review ID (assigned by the service) to the internal
    /// content ID of the item.
    /// </summary>
    public class ReviewItem
    {
        /// <summary>
        /// The media type for the item to review.
        /// </summary>
        public string Type;

        /// <summary>
        /// The URL of the item to review.
        /// </summary>
        public string Url;

        /// <summary>
        /// The internal content ID for the item to review.
        /// </summary>
        public string ContentId;

        /// <summary>
        /// The ID that the service assigned to the review.
        /// </summary>
        public string ReviewId;
    }
```

### <a name="initialize-application-specific-settings"></a>Инициализация параметров приложения

> [!NOTE]
> Для ключа службы Content Moderator установлен лимит числа запросов в секунду (RPS). Если его превысить, пакет SDK породит исключение с кодом ошибки 429.
>
> Для ключа бесплатного уровня предусмотрен лимит в один запрос в секунду.

#### <a name="add-the-following-constants-to-the-program-class-in-programcs"></a>Добавьте следующие константы для **программы** класс в файле Program.cs

```csharp
/// <summary>
    /// The minimum amount of time, in milliseconds, to wait between calls
    /// to the Image List API.
    /// </summary>
    private const int throttleRate = 3000;

    /// <summary>
    /// The number of seconds to delay after a review has finished before
    /// getting the review results from the server.
    /// </summary>
    private const double latencyDelay = 45;

    /// <summary>
    /// The name of the log file to create.
    /// </summary>
    /// <remarks>Relative paths are relative to the execution directory.</remarks>
    private const string OutputFile = "OutputLog.txt";
```

#### <a name="add-the-following-constants-and-static-fields-to-the-program-class-in-programcs"></a>Добавьте следующие константы и статические поля для **программы** класс в файле Program.cs

Измените эти значения с учетом данных своей подписки и команды.

> [!NOTE]
> Константе TeamName присваивается имя, указанное вами при создании подписки [инструмента проверки Content Moderator](https://contentmoderator.cognitive.microsoft.com/). Узнать значение TeamName можно в разделе **Credentials** (Учетные данные), щелкнув меню **Settings** (Параметры) (значок шестеренки).
>
> Имя группы указано в поле **Id** (Идентификатор) в разделе **API**.

```csharp
/// <summary>
/// The name of the team to assign the review to.
/// </summary>
/// <remarks>This must be the team name you used to create your
/// Content Moderator account. You can retrieve your team name from
/// the Content Moderator web site. Your team name is the Id associated
/// with your subscription.</remarks>
private const string TeamName = "YOUR REVIEW TEAM ID";

/// <summary>
/// The optional name of the subteam to assign the review to.
/// </summary>
private const string Subteam = null;

/// <summary>
/// The callback endpoint for completed reviews.
/// </summary>
/// <remarks>Reviews show up for reviewers on your team. 
/// As reviewers complete reviews, results are sent to the
/// callback endpoint using an HTTP POST request.</remarks>
private const string CallbackEndpoint = "YOUR API ENDPOINT";

/// <summary>
/// The media type for the item to review.
/// </summary>
/// <remarks>Valid values are "image", "text", and "video".</remarks>
private const string MediaType = "image";

/// <summary>
/// The URLs of the images to create review jobs for.
/// </summary>
private static readonly string[] ImageUrls = new string[] {
    "https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg"
    // add more if you want
};

/// <summary>
/// The metadata key to initially add to each review item.
/// </summary>
private const string MetadataKey = "sc";

/// <summary>
/// The metadata value to initially add to each review item.
/// </summary>
private const string MetadataValue = "true";
```

#### <a name="add-the-following-static-fields-to-the-program-class-in-programcs"></a>Добавьте следующие статические поля для **программы** класс в файле Program.cs

Эти поля используются для отслеживания состояния приложения.

```csharp
/// <summary>
/// A static reference to the text writer to use for logging.
/// </summary>
private static TextWriter writer;

/// <summary>
/// The cached review information, associating a local content ID
/// to the created review ID for each item.
/// </summary>
private static List<ReviewItem> reviewItems =
    new List<ReviewItem>();
```

## <a name="create-a-method-to-write-messages-to-the-log-file"></a>Создание метода для записи сообщений в файл журнала

Добавьте следующий метод в класс **Program**.

```csharp
/// <summary>
/// Writes a message to the log file, and optionally to the console.
/// </summary>
/// <param name="message">The message.</param>
/// <param name="echo">if set to <c>true</c>, write the message to the console.</param>
private static void WriteLine(string message = null, bool echo = false)
{
    writer.WriteLine(message ?? String.Empty);

    if (echo)
    {
        Console.WriteLine(message ?? String.Empty);
    }
}
```

## <a name="create-a-method-to-create-a-set-of-reviews"></a>Создание метода для создания набора проверок

Как правило, у разработчика определена бизнес-логика для идентификации входящих изображений, текста или видео, которые нужно проверять. Однако сейчас просто используйте фиксированный список изображений.

Добавьте следующий метод в класс **Program**.

```csharp
/// <summary>
/// Create the reviews using the fixed list of images.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
private static void CreateReviews(ContentModeratorClient client)
{
    WriteLine(null, true);
    WriteLine("Creating reviews for the following images:", true);

    // Create the structure to hold the request body information.
    List<CreateReviewBodyItem> requestInfo =
        new List<CreateReviewBodyItem>();

    // Create some standard metadata to add to each item.
    List<CreateReviewBodyItemMetadataItem> metadata =
        new List<CreateReviewBodyItemMetadataItem>(
        new CreateReviewBodyItemMetadataItem[] {
            new CreateReviewBodyItemMetadataItem(
                MetadataKey, MetadataValue)
        });

    // Populate the request body information and the initial cached review information.
    for (int i = 0; i < ImageUrls.Length; i++)
    {
        // Cache the local information with which to create the review.
        var itemInfo = new ReviewItem()
        {
            Type = MediaType,
            ContentId = i.ToString(),
            Url = ImageUrls[i],
            ReviewId = null
        };

        WriteLine($" - {itemInfo.Url}; with id = {itemInfo.ContentId}.", true);

        // Add the item informaton to the request information.
        requestInfo.Add(new CreateReviewBodyItem(
            itemInfo.Type, itemInfo.Url, itemInfo.ContentId,
            CallbackEndpoint, metadata));

        // Cache the review creation information.
        reviewItems.Add(itemInfo);
    }

    var reviewResponse = client.Reviews.CreateReviewsWithHttpMessagesAsync(
        "application/json", TeamName, requestInfo);

    // Update the local cache to associate the created review IDs with
    // the associated content.
    var reviewIds = reviewResponse.Result.Body;
    for (int i = 0; i < reviewIds.Count; i++)
    {
        Program.reviewItems[i].ReviewId = reviewIds[i];
    }

    WriteLine(JsonConvert.SerializeObject(
    reviewIds, Formatting.Indented));

    Thread.Sleep(throttleRate);
}
```

## <a name="create-a-method-to-get-the-status-of-existing-reviews"></a>Создание метода для получения состояния существующих проверок

Добавьте следующий метод в класс **Program**.

> [!Note]
> На практике в качестве URL-адреса обратного вызова `CallbackEndpoint` указывается URL-адрес, на который отправляются результаты проверки вручную (с помощью HTTP-запроса POST). Можно изменить этот метод для проверки состояния ожидающих проверок.

```csharp
/// <summary>
    /// Gets the review details from the server.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    private static void GetReviewDetails(ContentModeratorClient client)
    {
        WriteLine(null, true);
        WriteLine("Getting review details:", true);
        foreach (var item in reviewItems)
        {
            var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(
                TeamName, item.ReviewId);

            WriteLine(
                $"Review {item.ReviewId} for item ID {item.ContentId} is " +
                $"{reviewDetail.Result.Body.Status}.", true);
            WriteLine(JsonConvert.SerializeObject(
                reviewDetail.Result.Body, Formatting.Indented));

            Thread.Sleep(throttleRate);
        }
    }
```

## <a name="add-code-to-create-a-set-of-reviews-and-check-its-status"></a>Добавление кода для создания набора проверок и проверки их состояния

Добавьте следующий код в метод **Main**.

Этот код моделирует многие операции, выполняемые для определения списка и управления им, а также для проверки изображений с его помощью. Средства ведения журнала позволяют просматривать созданные вызовы пакета SDK для службы содержимого mModerator объектов ответа.

```csharp
using (TextWriter outputWriter = new StreamWriter(OutputFile, false))
{
    writer = outputWriter;
    using (var client = Clients.NewClient())
    {
        CreateReviews(client);
        GetReviewDetails(client);

        Console.WriteLine();
        Console.WriteLine("Perform manual reviews on the Content Moderator site.");
        Console.WriteLine("Then, press any key to continue.");
        Console.ReadKey();

        Console.WriteLine();
        Console.WriteLine($"Waiting {latencyDelay} seconds for results to propigate.");
        Thread.Sleep(latencyDelay * 1000);

        GetReviewDetails(client);
    }

    writer = null;
    outputWriter.Flush();
    outputWriter.Close();
}

Console.WriteLine();
Console.WriteLine("Press any key to exit...");
Console.ReadKey();
```

## <a name="run-the-program-and-review-the-output"></a>Запуск программы и просмотр выходных данных

Ниже приведен пример выходных данных.

```console
Creating reviews for the following images:
        - https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg; with id = 0.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Pending.
```

Войдите в инструмент проверки Content Moderator, чтобы просмотреть ожидающую проверку изображений с меткой **sc** со значением **true**. Вы также увидите теги по умолчанию **a** и **r**, а также настраиваемые теги, которые были определены в инструменте проверки.

Нажмите кнопку **Next** (Далее) для отправки.

![Проверка изображений модераторами-пользователями](images/moderation-reviews-quickstart-dotnet.PNG)

Нажмите любую клавишу чтобы продолжить.

```console
Waiting 45 seconds for results to propagate.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Complete.

    Press any key to exit...
```

## <a name="check-out-the-following-output-in-the-log-file"></a>Просмотрите приведенные ниже выходные данные в файле журнала.

> [!NOTE]
> В выходном файле строки "\{teamname}" и "\{callbackUrl}" отражают значения для полей `TeamName` и `CallbackEndpoint` соответственно.

Идентификаторы проверок и URL-адреса изображений будут отличаться при каждом запуске приложения. По завершению проверки поле `reviewerResultTags` отображает теги, назначенные рецензентом элементу.

```json
Creating reviews for the following images:
        - https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg; with id = 0.
    [
        "201712i46950138c61a4740b118a43cac33f434",
    ]

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Pending.
    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Pending",
        "reviewerResultTags": [],
        "createdBy": "{teamname}",
        "metadata": [
        {
              "key": "sc",
              "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Complete.
    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Complete",
        "reviewerResultTags": [
        {
              "key": "a",
              "value": "False"
        },
        {
              "key": "r",
              "value": "True"
        },
        {
              "key": "sc",
              "value": "True"
        }
        ],
        "createdBy": "{teamname}",
        "metadata": [
        {
              "key": "sc",
              "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }
```

## <a name="your-callback-url-if-provided-receives-this-response"></a>Этот ответ возвращается на URL-адрес обратного вызова, если он указан.

Вы получите примерно такой ответ:

```json
{
    "ReviewId": "201801i48a2937e679a41c7966e838c92f5e649",
    "ModifiedOn": "2018-01-06T05:04:40.5525865Z",
    "ModifiedBy": "yourusername",
    "CallBackType": "Review",
    "ContentId": "0",
    "ContentType": "Image",
    "Metadata": {
        "sc": "true"
        },
    "ReviewerResultTags": {
        "a": "False",
        "r": "False",
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия

Получить [содержимого пакета SDK для .NET Moderator](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) и загрузите [решение Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) для этого и другие краткие руководства Content Moderator для .NET и начать работу над интеграцией.