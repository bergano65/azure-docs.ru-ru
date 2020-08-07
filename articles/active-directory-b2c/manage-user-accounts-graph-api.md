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
ms.date: 08/03/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8132eb72b3e448d7ae830b29ccb7dc51528c1250
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2020
ms.locfileid: "87921407"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>Управление Azure AD B2C учетными записями пользователей с помощью Microsoft Graph

Microsoft Graph позволяет управлять учетными записями пользователей в каталоге Azure AD B2C, предоставляя методы создания, чтения, обновления и удаления в API-интерфейсе Microsoft Graph. Вы можете перенести существующее хранилище пользователя в клиент Azure AD B2C и выполнить другие операции по управлению учетными записями пользователей, вызвав API Microsoft Graph.

В следующих разделах представлены ключевые аспекты Azure AD B2C управления пользователями с помощью API Microsoft Graph. Операции, типы и свойства API Microsoft Graph, представленные здесь, являются подмножеством, которое отображается в справочной документации по API Microsoft Graph.

## <a name="register-a-management-application"></a>Регистрация приложения управления

Для взаимодействия с ресурсами в клиенте Azure AD B2C необходимо зарегистрировать приложение, которое предоставляет разрешения на доступ к этим ресурсам.

Выполните действия, описанные в этой статье, чтобы создать регистрацию приложения, которую может использовать приложение управления.

