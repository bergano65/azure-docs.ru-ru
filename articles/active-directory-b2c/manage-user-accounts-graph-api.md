---
title: Управление пользователями с помощью API Графика Майкрософт
titleSuffix: Azure AD B2C
description: Как управлять пользователями в наемщике Azure AD B2C, вызывая API Microsoft Graph и используя идентификатор приложения для автоматизации процесса.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 42596ba5470c6062efba4fd1050c1c9745b76e80
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637327"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>Управление учетными записями пользователей Azure AD B2C с помощью Microsoft Graph

Microsoft Graph позволяет управлять учетными записями пользователей в каталоге Azure AD B2C, предоставляя методы создания, чтения, обновления и удаления в API Microsoft Graph. Можно перенести существующий магазин пользователей в наниматель Azure AD B2C и выполнить другие операции по управлению учетными записями пользователей, позвонив в API Microsoft Graph.

В следующих разделах представлены ключевые аспекты управления пользователями Azure AD B2C с помощью API Microsoft Graph. Операции, типы и свойства Microsoft Graph API, представленные здесь, представляют собой подмножество, которое отображается в справочной документации Microsoft GraphPI.

## <a name="register-a-management-application"></a>Регистрация управленческого приложения

Перед тем, как какое-либо приложение или скрипт управления пользователями, которые вы пишете, можно взаимодействовать с ресурсами в вашем арендаторе Azure AD B2C, вам нужна регистрация приложения, которая предоставляет на это разрешения.

Выполните последующие действия в этой статье, чтобы создать регистрацию приложений, которую может использовать ваше приложение управления:

