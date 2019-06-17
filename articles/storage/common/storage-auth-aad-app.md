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
ms.openlocfilehash: 30ebfec88182684f8e852808e978a51854389898
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073417"
---
# <a name="authenticate-with-azure-active-directory-from-an-application-for-access-to-blobs-and-queues"></a>Пройти проверку подлинности с помощью Azure Active Directory с приложения для доступа к большим двоичным объектам и очередям

Ключевое преимущество использования Azure Active Directory (Azure AD) с помощью хранилища BLOB-объектов Azure или хранилище очередей является то, что учетные данные больше не должны храниться в коде. Вместо этого вы можете запросить маркер доступа OAuth 2.0 (прежнее название — Azure AD) для платформы Microsoft identity. Azure AD проверяет подлинность субъекта безопасности (пользователя, группы или субъекта-службы) под управлением приложения. Если проверка подлинности завершается успешно, Azure AD возвращает маркер доступа к приложению, и приложение затем может использовать маркер доступа для авторизации запросов в хранилище BLOB-объектов Azure или хранилище очередей.

В этой статье показано, как настроить собственное приложение или веб-приложение для проверки подлинности с платформой Microsoft identity 2.0. В примерах кода используется .NET, но в других языках применяется аналогичный подход. Дополнительные сведения о платформе Microsoft identity 2.0, см. в разделе [обзор платформы (версии 2.0) Microsoft identity](../../active-directory/develop/v2-overview.md).