[Управление Azure AD B2C с помощью Microsoft Graph](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>Операции Microsoft Graph управления пользователями

В [Microsoft Graph API](https://docs.microsoft.com/graph/api/resources/user)доступны следующие операции управления пользователями:

- [Получение списка пользователей](https://docs.microsoft.com/graph/api/user-list)
- [Создание пользователя](https://docs.microsoft.com/graph/api/user-post-users)
- [Получение пользователя](https://docs.microsoft.com/graph/api/user-get)
- [Обновление пользователя](https://docs.microsoft.com/graph/api/user-update)
- [Удаление пользователя](https://docs.microsoft.com/graph/api/user-delete)

## <a name="user-properties"></a>Свойства пользователя

### <a name="display-name-property"></a>Свойство "отображаемое имя"

`displayName`— Это имя, которое отображается в портал Azure управления пользователями для пользователя и в маркере доступа Azure AD B2C возвращается приложению. Это свойство обязательно.

### <a name="identities-property"></a>Identity, свойство

Учетная запись клиента, которая может быть потребителем, партнером или участником, может быть связана с этими типами удостоверений:

- **Локальное** удостоверение. имя пользователя и пароль хранятся локально в каталоге Azure AD B2C. Часто мы называем такие удостоверения "локальными учетными записями".
- **Федеративная** идентификация (также называемая *социальными* или *корпоративными* учетными записями) идентификатор пользователя управляется федеративным поставщиком удостоверений, например Facebook, Microsoft, ADFS или Salesforce.

Пользователь с учетной записью клиента может войти в систему с несколькими удостоверениями. Например, имя пользователя, адрес электронной почты, идентификатор сотрудника, идентификатор правительства и другие. Одна учетная запись может иметь несколько удостоверений (локальных и социальных) с одним и тем же паролем.

В Microsoft Graph API локальные и Федеративные удостоверения хранятся в `identities` атрибуте User, который имеет тип [обжектидентити][graph-objectIdentity]. `identities`Коллекция представляет набор удостоверений, используемых для входа в учетную запись пользователя. Эта коллекция позволяет пользователю входить в учетную запись пользователя с любыми связанными удостоверениями.

| Свойство   | Тип |Описание|
|:---------------|:--------|:----------|
|сигнинтипе|строка| Указывает типы входов пользователей в каталоге. Для локальной учетной записи: `emailAddress` , `emailAddress1` , `emailAddress2` ,, `emailAddress3` `userName` или любой другой тип, который вам нравится. Учетная запись социальных сетей должна иметь значение `federated` .|
|issuer|строка|Указывает издателя удостоверения. Для локальных учетных записей (где **сигнинтипе** — не `federated` ) это свойство является локальным доменным именем по умолчанию для клиента B2C, например `contoso.onmicrosoft.com` . Для социальных удостоверений (где **сигнинтипе** — `federated` ) значением является имя издателя, например`facebook.com`|
|иссуерассигнедид|строка|Указывает уникальный идентификатор, назначенный пользователю издателем. Сочетание **издателя** и **иссуерассигнедид** должно быть уникальным в пределах клиента. Для локальной учетной записи, когда **сигнинтипе** имеет значение `emailAddress` или `userName` , он представляет имя входа для пользователя.<br>Если **сигнинтипе** имеет значение: <ul><li>`emailAddress`(или начинается с `emailAddress` Like `emailAddress1` ) **иссуерассигнедид** должен быть допустимым адресом электронной почты</li><li>`userName`(или любое другое значение) **иссуерассигнедид** должен быть действительной [локальной частью адреса электронной почты](https://tools.ietf.org/html/rfc3696#section-3)</li><li>`federated`, **иссуерассигнедид** представляет уникальный идентификатор федеративной учетной записи.</li></ul>|

Следующее свойство identitys **с удостоверением** локальной учетной записи с именем для входа, адресом электронной почты в качестве входа и с удостоверением социальных сетей. 

 ```json
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

Для федеративных удостоверений в зависимости от поставщика удостоверений **иссуерассигнедид** является уникальным значением для конкретного пользователя на приложение или учетную запись разработки. Настройте политику Azure AD B2C с тем же ИДЕНТИФИКАТОРом приложения, который ранее был назначен поставщиком социальных сетей или другим приложением в той же учетной записи разработки.

### <a name="password-profile-property"></a>Свойство профиля пароля

Для локального удостоверения свойство **passwordProfile** является обязательным и содержит пароль пользователя. `forceChangePasswordNextSignIn`Свойство должно иметь значение `false` .

Для федеративного (социальных) удостоверения свойство **passwordProfile** не является обязательным.

```json
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

### <a name="password-policy-property"></a>Свойство политики паролей

Политика паролей Azure AD B2C (для локальных учетных записей) основана на Azure Active Directory политике [надежности надежных паролей](../active-directory/authentication/concept-sspr-policy.md) . Для политик регистрации или входа в систему и сброса пароля требуется эта надежная стойкость пароля, а Azure AD B2C также не срок действия паролей.

В сценариях миграции пользователей, если учетные записи, для которых требуется выполнить миграцию, имеют более слабую стойкость пароля, чем [усиленный пароль](../active-directory/authentication/concept-sspr-policy.md) , принудительно заданный Azure AD B2C, можно отключить требование надежного пароля. Чтобы изменить политику паролей по умолчанию, задайте для свойства `passwordPolicies` значение `DisableStrongPassword`. Например, можно изменить запрос создания пользователя следующим образом:

```json
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

### <a name="extension-properties"></a>Свойства расширения

Каждое клиентское приложение имеет уникальные требования к собираемым данным. Клиент Azure AD B2C поставляется со встроенным набором данных, хранящихся в свойствах, таких как имя, фамилия, город и почтовый индекс. С помощью Azure AD B2C можно расширить набор свойств, хранящихся в каждой учетной записи клиента. Дополнительные сведения об определении настраиваемых атрибутов см. в разделе [пользовательские атрибуты (потоки пользователей)](user-flow-custom-attributes.md) и [настраиваемые атрибуты (пользовательские политики)](custom-policy-custom-attributes.md).

Microsoft Graph API поддерживает создание и обновление пользователя с помощью атрибутов расширения. Атрибуты расширения в API Graph именуются с помощью соглашения `extension_ApplicationClientID_attributename` , где `ApplicationClientID` — это **идентификатор приложения (клиента)** `b2c-extensions-app` приложения (находится в **Регистрация приложений**  >  **всех приложениях** в портал Azure). Обратите внимание, что **идентификатор приложения (клиента)** , представленный в имени атрибута расширения, не включает дефисы. Например:

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>Пример кода: программное управление учетными записями пользователей

Этот пример кода является консольным приложением .NET Core, которое использует [пакет SDK для Microsoft Graph](https://docs.microsoft.com/graph/sdks/sdks-overview) для взаимодействия с Microsoft Graph API. В его коде показано, как вызывать API для программного управления пользователями в клиенте Azure AD B2C.
Вы можете [скачать пример архива](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*. zip), [просмотреть репозиторий](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) на сайте GitHub или клонировать репозиторий:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

После получения примера кода настройте его для своей среды, а затем выполните сборку проекта:

1. Откройте проект в [Visual Studio](https://visualstudio.microsoft.com) или [Visual Studio Code](https://code.visualstudio.com).
1. Откройте `src/appsettings.json`.
1. В `appSettings` разделе замените `your-b2c-tenant` именем своего клиента и `Application (client) ID` `Client secret` значениями для регистрации приложения управления (см. раздел [Регистрация приложения управления](#register-a-management-application) в этой статье).
1. Откройте окно консоли в локальном клоне репозитория, перейдите в `src` каталог, а затем выполните сборку проекта:
    ```console
    cd src
    dotnet build
    ```
1. Запустите приложение, выполнив команду `dotnet`:

    ```console
    dotnet bin/Debug/netcoreapp3.0/b2c-ms-graph.dll
    ```

В приложении отображается список команд, которые можно выполнить. Например, можно получить всех пользователей, получить одного пользователя, удалить пользователя, обновить пароль пользователя и выполнить групповое импортирование.

### <a name="code-discussion"></a>Обсуждение кода

В примере кода используется [пакет SDK Microsoft Graph](https://docs.microsoft.com/graph/sdks/sdks-overview), предназначенный для упрощения создания высококачественных, эффективных и устойчивых приложений, обращающихся к Microsoft Graph.

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

[Выполнение вызовов API с помощью Microsoft Graph пакетов SDK](https://docs.microsoft.com/graph/sdks/create-requests) включает сведения о том, как считывать и записывать данные из Microsoft Graph, использовать `$select` для управления возвращаемыми свойствами, предоставлять настраиваемые параметры запроса и использовать `$filter` `$orderBy` Параметры запроса и.

## <a name="next-steps"></a>Дальнейшие действия

Полный индекс операций Microsoft Graph API, поддерживаемых для ресурсов Azure AD B2C, см. в разделе [операции Microsoft Graph, доступные для Azure AD B2C](microsoft-graph-operations.md).

<!-- LINK -->

[graph-objectIdentity]: https://docs.microsoft.com/graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
