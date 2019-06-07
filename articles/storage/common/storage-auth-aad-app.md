---
title: Проверка подлинности с помощью Azure Active Directory для доступа к данным больших двоичных объектов и очереди из клиентского приложения
description: Используйте Azure Active Directory для проверки подлинности из приложения клиента, получить маркер OAuth 2.0 и авторизации запросов к хранилищу BLOB-объектов Azure и хранилища очередей.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/05/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e45fe20e93d81c1cfd1f868b40f76743558758bb
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754932"
---
# <a name="authenticate-with-azure-active-directory-from-an-application-for-access-to-blobs-and-queues"></a>Пройти проверку подлинности с помощью Azure Active Directory с приложения для доступа к большим двоичным объектам и очередям

Ключевое преимущество использования Azure Active Directory (Azure AD) с помощью хранилища BLOB-объектов Azure или хранилище очередей является то, что учетные данные больше не должны храниться в коде. Вместо этого вы можете запросить маркер доступа OAuth 2.0 (прежнее название — Azure AD) для платформы Microsoft identity. Azure AD проверяет подлинность субъекта безопасности (пользователя, группы или субъекта-службы) под управлением приложения. Если проверка подлинности завершается успешно, Azure AD возвращает маркер доступа к приложению, и приложение затем может использовать маркер доступа для авторизации запросов в хранилище BLOB-объектов Azure или хранилище очередей.

В этой статье показано, как настроить собственное приложение или веб-приложение для проверки подлинности с помощью Azure AD. В примерах кода используется .NET, но в других языках применяется аналогичный подход.

