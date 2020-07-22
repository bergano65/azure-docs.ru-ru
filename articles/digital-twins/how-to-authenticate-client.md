---
title: Аутентификация клиентского приложения
titleSuffix: Azure Digital Twins
description: См. статью Проверка подлинности клиентского приложения в службе Digital двойников.
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e52307c92d9371af6479f64841c6f269ed10e4b4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85390828"
---
# <a name="authenticate-a-client-application-with-azure-digital-twins"></a>Проверка подлинности клиентского приложения с помощью Azure Digital двойников

После [создания экземпляра Digital двойников для Azure](how-to-set-up-instance.md)можно создать клиентское приложение, которое будет использоваться для взаимодействия с экземпляром. После настройки проекта начального клиента в этой статье показано, как правильно проверить подлинность этого клиентского приложения с помощью экземпляра Azure Digital двойников.

Это осуществляется в два этапа:
1. Регистрация приложения
2. Написание кода проверки подлинности в клиентском приложении

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-an-app-registration"></a>Регистрация приложения

Для проверки подлинности в Azure Digital двойников из клиентского приложения необходимо настроить **регистрацию приложения** в [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md).

Регистрация приложения — это место, где вы настраиваете разрешения на доступ к [API-интерфейсам цифровых двойников Azure](how-to-use-apis-sdks.md). Клиентское приложение выполняет проверку подлинности при регистрации приложения, и в результате ему предоставляются настроенные разрешения на доступ к API.

Чтобы создать регистрацию приложения, необходимо указать идентификаторы ресурсов для API цифровых двойников Azure и базовые разрешения для API. В рабочем каталоге откройте новый файл и введите следующий фрагмент JSON, чтобы настроить следующие сведения: 

```json
[{
    "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
    "resourceAccess": [
     {
       "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
       "type": "Scope"
     }
    ]
}]
``` 

Сохраните этот файл как *manifest.js*.

> [!NOTE] 
> В некоторых местах `https://digitaltwins.azure.net` для идентификатора приложения Azure Digital двойников вместо GUID можно использовать удобную для восприятия строку `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Например, многие примеры по всему этому набору документации используют проверку подлинности с помощью библиотеки MSAL, а для этого можно использовать понятную строку. Однако на этом этапе создания регистрации приложения требуется форма идентификатора GUID, как показано выше. 

В окне Cloud Shell щелкните значок "Отправить/скачать файлы" и выберите "Отправить".

:::image type="content" source="media/how-to-authenticate-client/upload-extension.png" alt-text="Окно Cloud Shell, в котором отображается выбор параметра отправки":::
Перейдите к только что созданному *manifest.js* и нажмите кнопку "Открыть".

Затем выполните следующую команду, чтобы создать регистрацию приложения (заменяя заполнители по мере необходимости):

```azurecli
az ad app create --display-name <name-for-your-app> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

Выходные данные этой команды выглядят примерно так.

:::image type="content" source="media/how-to-authenticate-client/new-app-registration.png" alt-text="Регистрация нового приложения AAD":::

После создания регистрации приложения перейдите по [этой ссылке](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) , чтобы перейти на страницу обзора регистрации приложений AAD на портал Azure.

В этом обзоре выберите в списке только что созданную регистрацию приложения. Это приведет к открытию подробных сведений на следующей странице:

:::image type="content" source="media/how-to-authenticate-client/get-authentication-ids.png" alt-text="Портал Azure: идентификаторы проверки подлинности":::

Запишите *идентификатор приложения (клиента)* и *идентификатор каталога (клиента)* , показанные на **странице.** Эти значения будут использоваться позже для проверки подлинности клиентского приложения в API-интерфейсах Azure Digital двойников.

