---
title: Управление пользователями с помощью Microsoft Graph API
titleSuffix: Azure AD B2C
description: Управление пользователями в клиенте Azure AD B2C путем вызова API Microsoft Graph и использования удостоверения приложения для автоматизации процесса.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/13/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff3cd858de86d21637f4a7a9ab9d9a83c7022f5a
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178880"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>Управление Azure AD B2C учетными записями пользователей с помощью Microsoft Graph

Microsoft Graph позволяет управлять учетными записями пользователей в каталоге Azure AD B2C, предоставляя методы создания, чтения, обновления и удаления в API-интерфейсе Microsoft Graph. Вы можете перенести существующее хранилище пользователя в клиент Azure AD B2C и выполнить другие операции по управлению учетными записями пользователей, вызвав API Microsoft Graph.

В следующих разделах представлены ключевые аспекты Azure AD B2C управления пользователями с помощью API Microsoft Graph. Операции, типы и свойства API Microsoft Graph, представленные здесь, являются подмножеством, которое отображается в справочной документации по API Microsoft Graph.

## <a name="register-a-management-application"></a>Регистрация приложения управления

Для взаимодействия с ресурсами в клиенте Azure AD B2C необходимо зарегистрировать приложение, которое предоставляет разрешения на доступ к этим ресурсам.

Выполните действия, описанные в этой статье, чтобы создать регистрацию приложения, которую может использовать приложение управления.

[Управление Azure AD B2C с помощью Microsoft Graph](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>Операции Microsoft Graph управления пользователями

В [Microsoft Graph API](/graph/api/resources/user)доступны следующие операции управления пользователями:

- [Получение списка пользователей](/graph/api/user-list)
- [Создание пользователя](/graph/api/user-post-users)
- [Получение пользователя](/graph/api/user-get)
- [Обновление пользователя](/graph/api/user-update)
- [Удаление пользователя](/graph/api/user-delete)


## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>Пример кода: программное управление учетными записями пользователей

Этот пример кода является консольным приложением .NET Core, которое использует [пакет SDK для Microsoft Graph](/graph/sdks/sdks-overview) для взаимодействия с Microsoft Graph API. В его коде показано, как вызывать API для программного управления пользователями в клиенте Azure AD B2C.
Вы можете [скачать пример архива](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*. zip), [просмотреть репозиторий](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) на сайте GitHub или клонировать репозиторий:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

После получения примера кода настройте его для своей среды, а затем выполните сборку проекта:

1. Откройте проект в [Visual Studio](https://visualstudio.microsoft.com) или [Visual Studio Code](https://code.visualstudio.com).
1. Откройте файл `src/appsettings.json`.
1. В `appSettings` разделе замените `your-b2c-tenant` именем своего клиента и `Application (client) ID` `Client secret` значениями для регистрации приложения управления (см. раздел [Регистрация приложения управления](#register-a-management-application) в этой статье).
1. Откройте окно консоли в локальном клоне репозитория, перейдите в `src` каталог, а затем выполните сборку проекта:
    ```console
    cd src
    dotnet build
    ```
1. Запустите приложение, выполнив команду `dotnet`:

    ```console
    dotnet bin/Debug/netcoreapp3.1/b2c-ms-graph.dll
    ```

В приложении отображается список команд, которые можно выполнить. Например, можно получить всех пользователей, получить одного пользователя, удалить пользователя, обновить пароль пользователя и выполнить групповое импортирование.

### <a name="code-discussion"></a>Обсуждение кода

В примере кода используется [пакет SDK Microsoft Graph](/graph/sdks/sdks-overview), предназначенный для упрощения создания высококачественных, эффективных и устойчивых приложений, обращающихся к Microsoft Graph.

Для любого запроса к Microsoft Graph API требуется маркер доступа для проверки подлинности. Решение использует пакет NuGet [Microsoft. Graph. auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) , который предоставляет оболочку на основе сценария проверки подлинности Майкрософт (MSAL) для использования с пакетом SDK для Microsoft Graph.

`RunAsync`Метод в файле _Program.CS_ :

1. Чтение параметров приложения из _appsettings.js_ файла
1. Инициализирует поставщик проверки подлинности с помощью потока [предоставления учетных данных клиента OAuth 2,0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) . Используя поток предоставления учетных данных клиента, приложение может получить маркер доступа для вызова API Microsoft Graph.
1. Настраивает Клиент службы Microsoft Graph с помощью поставщика проверки подлинности:

    ```csharp
    // Read application settings from appsettings.json (tenant ID, app ID, client secret, etc.)
    AppSettings config = AppSettingsFile.ReadFromJsonFile();

    // Initialize the client credential auth provider
    IConfidentialClientApplication confidentialClientApplication = ConfidentialClientApplicationBuilder
        .Create(config.AppId)
        .WithTenantId(config.TenantId)
        .WithClientSecret(config.ClientSecret)
        .Build();
    ClientCredentialProvider authProvider = new ClientCredentialProvider(confidentialClientApplication);

    // Set up the Microsoft Graph service client with client credentials
    GraphServiceClient graphClient = new GraphServiceClient(authProvider);
    ```

Затем инициализированный *графсервицеклиент* используется в _UserService.CS_ для выполнения операций управления пользователями. Например, получение списка учетных записей пользователей в клиенте:

```csharp
public static async Task ListUsers(GraphServiceClient graphClient)
{
    Console.WriteLine("Getting list of users...");

    // Get all users (one page)
    var result = await graphClient.Users
        .Request()
        .Select(e => new
        {
            e.DisplayName,
            e.Id,
            e.Identities
        })
        .GetAsync();

    foreach (var user in result.CurrentPage)
    {
        Console.WriteLine(JsonConvert.SerializeObject(user));
    }
}
```

[Выполнение вызовов API с помощью Microsoft Graph пакетов SDK](/graph/sdks/create-requests) включает сведения о том, как считывать и записывать данные из Microsoft Graph, использовать `$select` для управления возвращаемыми свойствами, предоставлять настраиваемые параметры запроса и использовать `$filter` `$orderBy` Параметры запроса и.

## <a name="next-steps"></a>Дальнейшие действия

Полный индекс операций Microsoft Graph API, поддерживаемых для ресурсов Azure AD B2C, см. в разделе [операции Microsoft Graph, доступные для Azure AD B2C](microsoft-graph-operations.md).

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