[Управление Azure AD B2C с помощью графика Microsoft](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>Операции управления пользователями Microsoft Graph

Следующие операции управления пользователями доступны в [API Microsoft Graph:](https://docs.microsoft.com/graph/api/resources/user)

- [Получить список пользователей](https://docs.microsoft.com/graph/api/user-list)
- [Создание пользователя](https://docs.microsoft.com/graph/api/user-post-users)
- [Получить пользователя](https://docs.microsoft.com/graph/api/user-get)
- [Обновление пользователя](https://docs.microsoft.com/graph/api/user-update)
- [Удаление пользователя](https://docs.microsoft.com/graph/api/user-delete)

## <a name="user-properties"></a>Свойства пользователя

### <a name="display-name-property"></a>Отображение свойства имени

Это `displayName` имя для отображения в управлении пользователем портала Azure для пользователя, а в маркере доступа Azure AD B2C возвращается в приложение. Это — обязательное свойство.

### <a name="identities-property"></a>Свойство идентификационных данных

Учетная запись клиента, которая может быть потребителем, партнером или гражданином, может быть связана с этими типами идентификации:

- **Локальная** идентификация - Имя пользователя и пароль хранятся локально в каталоге Azure AD B2C. Мы часто называем эти удостоверения "местными учетными записями".
- **Федеративная** идентификация - Также известная как *социальная* или *корпоративная* учетная записи, личность пользователя управляется федеративным поставщиком идентификационных данных, таким как Facebook, Microsoft, ADFS или Salesforce.

Пользователь с учетной записью клиента может войти в систему с несколькими удостоверениями. Например, имя пользователя, электронная почта, идентификатор сотрудника, правительственный идентификатор и другие. Одна учетная запись может иметь несколько идентификационных данных, как местных, так и социальных, с одним и тем же паролем.

В Microsoft Graph API в атрибуте пользователя, `identities` который имеет тип [objectIdentity,][graph-objectIdentity]сохраняются как локальные, так и федеративные идентификаторы. Коллекция `identities` представляет собой набор идентификационных данных, используемых для ввоза в учетную запись пользователя. Эта коллекция позволяет пользователю войти в учетную запись пользователя с любым из связанных с ним идентификаторов.

| Свойство   | Тип |Описание|
|:---------------|:--------|:----------|
|signInType|строка| Укажите типы вхданных пользователем в каталог в каталоге. Для локального `emailAddress` `emailAddress1`счета: `emailAddress3` `userName`, `emailAddress2`, , , или любой другой тип вам нравится. Социальный счет должен `federated`быть установлен на .|
|issuer|строка|Определяет эмитента идентификации. Для локальных учетных записей (если **signInType** не `federated`является), это свойство является локальным доменным именем локального клиента B2C по умолчанию, например. `contoso.onmicrosoft.com` Для социальной идентичности (где `federated` **signInType** является) значение имя эмитента, например,`facebook.com`|
|эмитентAssignedId|строка|Упоняет уникальный идентификатор, присвоенный пользователю эмитентом. Сочетание **эмитента** и **эмитентаAssignedId** должно быть уникальным в вашем арендаторе. Для локальной учетной записи, `emailAddress` `userName`когда **signInType** установлен или представляет имя ввода для пользователя.<br>Когда **signInType** установлен на: <ul><li>`emailAddress`(или начинается с `emailAddress` как `emailAddress1`) **issuerAssignedId** должен быть действительным адресом электронной почты</li><li>`userName`(или любое другое значение), **issuerAssignedId** должен быть действительной [локальной частью адреса электронной почты](https://tools.ietf.org/html/rfc3696#section-3)</li><li>`federated`, **issuerAssignedId** представляет уникальный идентификатор federated account</li></ul>|

Следующее **свойство идентификационных данных,** с локальной учетной записью с именем введома, адресом электронной почты в качестве входиный адреса и социальной идентичностью. 

 ```JSON
 "identities": [
     {
       "signInType": "userName",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "johnsmith"
     },
     {
       "signInType": "emailAddress",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "jsmith@yahoo.com"
     },
     {
       "signInType": "federated",
       "issuer": "facebook.com",
       "issuerAssignedId": "5eecb0cd"
     }
   ]
 ```

Для федеративных идентификационных данных, в зависимости от поставщика идентификационных данных, **эмитентAssignedId** является уникальным значением для данного пользователя для одного приложения или учетной записи разработки. Настройте политику Azure AD B2C с тем же идентификатором приложения, который ранее был назначен социальным поставщиком или другим приложением в той же учетной записи разработки.

### <a name="password-profile-property"></a>Свойство профиля паролей

Для локальной идентификации требуется свойство **passwordProfile,** содержащее пароль пользователя. Свойство `forceChangePasswordNextSignIn` должно `false`установить на .

Для федеративной (социальной) идентификации свойство **passwordProfile** не требуется.

```JSON
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

### <a name="password-policy-property"></a>Свойство политики паролей

Политика паролей Azure AD B2C (для локальных учетных записей) основана на твердой политике [безопасности паролей](../active-directory/authentication/concept-sspr-policy.md) Azure Active. Политики регистрации Azure AD B2C или регистрации пароля и сброса паролей требуют такой прочности пароля и не истекают паролями.

В сценариях миграции пользователей, если учетные записи, которые вы хотите перенести, имеют более слабую силу пароля, чем [сильная сила пароля,](../active-directory/authentication/concept-sspr-policy.md) применяемая Azure AD B2C, можно отключить сильное требование пароля. Чтобы изменить политику паролей по умолчанию, задайте для свойства `passwordPolicies` значение `DisableStrongPassword`. Например, можно изменить запрос создания пользователя следующим образом:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

### <a name="extension-properties"></a>Свойства расширения

Каждое приложение, ориентированное на клиента, имеет уникальные требования к собранной информации. Ваш арендатор Azure AD B2C оснащен встроенным набором информации, хранящейся в таких свойствах, как Given Name, Surname, City и Postal Code. С Помощью Azure AD B2C можно расширить набор свойств, хранящихся в каждой учетной записи клиента. Для получения дополнительной информации об определении пользовательских атрибутов см. [пользовательские атрибуты (потоки пользователей)](user-flow-custom-attributes.md) и [пользовательские атрибуты (пользовательские политики).](custom-policy-custom-attributes.md)

Microsoft Graph API поддерживает создание и обновление пользователя с атрибутами расширения. Атрибуты расширений в API Graph именуются по соглашению `extension_ApplicationObjectID_attributename`. Пример:

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

## <a name="code-sample"></a>Пример кода

Этот пример кода — консольное приложение .NET Core, которое использует [Microsoft Graph SDK](https://docs.microsoft.com/graph/sdks/sdks-overview) для взаимодействия с API Microsoft Graph. Его код демонстрирует, как вызывать API для программного управления пользователями в налаживании Azure AD B2C.
Вы можете [скачать образец архива](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (з.zip), [просмотреть репозиторий](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) на GitHub, или клонировать репозиторий:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

После получения образца кода настройте его для среды, а затем создайте проект:

1. Откройте проект в [Visual Studio](https://visualstudio.microsoft.com) или Visual [Studio Code](https://code.visualstudio.com).
1. Откройте среду `src/appsettings.json`.
1. В `appSettings` `your-b2c-tenant` разделе замените имя вашего арендатора, `Application (client) ID` а также `Client secret` значения для регистрации приложения для управления (см. раздел приложения для [управления](#register-a-management-application) в этой статье).
1. Откройте окно консоли в локальном клоне репо, переключитесь в `src` каталог, а затем создайте проект:
    ```console
    cd src
    dotnet build
    ```
1. Запустите приложение, выполнив команду `dotnet`:

    ```console
    dotnet bin/Debug/netcoreapp3.0/b2c-ms-graph.dll
    ```

Приложение отображает список команд, которые можно выполнить. Например, получить всех пользователей, получить одного пользователя, удалить пользователя, обновить пароль пользователя, и объем импорта.

### <a name="code-discussion"></a>Обсуждение кода

В примере кода используется [Microsoft Graph SDK,](https://docs.microsoft.com/graph/sdks/sdks-overview)который предназначен для упрощения создания высококачественных, эффективных и устойчивых приложений, которые получают доступ к Microsoft Graph.

Любой запрос на API Microsoft Graph требует токен доступа для проверки подлинности. В решении используется пакет [Microsoft.Graph.Auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) NuGet, который обеспечивает обертку на основе сценариев проверки библиотеки подлинности Майкрософт (MSAL) для использования в Microsoft Graph SDK.

Метод `RunAsync` в _файле Program.cs:_

1. Читает настройки приложения из файла _appsettings.json_
1. Инициализирует auth провайдера с помощью [OAuth 2.0 клиент учетных данных грантового](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) потока. С потоком грантов учетных данных клиента приложение может получить токен доступа для вызова API Microsoft Graph.
1. Настройка клиента службы Microsoft Graph с поставщиком auth:

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

Затем инициализированный *GraphServiceClient* используется в _UserService.cs_ для выполнения операций по управлению пользователями. Например, получение списка учетных записей пользователей в арендаторе:

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

[Вызовы API с помощью SDK Microsoft Graph](https://docs.microsoft.com/graph/sdks/create-requests) включают информацию о `$select` том, как читать и писать информацию из `$filter` `$orderBy` Microsoft Graph, использовать для управления возвращенными свойствами, предоставлять пользовательские параметры запроса и использовать параметры запроса.

## <a name="next-steps"></a>Дальнейшие действия

Полный индекс операций Microsoft Graph API, поддерживаемых для ресурсов [Microsoft Graph operations available for Azure AD B2C](microsoft-graph-operations.md)Azure AD B2C, см.

<!-- LINK -->

[graph-objectIdentity]: https://docs.microsoft.com/graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
