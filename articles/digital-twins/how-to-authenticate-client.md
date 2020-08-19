---
title: Написание кода проверки подлинности приложения
titleSuffix: Azure Digital Twins
description: См. статью как написать код проверки подлинности в клиентском приложении.
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-javascript
ms.openlocfilehash: 008d5f22a48fdd31c90e63643adc94b26a975ca2
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589373"
---
# <a name="write-client-app-authentication-code"></a>Запись кода проверки подлинности клиентского приложения

После [настройки экземпляра и проверки подлинности Azure Digital двойников](how-to-set-up-instance-scripted.md)можно создать клиентское приложение, которое будет использоваться для взаимодействия с экземпляром. После настройки проекта начального клиента в этой статье показано **, как написать код в клиентском приложении для проверки подлинности** в экземпляре Azure Digital двойников.

В этой статье есть два подхода к примерам кода. Вы можете использовать тот, который вам подходит, в зависимости от выбранного языка:
* В первом разделе примера кода используется пакет SDK Azure Digital двойников .NET (C#). Пакет SDK является частью пакета SDK для Azure для .NET и находится здесь: [*Клиентская библиотека Digital двойника для Azure IOT для .NET*](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core).
* Второй раздел примера кода предназначен для пользователей, не использующих пакет SDK для .NET, и вместо этого использует пакеты SDK, созданные автоматически на других языках. Дополнительные сведения об этой стратегии см. в разделе [*инструкции. Создание настраиваемых пакетов SDK для Azure Digital двойников с помощью автоrestful*](how-to-create-custom-sdks.md).

Дополнительные сведения об API и пакетах SDK для Azure Digital двойников см. в статье [*Использование интерфейсов API и пакетов SDK для цифровых двойников Azure*](how-to-use-apis-sdks.md).

## <a name="prerequisites"></a>Предварительные требования

Сначала выполните действия по настройке, описанные в разделе [*инструкции. Настройка экземпляра и аутентификации*](how-to-set-up-instance-scripted.md). Это обеспечит наличие экземпляра Azure Digital двойников, у пользователя есть разрешения на доступ, и вы настроили разрешения для клиентских приложений. После выполнения всех этих настроек вы сможете писать код клиентского приложения.

Для продолжения Вам потребуется проект клиентского приложения, в котором будет написан код. Если вы еще не настроили проект клиентского приложения, создайте базовый проект на выбранном вами языке для использования с этим руководством.

## <a name="authentication-and-client-creation-net-c-sdk"></a>Проверка подлинности и создание клиентов: пакет SDK для .NET (C#)

Во-первых, включите в проект следующие пакеты, чтобы использовать пакет SDK для .NET и средства проверки подлинности для этого руководства:
* `Azure.DigitalTwins.Core` (версия `1.0.0-preview.2` )
* `Azure.Identity` (версия `1.1.1` )

В зависимости от выбранных вами средств можно включить пакеты с помощью диспетчера пакетов Visual Studio или `dotnet` средства командной строки. 

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

См. [*инструкции по настройке функции Azure для обработки данных*](how-to-create-azure-function.md) для более полного примера, в котором объясняются некоторые важные варианты настройки в контексте функций.

Кроме того, чтобы использовать проверку подлинности в функции, не забудьте:
* [Включение управляемого удостоверения](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* Используйте [переменные среды](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp) соответствующим образом
* Назначьте разрешения приложению "функции", которое позволяет ему получить доступ к интерфейсам API цифровых двойников. Дополнительные сведения о процессах функций Azure см. [*в разделе Практические руководства. Настройка функции Azure для обработки данных*](how-to-create-azure-function.md).

## <a name="authentication-with-an-autorest-generated-sdk"></a>Проверка подлинности с помощью пакета SDK, созданного автоматически

Если вы не используете .NET, вы можете создать библиотеку SDK на любом языке, как описано в статье [*Создание настраиваемых пакетов SDK для Azure Digital двойников с помощью*](how-to-create-custom-sdks.md)функции автозаписи.

В этом разделе объясняется, как выполнить проверку подлинности в этом случае.

### <a name="prerequisites"></a>Предварительные требования

Во-первых, необходимо выполнить действия по созданию настраиваемого пакета SDK с помощью функции автозаполнения, выполнив действия, описанные в разделе [*инструкции. Создание настраиваемых пакетов SDK для Azure Digital двойников с*](how-to-create-custom-sdks.md)назначением.

В этом примере используется пакет SDK typescript, созданный с помощью автоотдыха. В результате также требуется:
* [msal-JS](https://github.com/AzureAD/microsoft-authentication-library-for-js)
* [MS-RESTful-JS](https://github.com/Azure/ms-rest-js)

### <a name="minimal-authentication-code-sample"></a>Пример кода минимальной проверки подлинности

Для проверки подлинности приложения с помощью служб Azure можно использовать следующий минимальный код в клиентском приложении.

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

MSAL обладает многими дополнительными возможностями, которые можно использовать для реализации таких возможностей, как кэширование и другие потоки проверки подлинности. Дополнительные сведения об этом см. в [*статье Обзор библиотеки проверки подлинности Майкрософт (MSAL)*](../active-directory/develop/msal-overview.md).

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о работе системы безопасности в Azure Digital двойников.
* [*Основные понятия: безопасность решений для цифровых двойников Azure*](concepts-security.md)

Кроме того, теперь, когда проверка подлинности настроена, переходите к созданию моделей в экземпляре:
* [*Практическое руководство. Управление настраиваемыми моделями*](how-to-manage-model.md).