Общие сведения о процессе предоставления кода OAuth 2.0 представлены в разделе [Авторизация доступа к веб-приложениям Azure Active Directory с помощью потока предоставления кода OAuth 2.0](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="assign-an-rbac-role-to-an-azure-ad-security-principal"></a>Назначение роли RBAC для субъекта безопасности Azure AD

Чтобы обеспечить проверку подлинности субъекта безопасности из приложения службы хранилища Azure, нужно сначала настроить параметры управления доступом на основе ролей (RBAC) для этого субъекта безопасности. Хранилище Azure определяет встроенные роли RBAC, которые охватывают разрешений для контейнеров и очередей. При назначении роли RBAC субъекту безопасности ему предоставляется доступ к соответствующему ресурсу. Дополнительные сведения см. в разделе [управление права доступа к данным BLOB-объектов Azure и очереди с помощью RBAC](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Регистрация приложения в клиенте Azure AD

Первым шагом в с помощью Azure AD для авторизации доступа к ресурсам хранилища, регистрирующий клиентское приложение с клиентом Azure AD из [портала Azure](https://portal.azure.com). При регистрации клиентского приложения, для указания сведений о приложении в Azure AD. После этого служба Azure AD предоставляет идентификатор клиента (также называемый *идентификатором приложения*), позволяющий связать с ней приложение во время выполнения. Дополнительные сведения об идентификаторе клиента см. в статье [Объекты приложения и субъекта-службы в Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md).

Чтобы зарегистрировать приложение хранилища Azure, следуйте инструкциям, указанным в [краткое руководство: Регистрация приложения в платформе Microsoft identity](../../active-directory/develop/quickstart-configure-app-access-web-apis.md). Ниже приведены общие параметры для регистрации веб-приложения:

![Снимок экрана, показывающий, как зарегистрировать приложение хранилища в Azure AD](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> При регистрации приложения как собственного вы можете указать любой допустимый универсальный код ресурса (URI) в качестве **URI перенаправления**. Для собственных приложений это значение не быть действительный URL-адрес. Для веб-приложений URI перенаправления должен быть допустимый URI, так как оно задает URL-адрес, к которому предоставляются маркеры.

После регистрации приложения в разделе **Параметры** отобразится идентификатор приложения (идентификатор клиента).

![Снимок экрана, показывающий идентификатор клиента](./media/storage-auth-aad-app/app-registration-client-id.png)

Дополнительные сведения о регистрации приложения в Azure AD см. в разделе [Интеграция приложений с Azure Active Directory](../../active-directory/develop/quickstart-v2-register-an-app.md).

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Предоставление зарегистрированному приложению разрешений для службы хранилища Azure

Затем предоставьте приложению разрешения для вызова API службы хранилища Azure. Этот шаг позволяет вашему приложению для авторизации запросов в службу хранилища Azure с помощью Azure AD.

1. На **Обзор** страницы для зарегистрированного приложения, выберите **разрешения на просмотр API**.
1. В **разрешения API** выберите **добавить разрешение** и выберите **API, организации**.
1. В разделе **API, организации** поиска «Хранилище Azure», а также выберите **хранилища Azure** из списка результатов для отображения **разрешения запроса API** область.

    ![Снимок экрана: отображение разрешения для хранилища](media/storage-auth-aad-app/registered-app-permissions-1.png)

1. В разделе **какой тип разрешения требует приложения?** , обратите внимание, что тип доступные разрешения **делегированные разрешения**. Этот параметр выбирается по умолчанию.
1. В **Выбор разрешений** раздел **разрешения запроса API** области установите флажок рядом с полем **user_impersonation**, нажмите кнопку **добавить разрешения**.
1. **Разрешения API** области теперь показывает, что приложение Azure AD имеет доступ к Microsoft Graph и службы хранилища Azure. Разрешения предоставляются для Microsoft Graph автоматически во время регистрации приложения с Azure AD.

    ![Снимок экрана, показывающий зарегистрировать разрешения приложения](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="libraries-for-token-acquisition"></a>Библиотеки для получения маркера

После регистрации приложения и разрешения его для доступа к данным в хранилище BLOB-объектов Azure или хранилище очередей, можно добавить код в приложение для проверки подлинности участника безопасности и получить маркер OAuth 2.0. Для проверки подлинности и получить маркер, можно использовать одну из [библиотеки проверки подлинности платформы Microsoft identity](../../active-directory/develop/reference-v2-libraries.md) или другой библиотеке открытым исходным кодом, который поддерживает OpenID Connect 1.0. Приложение затем может использовать маркер доступа для авторизации запроса к хранилища BLOB-объектов Azure или хранилище очередей.

Список сценариев, для которых поддерживается приобретения токенов, см. в разделе [сценариев](https://aka.ms/msal-net-scenarios) раздел [библиотеки аутентификации Майкрософт (MSAL) для .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) репозитория GitHub.

## <a name="net-code-example-create-a-block-blob"></a>Пример кода .NET. Создание блочного BLOB-объекта

В примере кода показано, как получить маркер доступа из Azure AD. Маркер доступа используется для аутентификации указанного пользователя и последующей авторизации запроса на создание блочного BLOB-объекта. Чтобы этот пример работал, сначала выполните инструкции в предыдущих разделах.

> [!NOTE]
> Как владельцу учетной записи хранения Azure вам автоматически не назначаются разрешения на доступ к данным. Для службы хранилища Azure вы должны назначить себе роль RBAC явным образом. Вы можете назначить ее на уровне подписки, группы ресурсов, учетной записи хранения, контейнера или очереди.
>
> Например, чтобы запустить пример кода с учетной записью хранения, владельцем которой вы являетесь, и вашим удостоверением пользователя, необходимо назначить себе роль участника данных для большого двоичного объекта. В противном случае вызов для создания большого двоичного объекта завершится ошибкой с кодом состояния HTTP 403 (запрещено). Дополнительные сведения см. в разделе [управление права доступа к данным хранилища с помощью RBAC](storage-auth-aad-rbac.md).

### <a name="well-known-values-for-authentication-with-azure-ad"></a>Известные значения для аутентификации с помощью Azure AD

Чтобы выполнить аутентификацию субъекта безопасности с помощью Azure AD, необходимо добавить в код некоторые известные значения.

#### <a name="azure-ad-authority"></a>Центр Azure AD

Ниже приведен базовый центр Azure AD для общедоступного облака Microsoft, где *tenant-id* — это идентификатор клиента Active Directory (или идентификатор каталога).

`https://login.microsoftonline.com/<tenant-id>/`

Идентификатор клиента определяет клиент Azure AD для аутентификации. Чтобы получить идентификатор клиента, выполните шаги, описанные в разделе **получение идентификатора клиента для Azure Active Directory**.

#### <a name="storage-resource-id"></a>Идентификатор ресурса хранилища

Используйте идентификатор ресурса хранилища Azure, чтобы получить маркер для авторизации запросов к службе хранилища Azure:

`https://storage.azure.com/`

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Получение идентификатора клиента для Azure Active Directory

Чтобы получить идентификатор клиента, сделайте следующее:

1. На портале Azure выберите Active Directory.
2. Щелкните **Свойства**.
3. Скопируйте значение GUID, указанное для параметра **Идентификатор каталога**. Это значение также называется идентификатором клиента.

![Снимок экрана, показывающий, как скопировать идентификатор клиента](./media/storage-auth-aad-app/aad-tenant-id.png)

## <a name="set-up-a-basic-web-app-to-authenticate-to-azure-ad"></a>Настройка базового веб-приложения для проверки подлинности в Azure AD

Когда приложение обращается к службе хранилища Azure, так далее имени пользователя. Чтобы повторить этот пример кода, необходимо приложение, в котором пользователь может входить с помощью удостоверения Azure AD. Это средство можно загрузить [примере](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) для тестирования базового веб-приложения, которая выполняет проверку подлинности с учетной записью Azure AD.

### <a name="completed-sample"></a>Готовый пример

Полная рабочая версия образце кода, приведенном в этой статье можно загрузить из [GitHub](http://aka.ms/aadstorage). Проверка и выполнение полного примера может оказаться полезным для понимания того, в примерах кода.

### <a name="add-references-and-using-statements"></a>Добавление ссылок и инструкций using  

Из Visual Studio установите клиентскую библиотеку хранилища Azure. В меню **Сервис** выберите **Диспетчер пакетов NuGet**, а затем — **Консоль диспетчера пакетов**. Введите следующие команды в окне консоли, чтобы установить необходимые пакеты в клиентской библиотеке хранилища Azure для .NET:

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

Добавьте следующие операторы using в файл HomeController.cs:

```csharp
using System;
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

### <a name="create-a-block-blob"></a>Создание блочного BLOB-объекта

Добавьте следующий фрагмент кода для создания большого двоичного объекта:

```csharp
private static async Task<string> CreateBlob(string accessToken)
{
    // Create a blob on behalf of the user
    TokenCredential tokenCredential = new TokenCredential(accessToken);
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

    // Replace the URL below with your storage account URL
    CloudBlockBlob blob =
        new CloudBlockBlob(
            new Uri("https://<storage-account>.blob.core.windows.net/sample-container/Blob1.txt"),
            storageCredentials);
    await blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
    return "Blob successfully created";
}
```

> [!NOTE]
> Для авторизации операций BLOB-объектов и очередей с помощью токена OAuth 2.0, необходимо использовать протокол HTTPS.

В приведенном выше примере клиентская библиотека .NET обрабатывает авторизацию запроса для создания блочного BLOB-объекта. Клиент библиотеки службы хранилища Azure для других языков также обрабатывать авторизации запроса для вас. но если вы вызываете операцию Службы хранилища Azure через REST API с помощью маркера OAuth, то авторизовать запрос нужно также с помощью маркера OAuth.

Чтобы вызвать операции служб очередей и BLOB-объектов с помощью маркеров доступа OAuth, передайте маркер доступа в заголовок **Authorization** с помощью схемы **Bearer** и укажите версию службы 2017-11-09 (или новее), как показано в примере ниже.

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

### <a name="get-an-oauth-token-from-azure-ad"></a>Получение токена OAuth из Azure AD

Добавьте метод, который запрашивает токен из Azure AD. Запрашивается маркер будет от имени пользователя, и мы будем использовать метод GetTokenOnBehalfOfUser.

Для запроса токена, потребуются следующие значения, полученные при регистрации приложения

- Идентификатор клиента (или каталог)
- Идентификатор клиента (или приложения)
- URI перенаправления клиента.

Помните, что если необходимо просто войти, и вы запрашиваете маркер для `storage.azure.com` ресурсов, вам потребуется предоставить пользователю с пользовательским Интерфейсом, где пользователь может предоставить подобные действия от их имени. Для упрощения требуется перехватывать `MsalUiRequiredException` и добавить функции для запроса согласия пользователя, как показано в следующем примере:

```csharp
public async Task<IActionResult> Blob()
{
    var scopes = new string[] { "https://storage.azure.com/user_impersonation" };
    try
    {
        var accessToken =
            await _tokenAcquisition.GetAccessTokenOnBehalfOfUser(HttpContext, scopes);
        ViewData["Message"] = await CreateBlob(accessToken);
        return View();
    }
    catch (MsalUiRequiredException ex)
    {
        AuthenticationProperties properties = BuildAuthenticationPropertiesForIncrementalConsent(scopes, ex);
        return Challenge(properties);
    }
}
```

Согласие — это процесс предоставления пользователем разрешения приложению получать доступ к защищенным ресурсам от имени пользователя. Платформе Microsoft identity 2.0 поддерживает добавочное согласие, это означает, что участник безопасности можно запросить минимальный набор разрешений, изначально и при необходимости добавьте разрешения со временем. Когда код запрашивает маркер доступа, укажите область разрешений, используемые вашим приложением в любой момент времени, в `scope` параметра. Следующий метод создает свойства проверки подлинности для запроса добавочное согласие:

```csharp
private AuthenticationProperties BuildAuthenticationPropertiesForIncrementalConsent(string[] scopes, MsalUiRequiredException ex)
{
    AuthenticationProperties properties = new AuthenticationProperties();

    // Set the scopes, including the scopes that ADAL.NET / MSAL.NET need for the Token cache.
    string[] additionalBuildInScopes = new string[] { "openid", "offline_access", "profile" };
    properties.SetParameter<ICollection<string>>(OpenIdConnectParameterNames.Scope, scopes.Union(additionalBuildInScopes).ToList());

    // Attempt to set the login_hint so that the logged-in user is not presented with an account selection dialog.
    string loginHint = HttpContext.User.GetLoginHint();
    if (!string.IsNullOrWhiteSpace(loginHint))
    {
        properties.SetParameter<string>(OpenIdConnectParameterNames.LoginHint, loginHint);

        string domainHint = HttpContext.User.GetDomainHint();
        properties.SetParameter<string>(OpenIdConnectParameterNames.DomainHint, domainHint);
    }

    // Specify any additional claims that are required (for instance, MFA).
    if (!string.IsNullOrEmpty(ex.Claims))
    {
        properties.Items.Add("claims", ex.Claims);
    }

    return properties;
}
```

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о платформе Microsoft identity, см. в разделе [платформы удостоверений Microsoft](https://docs.microsoft.com/azure/active-directory/develop/).
- Дополнительные сведения о ролях RBAC для службы хранилища Azure, см. в разделе [управление права доступа к данным хранилища с помощью RBAC](storage-auth-aad-rbac.md).
- Дополнительные сведения об использовании управляемых удостоверений для ресурсов Azure со службой хранилища Azure, см. в разделе [проверки подлинности доступа к BLOB-объектов и очередей с Azure Active Directory и управляемых удостоверений для ресурсов Azure](storage-auth-aad-msi.md).
