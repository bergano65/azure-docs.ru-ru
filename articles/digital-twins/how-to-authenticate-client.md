---
title: Написание кода проверки подлинности приложения
titleSuffix: Azure Digital Twins
description: См. статью как написать код проверки подлинности в клиентском приложении.
author: baanders
ms.author: baanders
ms.date: 10/7/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f2cef34413f46608e8bc35a009a29212af5ddf20
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893600"
---
# <a name="write-client-app-authentication-code"></a>Запись кода проверки подлинности клиентского приложения

После [настройки экземпляра и проверки подлинности Azure Digital двойников](how-to-set-up-instance-portal.md)можно создать клиентское приложение, которое будет использоваться для взаимодействия с экземпляром. После настройки проекта начального клиента необходимо **написать код в этом клиентском приложении для проверки подлинности** в экземпляре Azure Digital двойников.

Azure Digital двойников выполняет проверку подлинности с помощью [маркеров безопасности Azure AD на основе OAUTH 2,0](../active-directory/develop/security-tokens.md#json-web-tokens-jwts-and-claims). Чтобы проверить подлинность пакета SDK, необходимо получить маркер носителя с нужными разрешениями для Azure Digital двойников и передать его вместе с вызовами API. 

В этой статье описывается, как получить учетные данные с помощью `Azure.Identity` клиентской библиотеки. Хотя в этой статье показаны примеры кода для [пакета SDK для .NET (C#)](https://www.nuget.org/packages/Azure.DigitalTwins.Core), можно использовать версию независимо от того, `Azure.Identity` какой пакет SDK вы используете (Дополнительные сведения см. в разделе с [*инструкциями по использованию интерфейсов API цифровых двойников и пакетов SDK*](how-to-use-apis-sdks.md)для Azure Digital двойников).

## <a name="prerequisites"></a>Предварительные требования

Сначала выполните действия по настройке, описанные в разделе [*инструкции. Настройка экземпляра и аутентификации*](how-to-set-up-instance-portal.md). Это обеспечит наличие экземпляра Azure Digital двойников, у пользователя есть разрешения на доступ, и вы настроили разрешения для клиентских приложений. После выполнения всех этих настроек вы сможете писать код клиентского приложения.

Для продолжения Вам потребуется проект клиентского приложения, в котором будет написан код. Если вы еще не настроили проект клиентского приложения, создайте базовый проект на выбранном вами языке для использования с этим руководством.

## <a name="common-authentication-methods-with-azureidentity"></a>Общие методы проверки подлинности с помощью Azure. Identity

`Azure.Identity` — это клиентская библиотека, которая предоставляет несколько методов получения учетных данных, которые можно использовать для получения маркера носителя и проверки подлинности с помощью пакета SDK. Хотя в этой статье приведены примеры в C#, можно просматривать `Azure.Identity` несколько языков, включая...
* [.NET (C#)](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true)
* [Java](https://docs.microsoft.com/java/api/overview/azure/identity-readme?view=azure-java-stable&preserve-view=true)
* [JavaScript](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme?view=azure-node-latest&preserve-view=true)
* [Python](https://docs.microsoft.com/python/api/overview/azure/identity-readme?view=azure-python&preserve-view=true)

Три общих метода получения учетных данных в `Azure.Identity` :
* [Дефаултазурекредентиал](https://docs.microsoft.com/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) предоставляет `TokenCredential` поток проверки подлинности по умолчанию для приложений, которые будут развернуты в Azure. это **рекомендуемый вариант для локальной разработки**. Также можно включить другие два метода, которые рекомендуются в этой статье. Он является оболочкой `ManagedIdentityCredential` и может иметь доступ к `InteractiveBrowserCredential` переменной конфигурации.
* [Манажедидентитикредентиал](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true) работает отлично в тех случаях, когда вам нужны [управляемые удостоверения (MSI)](../active-directory/managed-identities-azure-resources/overview.md), и это хороший кандидат на работу с функциями Azure и развертывание в службах Azure.
* [Интерактивебровсеркредентиал](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) предназначен для интерактивных приложений и может использоваться для создания клиента SDK с проверкой подлинности

В следующем примере показано, как использовать каждый из них с пакетом SDK для .NET (C#).

## <a name="authentication-examples-net-c-sdk"></a>Примеры проверки подлинности: пакет SDK для .NET (C#)

В этом разделе показан пример в C# для использования предоставленного пакета SDK для .NET для написания кода проверки подлинности.

Сначала включите пакет SDK `Azure.DigitalTwins.Core` и `Azure.Identity` пакет в проект. В зависимости от выбранных вами средств можно включить пакеты с помощью диспетчера пакетов Visual Studio или `dotnet` средства командной строки. 

Кроме того, в код проекта необходимо добавить следующие операторы using:

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```

Затем добавьте код для получения учетных данных с помощью одного из методов в `Azure.Identity` .

### <a name="defaultazurecredential-method"></a>Метод Дефаултазурекредентиал

[Дефаултазурекредентиал](https://docs.microsoft.com/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) предоставляет `TokenCredential` поток проверки подлинности по умолчанию для приложений, которые будут развернуты в Azure. это **рекомендуемый вариант для локальной разработки**.

Чтобы использовать учетные данные Azure по умолчанию, вам потребуется URL-адрес экземпляра Azure Digital двойников ([инструкции по поиску](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).

Ниже приведен пример кода для добавления в `DefaultAzureCredential` проект.

```csharp
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new DefaultAzureCredential();
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

### <a name="managedidentitycredential-method"></a>Метод Манажедидентитикредентиал

Метод [манажедидентитикредентиал](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true) работает отлично в тех случаях, когда вам требуются [управляемые удостоверения (MSI)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview), например при работе с функциями Azure.

Это означает, что можно использовать `ManagedIdentityCredential` в том же проекте, что `DefaultAzureCredential` и или `InteractiveBrowserCredential` , для проверки подлинности другой части проекта.

Чтобы использовать учетные данные Azure по умолчанию, вам потребуется URL-адрес экземпляра Azure Digital двойников ([инструкции по поиску](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).

В функции Azure можно использовать учетные данные управляемого удостоверения следующим образом:

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

### <a name="interactivebrowsercredential-method"></a>Метод Интерактивебровсеркредентиал

Метод [интерактивебровсеркредентиал](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) предназначен для интерактивных приложений и позволяет открыть веб-браузер для проверки подлинности. Это можно использовать вместо `DefaultAzureCredential` в тех случаях, когда требуется Интерактивная проверка подлинности.

Чтобы использовать интерактивные учетные данные браузера, потребуется **Регистрация приложения** , имеющего разрешения на доступ к API-интерфейсам цифрового двойников Azure. Инструкции по настройке регистрации приложения см. в разделе " [*Настройка разрешений на доступ к клиентским приложениям*](how-to-set-up-instance-portal.md#set-up-access-permissions-for-client-applications) *" статьи как настроить экземпляр и проверку подлинности*. После настройки регистрации приложения вам потребуется...
* *идентификатор приложения (клиента)* регистрации приложения.
* *идентификатор каталога (клиента)* регистрации приложения.
* URL-адрес экземпляра Azure Digital двойников ([инструкции для поиска](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))

Ниже приведен пример кода для создания клиента пакета SDK с проверкой подлинности с помощью `InteractiveBrowserCredential` .

```csharp
// Your client / app registration ID
private static string clientId = "<your-client-ID>"; 
// Your tenant / directory ID
private static string tenantId = "<your-tenant-ID>";
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-URL>";

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

#### <a name="other-notes-about-authenticating-azure-functions"></a>Другие примечания о проверке подлинности функций Azure

См. [*инструкции по настройке функции Azure для обработки данных*](how-to-create-azure-function.md) для более полного примера, в котором объясняются некоторые важные варианты настройки в контексте функций.

Кроме того, чтобы использовать проверку подлинности в функции, не забудьте:
* [Включение управляемого удостоверения](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* Используйте [переменные среды](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp) соответствующим образом
* Назначьте разрешения приложению "функции", которое позволяет ему получить доступ к интерфейсам API цифровых двойников. Дополнительные сведения о процессах функций Azure см. [*в разделе Практические руководства. Настройка функции Azure для обработки данных*](how-to-create-azure-function.md).

## <a name="other-credential-methods"></a>Другие методы учетных данных

Если выделенные выше сценарии проверки подлинности не охватывают потребности приложения, можно изучить другие типы проверки подлинности, предлагаемые на [**платформе Microsoft Identity**](../active-directory/develop/v2-overview.md#getting-started). Документация по этой платформе охватывает дополнительные сценарии проверки подлинности, упорядоченные по типам приложений.

## <a name="next-steps"></a>Дальнейшие шаги

Узнайте больше о работе системы безопасности в Azure Digital двойников.
* [*Основные понятия: безопасность решений для цифровых двойников Azure*](concepts-security.md)

Кроме того, теперь, когда проверка подлинности настроена, переходите к созданию моделей в экземпляре:
* [*Практическое руководство. Управление настраиваемыми моделями*](how-to-manage-model.md).