Общие сведения о процессе предоставления кода OAuth 2.0 представлены в разделе [Авторизация доступа к веб-приложениям Azure Active Directory с помощью потока предоставления кода OAuth 2.0](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Назначение роли для субъекта безопасности Azure AD

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

1. В разделе **какой тип разрешения требует приложения?** , обратите внимание на тип доступные разрешения **делегированные разрешения**. Этот параметр выбирается по умолчанию.
1. В **Выбор разрешений** раздел **разрешения запроса API** области установите флажок рядом с полем **user_impersonation**, нажмите кнопку **добавить разрешения**.
1. **Разрешения API** области теперь показывает, что приложение Azure AD имеет доступ к Microsoft Graph и службы хранилища Azure. Разрешения предоставляются для Microsoft Graph автоматически во время регистрации приложения с Azure AD.

    ![Снимок экрана, показывающий зарегистрировать разрешения приложения](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>Создание секрета клиента

Приложению требуется секрет клиента для подтверждения его подлинности, при запросе маркера. Чтобы добавить секрет клиента, выполните следующие действия.

1. Перейдите к регистрации приложения на портале Azure.
1. Выберите **сертификаты и секреты** параметр.
1. В разделе **секреты клиента**, нажмите кнопку **новый секрет клиента** создать новый секрет.
1. Введите описание для секрета и выберите нужный интервал срока действия.
1. Сразу же скопируйте значение новый секрет в безопасное место. Полное значение отображается только один раз для вас.

    ![Секрет клиента отображение экрана](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>Клиентские библиотеки для получения маркера

После регистрации приложения и разрешения его для доступа к данным в хранилище BLOB-объектов Azure или хранилище очередей, можно добавить код в приложение для проверки подлинности участника безопасности и получить маркер OAuth 2.0. Для проверки подлинности и получить маркер, можно использовать одну из [библиотеки проверки подлинности платформы Microsoft identity](../../active-directory/develop/reference-v2-libraries.md) или другой библиотеке открытым исходным кодом, который поддерживает OpenID Connect 1.0. Приложение затем может использовать маркер доступа для авторизации запроса к хранилища BLOB-объектов Azure или хранилище очередей.

Список сценариев, для которых поддерживается приобретения токенов, см. в разделе [сценариев](https://aka.ms/msal-net-scenarios) раздел [библиотеки аутентификации Майкрософт (MSAL) для .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) репозитория GitHub.

## <a name="net-code-example-create-a-block-blob"></a>Пример кода .NET. Создание блочного BLOB-объекта

В примере кода показано, как получить маркер доступа из Azure AD. Маркер доступа используется для аутентификации указанного пользователя и последующей авторизации запроса на создание блочного BLOB-объекта. Чтобы этот пример работал, сначала выполните инструкции в предыдущих разделах.

Для запроса токена, потребуются следующие значения, полученные при регистрации приложения:

- Имя домена Azure AD. Получить это значение из **Обзор** страницы в Azure Active Directory.
- Идентификатор клиента (или каталог). Получить это значение из **Обзор** странице регистрации приложения.
- Идентификатор клиента (или приложения). Получить это значение из **Обзор** странице регистрации приложения.
- URI перенаправления клиента. Получить это значение из **проверки подлинности** параметров регистрации приложения.
- Значение секрета клиента. Это значение получалось из расположения, к которой вы ранее скопировали его.

### <a name="well-known-values-for-authentication-with-azure-ad"></a>Известные значения для аутентификации с помощью Azure AD

Чтобы выполнить аутентификацию субъекта безопасности с помощью Azure AD, необходимо добавить в код некоторые известные значения.

#### <a name="azure-ad-authority"></a>Центр Azure AD

Ниже приведен базовый центр Azure AD для общедоступного облака Microsoft, где *tenant-id* — это идентификатор клиента Active Directory (или идентификатор каталога).

`https://login.microsoftonline.com/<tenant-id>/`

Идентификатор клиента определяет клиент Azure AD для аутентификации. Он также называется идентификатор каталога. Чтобы получить идентификатор клиента, перейдите к **Обзор** странице регистрации приложения на портале Azure и скопируйте значение из него.

#### <a name="storage-resource-id"></a>Идентификатор ресурса хранилища

Используйте идентификатор ресурса хранилища Azure, чтобы получить маркер для авторизации запросов к службе хранилища Azure:

`https://storage.azure.com/`

### <a name="create-a-storage-account-and-container"></a>Создание учетной записи хранения и контейнера

Чтобы запустить пример кода, создайте учетную запись хранения в той же подписке, что в Azure Active Directory. Затем создайте контейнер в этой учетной записи хранения. В примере кода создается большой двоичный объект в этом контейнере.

Затем явно назначить **участник для данных больших двоичных объектов хранилища** роли с учетной записью пользователя, с которой будет выполняться в примере кода. Инструкции о том, как присвоить этой роли на портале Azure, см. в разделе [предоставить доступ к BLOB-объектов и очереди данных Azure с помощью RBAC на портале Azure](storage-auth-aad-rbac-portal.md).

> [!NOTE]
> При создании учетной записи хранения Azure, вы не назначаются автоматически разрешения на доступ к данным с помощью Azure AD. Для службы хранилища Azure вы должны назначить себе роль RBAC явным образом. Вы можете назначить ее на уровне подписки, группы ресурсов, учетной записи хранения, контейнера или очереди.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Создание веб-приложения, которая разрешает доступ к хранилищу BLOB-объектов с помощью Azure AD

Когда приложение обращается к службе хранилища Azure, так далее имени пользователя, это означает, что BLOB-объектов или очередей ресурсам осуществляется с помощью разрешения пользователя, вошедшего в систему. Чтобы повторить этот пример кода, вам понадобится веб-приложение, пользователю будет предложено войти с помощью удостоверения Azure AD. Можно создавать собственные или использовать пример приложения, предоставляемые корпорацией Майкрософт.

Готовый пример веб-приложение получает маркер и использует его для создания большого двоичного объекта в службе хранилища Azure можно найти в [GitHub](http://aka.ms/aadstorage). Проверка и выполнение готовый пример может оказаться полезным для понимания того, в примерах кода. Инструкции о том, как запустить готовый пример, см. в разделе [представление и выполнения готовый пример](#view-and-run-the-completed-sample).

#### <a name="add-references-and-using-statements"></a>Добавление ссылок и инструкций using  

Из Visual Studio установите клиентскую библиотеку хранилища Azure. В меню **Сервис** выберите **Диспетчер пакетов NuGet**, а затем — **Консоль диспетчера пакетов**. Введите следующие команды в окне консоли, чтобы установить необходимые пакеты в клиентской библиотеке хранилища Azure для .NET:

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

Добавьте следующие операторы using в файл HomeController.cs:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

#### <a name="create-a-block-blob"></a>Создание блочного BLOB-объекта

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
            new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
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

#### <a name="get-an-oauth-token-from-azure-ad"></a>Получение токена OAuth из Azure AD

Добавьте метод, который запрашивает токен из Azure AD. Запрашивается маркер будет от имени пользователя, и мы будем использовать метод GetTokenOnBehalfOfUser.

Помните, что если недавно был выполнен вход, и вы запрашиваете маркер для `storage.azure.com` ресурсов, вам потребуется предоставить пользователю с пользовательским Интерфейсом, где пользователь может предоставить подобные действия от их имени. Для упрощения требуется перехватывать `MsalUiRequiredException` и добавить функции для запроса согласия пользователя, как показано в следующем примере:

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

Согласие — это процесс предоставления пользователем разрешения приложению получать доступ к защищенным ресурсам от имени пользователя. Платформе Microsoft identity 2.0 поддерживает добавочное согласие, это означает, что участник безопасности можно запросить минимальный набор разрешений, изначально и при необходимости добавьте разрешения со временем. Когда код запрашивает маркер доступа, укажите область разрешений, используемые вашим приложением в любой момент времени, в `scope` параметра. Дополнительные сведения о добавочное согласие, см. в разделе **добавочное и динамическое согласие** в [Почему обновления к платформе Microsoft identity (версии 2.0)?](../../active-directory/develop/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent).

Следующий метод создает свойства проверки подлинности для запроса добавочное согласие:

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

## <a name="view-and-run-the-completed-sample"></a>Просмотр и запуск готовый пример

Чтобы запустить пример приложения, сначала Клонируйте или скачайте его из [GitHub](http://aka.ms/aadstorage). Затем обновите приложение, как описано в следующих разделах.

### <a name="provide-values-in-the-settings-file"></a>Укажите значения в файле параметров

Затем обновите *appsettings.json* файле собственными значениями, как показано ниже:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "<azure-ad-domain-name>.onmicrosoft.com",
    "TenantId": "<tenant-id>",
    "ClientId": "<client-id>",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    // To call an API
    "ClientSecret": "<client-secret>"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="update-the-storage-account-and-container-name"></a>Обновите имя учетной записи и контейнера хранилища

В *HomeController.cs* обновите URI, который ссылается на блочный BLOB-объект для использования имени учетной записи хранения и контейнер:

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>Включение неявного потока предоставления

Чтобы запустить образец, может потребоваться настроить неявный поток предоставления регистрации приложения. Выполните следующие действия.

1. Перейдите к регистрации приложения на портале Azure.
1. В разделе "Управление", выберите **проверки подлинности** параметр.
1. В разделе **Дополнительные параметры**в **неявное предоставление** выберите флажки, чтобы включить маркеры доступа и маркеров Идентификации, как показано на следующем рисунке:

    ![Снимок экрана, показывающий, как включить параметры для неявного потока предоставления](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>Обновления порт, используемый localhost

При запуске примера, вы обнаружите, что вам нужно обновить код URI, заданный в регистрации приложения для использования перенаправления *localhost* порт, назначенный во время выполнения. Чтобы обновить URI, используемый порт, назначенный для перенаправления, выполните следующие действия.

1. Перейдите к регистрации приложения на портале Azure.
1. В разделе "Управление", выберите **проверки подлинности** параметр.
1. В разделе **URI перенаправления**, изменить порт, который соответствует модификатору, используемому для примера приложения, как показано на следующем рисунке:

    ![Снимок экрана, показывающий URI перенаправления для регистрации приложения](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о платформе Microsoft identity, см. в разделе [платформы удостоверений Microsoft](https://docs.microsoft.com/azure/active-directory/develop/).
- Дополнительные сведения о ролях RBAC для службы хранилища Azure, см. в разделе [управление права доступа к данным хранилища с помощью RBAC](storage-auth-aad-rbac.md).
- Дополнительные сведения об использовании управляемых удостоверений для ресурсов Azure со службой хранилища Azure, см. в разделе [проверки подлинности доступа к BLOB-объектов и очередей с Azure Active Directory и управляемых удостоверений для ресурсов Azure](storage-auth-aad-msi.md).
