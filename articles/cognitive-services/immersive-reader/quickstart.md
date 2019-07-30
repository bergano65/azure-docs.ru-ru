---
title: Краткое руководство. Создание веб-приложения, которое запускает Иммерсивное средство чтения с использованием C#
titlesuffix: Azure Cognitive Services
description: В рамках этого краткого руководства вы создадите веб-приложения с нуля и добавите функции API "Иммерсивное средство чтения".
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 6386c22044483a0ac4a324397cf2f9d22e83b579
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442859"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>Краткое руководство. Создание веб-приложения, которое запускает иммерсивное средство чтения (C#)

[Иммерсивное средство чтения](https://www.onenote.com/learningtools) — это включительно разработанное решение, в котором реализованы проверенные методы, улучшающие понимание при чтении.

В рамках этого краткого руководства вы создадите веб-приложение с нуля и интегрируете иммерсивное средство чтения с помощью пакета SDK иммерсивного средства чтения. Полностью рабочий пример этого краткого руководства доступен [здесь](https://github.com/microsoft/immersive-reader-sdk/tree/master/samples/quickstart-csharp).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads)
* Ресурс "Иммерсивное средство чтения", настроенный для проверки подлинности Azure Active Directory (Azure AD). Инструкции по настройке см. [здесь](./azure-active-directory-authentication.md). Некоторые из этих созданных значений потребуются вам при настройке свойств примера проекта. Сохраните результаты своего сеанса в текстовом файле для использования в будущем.

## <a name="create-a-web-app-project"></a>Создание проекта веб-приложения

В Visual Studio создайте проект с использованием шаблона веб-приложения ASP.NET Core со встроенным MVC.

![Новый проект](./media/vswebapp.png)

![Новое веб-приложение ASP.NET Core](./media/vsmvc.png)

## <a name="acquire-an-azure-ad-authentication-token"></a>Получение маркера проверки подлинности Azure AD

Для этой части потребуются некоторые значения из описанного выше предварительного требования конфигурации проверки подлинности Azure AD. Вернитесь к текстовому файлу, который вы сохранили в этом сеансе.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Щелкните правой кнопкой мыши проект в _обозревателе решений_, а затем выберите **Управление секретами пользователей**. Откроется файл с именем _secrets.json_. Замените содержимое этого файла следующим кодом и укажите значения пользовательских свойств из примера выше.

```json
{
  "TenantId": YOUR_TENANT_ID,
  "ClientId": YOUR_CLIENT_ID,
  "ClientSecret": YOUR_CLIENT_SECRET,
  "Subdomain": YOUR_SUBDOMAIN
}
```

Откройте файл _Controllers\HomeController.cs_ и замените его содержимое следующим кодом.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.IdentityModel.Clients.ActiveDirectory;

namespace QuickstartSampleWebApp.Controllers
{
    public class HomeController : Controller
    {
        private readonly string TenantId;     // Azure subscription TenantId
        private readonly string ClientId;     // Azure AD ApplicationId
        private readonly string ClientSecret; // Azure AD Application Service Principal password
        private readonly string Subdomain;    // Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')

        public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
        {
            TenantId = configuration["TenantId"];
            ClientId = configuration["ClientId"];
            ClientSecret = configuration["ClientSecret"];
            Subdomain = configuration["Subdomain"];

            if (string.IsNullOrWhiteSpace(TenantId))
            {
                throw new ArgumentNullException("TenantId is null! Did you add that info to secrets.json?");
            }

            if (string.IsNullOrWhiteSpace(ClientId))
            {
                throw new ArgumentNullException("ClientId is null! Did you add that info to secrets.json?");
            }

            if (string.IsNullOrWhiteSpace(ClientSecret))
            {
                throw new ArgumentNullException("ClientSecret is null! Did you add that info to secrets.json?");
            }

            if (string.IsNullOrWhiteSpace(Subdomain))
            {
                throw new ArgumentNullException("Subdomain is null! Did you add that info to secrets.json?");
            }
        }

        public IActionResult Index()
        {
            return View();
        }

        [Route("subdomain")]
        public string GetSubdomain()
        {
            return Subdomain;
        }

        [Route("token")]
        public async Task<string> GetToken()
        {
            return await GetTokenAsync();
        }

        /// <summary>
        /// Get an Azure AD authentication token
        /// </summary>
        private async Task<string> GetTokenAsync()
        {
            string authority = $"https://login.windows.net/{TenantId}";
            const string resource = "https://cognitiveservices.azure.com/";

            AuthenticationContext authContext = new AuthenticationContext(authority);
            ClientCredential clientCredential = new ClientCredential(ClientId, ClientSecret);

            AuthenticationResult authResult = await authContext.AcquireTokenAsync(resource, clientCredential);

            return authResult.AccessToken;
        }
    }
}
```

## <a name="add-the-microsoftidentitymodelclientsactivedirectory-nuget-package"></a>Добавление пакета NuGet Microsoft.IdentityModel.Clients.ActiveDirectory

В приведенном выше коде используется пакет NuGet **Microsoft.IdentityModel.Clients.ActiveDirectory**, поэтому необходимо добавить в проект ссылку на этот пакет.

Откройте консоль диспетчера пакетов NuGet, выбрав **Средства -> Диспетчер пакетов NuGet -> Консоль диспетчера пакетов**, и введите следующее:

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.1.0
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
    <script type='text/javascript' src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js'></script>
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

    function getSubdomainAsync() {
        return new Promise((resolve) => {
            $.ajax({
                url: '/subdomain',
                type: 'GET',
                success: subdomain => {
                    resolve(subdomain);
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
        var subdomain = await getSubdomainAsync();

        ImmersiveReader.launchAsync(token, subdomain, content, { uiZIndex: 1000000 });
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