> [!NOTE]
> В зависимости от сценария может потребоваться внести дополнительные изменения в регистрацию приложения. Ниже приведены некоторые распространенные требования, которые могут потребоваться для удовлетворения.
> * Активация общедоступного клиента
> * Задать конкретные URL-адреса ответа для доступа к Web и рабочему столу
> * Разрешить неявные потоки проверки подлинности OAuth2
> * Если ваша подписка Azure создана с помощью учетная запись Майкрософт, например Live, Xbox или Hotmail, необходимо настроить *сигнинаудиенце* для регистрации приложения для поддержки личных учетных записей.
> Самый простой способ настроить эти параметры — использовать [портал Azure](https://portal.azure.com/). Дополнительные сведения об этом процессе см. [в статье регистрация приложения на платформе Microsoft Identity](https://docs.microsoft.com/graph/auth-register-app-v2).

## <a name="write-client-app-authentication-code-net-c-sdk"></a>Запись кода проверки подлинности клиентского приложения: пакет SDK для .NET (C#)

В этом разделе описывается код, который потребуется включить в клиентское приложение для завершения процесса проверки подлинности с помощью пакета SDK для .NET (C#).
Пакет SDK для C# Digital двойников является частью пакета Azure SDK для .NET. Он находится здесь: [Клиентская библиотека Digital двойника Azure IOT для .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core).

### <a name="prerequisites"></a>Предварительные условия

Если у вас еще нет проекта "начальное клиентское приложение", создайте базовый проект .NET для использования с этим руководством.

Чтобы использовать пакет SDK для .NET, необходимо включить в проект следующие пакеты:
* `Azure.DigitalTwins.Core`(версия `1.0.0-preview.2` )
* `Azure.Identity`

В зависимости от выбранных вами инструментов это можно сделать с помощью диспетчера пакетов Visual Studio или `dotnet` средства командной строки. 

### <a name="authentication-and-client-creation-net"></a>Проверка подлинности и создание клиентов: .NET

Для проверки подлинности с помощью пакета SDK для .NET используйте один из методов получения учетных данных, определенных в библиотеке [Azure. Identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet) .

Ниже приведены два наиболее часто используемых: 
* [Интерактивебровсеркредентиал](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet). Этот метод предназначен для интерактивных приложений и позволяет открыть веб-браузер для проверки подлинности.
* [Манажедидентитикредентиал](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet). Этот метод прекрасно работает в тех случаях, когда вам требуются [управляемые удостоверения (MSI)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview), например при работе с функциями Azure. 

Вам также потребуются следующие операторы using:

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```

Чтобы использовать учетные данные интерактивного браузера для создания клиента пакета SDK с проверкой подлинности, добавьте следующий код:

```csharp
// Your client / app registration ID
private static string clientId = "<your-client-ID>"; 
// Your tenant / directory ID
private static string tenantId = "<your-tenant-ID>";
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new InteractiveBrowserCredential(tenantId, clientId);
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

>[!NOTE]
> Хотя идентификатор клиента, идентификатор клиента и URL-адрес экземпляра можно разместить непосредственно в коде, как показано выше, рекомендуется заставить код получать эти значения из файла конфигурации или переменной среды.

В функции Azure можно использовать учетные данные управляемого удостоверения следующим образом:

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

См. [инструкции по настройке функции Azure для обработки данных](how-to-create-azure-function.md) для более полного примера, в котором объясняются некоторые важные варианты настройки в контексте функций.

Кроме того, чтобы использовать проверку подлинности в функции, не забудьте:
* [Включение управляемого удостоверения](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* [Переменные среды](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp)
* Назначьте разрешения приложению "функции", которое позволяет ему получить доступ к интерфейсам API цифровых двойников. Дополнительные сведения см. [в разделе как настроить функцию Azure для обработки данных](how-to-create-azure-function.md) .

## <a name="authentication-in-an-autorest-generated-sdk"></a>Проверка подлинности в пакете SDK, созданном автоматически

Если вы не используете .NET, вы можете создать библиотеку SDK на любом языке, как описано в статье [Создание настраиваемых пакетов SDK для Azure Digital двойников с помощью](how-to-create-custom-sdks.md)функции автозаписи.

В этом разделе объясняется, как выполнить проверку подлинности в этом случае.

### <a name="prerequisites"></a>Предварительные условия

В этом примере используется пакет SDK typescript, созданный с помощью автоотдыха. В результате также требуется:
* [msal-JS](https://github.com/AzureAD/microsoft-authentication-library-for-js)
* [MS-RESTful-JS](https://github.com/Azure/ms-rest-js)

### <a name="minimal-authentication-code-sample"></a>Пример кода минимальной проверки подлинности

Для проверки подлинности приложения .NET с помощью служб Azure можно использовать следующий минимальный код в клиентском приложении.

Вам потребуются *идентификатор вашего приложения (клиента)* и *каталога (клиента)* , а также URL-адрес вашего экземпляра Digital двойников для Azure.

> [!TIP]
> URL-адрес экземпляра Azure Digital двойников создается путем добавления *https://* в начало *имени узла*для цифрового двойникова Azure. Чтобы просмотреть *имя узла*, а также все свойства экземпляра, можно запустить `az dt show --dt-name <your-Azure-Digital-Twins-instance>` . `az account show --query tenantId`Для просмотра *идентификатора каталога (клиента)* можно использовать команду. 

```javascript
import * as Msal from "msal";
import { TokenCredentials } from "@azure/ms-rest-js";
// Autorest-generated SDK
import { AzureDigitalTwinsAPI } from './azureDigitalTwinsAPI';

// Client / app registration ID
var ClientId = "<your-client-ID>";
// Azure tenant / directory ID
var TenantId = "<your-tenant-ID>";
// URL of the Azure Digital Twins instance
var AdtInstanceUrl = "<your-instance-URL>"; 

var AdtAppId = "https://digitaltwins.azure.net";

let client = null;

export async function login() {

    const msalConfig = {
        auth: {
            clientId: ClientId,
            redirectUri: "http://localhost:3000",
            authority: "https://login.microsoftonline.com/"+TenantId
        }
    };

    const msalInstance = new Msal.UserAgentApplication(msalConfig);

    msalInstance.handleRedirectCallback((error, response) => {
        // handle redirect response or error
    });

    var loginRequest = {
        scopes: [AdtAppId + "/.default"] 
    };

    try {
        await msalInstance.loginPopup(loginRequest)
        var accessToken;
        // if the user is already logged in you can acquire a token
        if (msalInstance.getAccount()) {
            var tokenRequest = {
                scopes: [AdtAppId + "/.default"]
            };
            try {
                const response = await msalInstance.acquireTokenSilent(tokenRequest);
                accessToken = response.accessToken;
            } catch (err) {
                if (err.name === "InteractionRequiredAuthError") {
                    const response = await msalInstance.acquireTokenPopup(tokenRequest)
                    accessToken = response.accessToken;
                }
            }
        }
        if (accessToken!=null)
        {
            var tokenCredentials = new TokenCredentials(accessToken);
                
            // Add token and server URL to service instance
            const clientConfig = {
                baseUri: AdtInstanceUrl
            };
            client = new AzureDigitalTwinsAPI(tokenCredentials, clientConfig);
            appDataStore.client = client;
        }
    } catch (err) {
        ...
    }
}
```

Обратите внимание, что когда код, указанный выше, помещает идентификатор клиента, идентификатор клиента и URL-адрес экземпляра непосредственно в код для простоты, рекомендуется заставить код получать эти значения из файла конфигурации или переменной среды.

MSAL обладает многими дополнительными возможностями, которые можно использовать для реализации таких возможностей, как кэширование и другие потоки проверки подлинности. Дополнительные сведения об этом см. в [статье Обзор библиотеки проверки подлинности Майкрософт (MSAL)](../active-directory/develop/msal-overview.md).

## <a name="next-steps"></a>Дальнейшие шаги

Узнайте больше о работе системы безопасности в Azure Digital двойников.
* [Основные понятия: безопасность решений для цифровых двойников Azure](concepts-security.md)

Кроме того, теперь, когда проверка подлинности настроена, переходите к созданию моделей в экземпляре:
* [Руководство. Управление пользовательскими моделями](how-to-manage-model.md)