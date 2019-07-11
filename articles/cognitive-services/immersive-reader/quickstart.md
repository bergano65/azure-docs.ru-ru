---
title: Краткое руководство. Создание веб-приложения, которое запускает иммерсивное средство чтения (C#)
titlesuffix: Azure Cognitive Services
description: В рамках этого краткого руководства вы создадите веб-приложения с нуля и добавите функции иммерсивного средства чтения.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 77d95383c801038c256ccb2bf386ddf06048cf78
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67311809"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>Краткое руководство. Создание веб-приложения, которое запускает иммерсивное средство чтения (C#)

[Иммерсивное средство чтения](https://www.onenote.com/learningtools) — это включительно разработанное решение, в котором реализованы проверенные методы, улучшающие понимание при чтении.

В рамках этого краткого руководства вы создадите веб-приложение с нуля и интегрируете иммерсивное средство чтения с помощью пакета SDK иммерсивного средства чтения. Полностью рабочий пример этого краткого руководства доступен [здесь](https://github.com/Microsoft/immersive-reader-sdk/samples/quickstart-csharp).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads)
* Ключ подписки иммерсивного средства чтения. Получите ключ, следуя [этим инструкциям](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).

## <a name="create-a-web-app-project"></a>Создание проекта веб-приложения

В Visual Studio создайте проект с использованием шаблона веб-приложения ASP.NET Core со встроенным MVC.

![Новый проект](./media/vswebapp.png)

![Новое веб-приложение ASP.NET Core](./media/vsmvc.png)

## <a name="acquire-an-access-token"></a>Получение маркера доступа

Вам потребуются ключ подписки и конечная точка для выполнения следующего шага. Ключ подписки на портале Azure вы сможете найти на странице ключей вашего ресурса иммерсивного средства чтения. Конечную точку вы сможете найти на странице "Обзор".

Щелкните правой кнопкой мыши проект в _обозревателе решений_, а затем выберите **Управление секретами пользователей**. Откроется файл с именем _secrets.json_. Замените содержимое этого файла следующим, указав при необходимости свой ключ подписки и конечную точку.

```json
{
  "SubscriptionKey": YOUR_SUBSCRIPTION_KEY,
  "Endpoint": YOUR_ENDPOINT
}
```

Откройте файл _Controllers\HomeController.cs_ и замените содержимое класса `HomeController` следующим кодом.

```csharp
public class HomeController : Controller
{
    private readonly string SubscriptionKey;
    private readonly string Endpoint;

    public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
    {
        SubscriptionKey = configuration["SubscriptionKey"];
        Endpoint = configuration["Endpoint"];

        if (string.IsNullOrEmpty(Endpoint) || string.IsNullOrEmpty(SubscriptionKey))
        {
            throw new ArgumentNullException("Endpoint or subscriptionKey is null!");
        }
    }

    public IActionResult Index()
    {
        return View();
    }

    [Route("token")]
    public async Task<string> Token()
    {
        return await GetTokenAsync();
    }

    /// <summary>
    /// Exchange your Azure subscription key for an access token
    /// </summary>
    private async Task<string> GetTokenAsync()
    {
        using (var client = new System.Net.Http.HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", SubscriptionKey);
            using (var response = await client.PostAsync(Endpoint, null))
            {
                return await response.Content.ReadAsStringAsync();
            }
        }
    }
}
```

## <a name="add-sample-content"></a>Добавление примеров содержимого

Теперь мы добавим некоторые примеры содержимого для этого веб-приложения. Откройте файл _Views\Home\Index.cshtml_ и замените автоматически созданный код этим примером:

```html
<h1 id='title'>Geography</h1>
<span id='content'>
    <p>The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers. Landforms are sometimes called physical features. It is important for students to know about the physical geography of Earth. The seasons, the atmosphere and all the natural processes of Earth affect where people are able to live. Geography is one of a combination of factors that people use to decide where they want to live.</p>
</span>

<div class='immersive-reader-button' data-button-style='iconAndText' onclick='launchImmersiveReader()'></div>

@section scripts {
<script type='text/javascript' src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.1.js'></script>
<script type='text/javascript' src='https://code.jquery.com/jquery-3.3.1.min.js'></script>
<script type='text/javascript'>
    function getImmersiveReaderTokenAsync() {
        return new Promise((resolve) => {
            $.ajax({
                url: '/token',
                type: 'GET',
                success: token => {
                    resolve(token);
                }
            });
        });
    }

    async function launchImmersiveReader() {
        const content = {
            title: document.getElementById('title').innerText,
            chunks: [ {
                content: document.getElementById('content').innerText,
                lang: 'en'
            } ]
        };

        const token = await getImmersiveReaderTokenAsync();
        ImmersiveReader.launchAsync(token, content, { uiZIndex: 1000000 });
    }
</script>
}
```

## <a name="build-and-run-the-app"></a>Создание и запуск приложения

В строке меню выберите **Отладка > Начать отладку** или нажмите клавишу **F5**, чтобы запустить приложение.

В браузере вы уведите:

![Пример приложения](./media/quickstart-result.png)

При нажатии кнопки "иммерсивное средство чтения" появится запущенное иммерсивное средство чтения с содержимым на странице.

![Иммерсивное средство чтения](./media/quickstart-immersive-reader.png)

## <a name="next-steps"></a>Дополнительная информация

* Ознакомьтесь с [учебником](./tutorial.md), чтобы узнать, что еще можно сделать с помощью пакета SDK иммерсивного средства чтения.
* Ознакомьтесь с разделом о [пакете SDK для иммерсивного средства чтения](https://github.com/Microsoft/immersive-reader-sdk) и [справочнике по этому пакету](./reference.md).