---
title: Авторизация доступа к BLOB-объектам и очередям с Azure Active Directory из клиентского приложения в службе хранилища Azure
description: Используйте Azure Active Directory для проверки подлинности в клиентском приложении, получения маркера OAuth 2,0 и авторизации запросов к хранилищу BLOB-объектов Azure и хранилищу очередей.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: ca6b055b5d3702cea4ca1986ad1c81b59f76cee3
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299636"
---
# <a name="authorize-access-to-blobs-and-queues-with-azure-active-directory-from-a-client-application"></a>Авторизация доступа к BLOB-объектам и очередям с Azure Active Directory из клиентского приложения

Ключевым преимуществом использования Azure Active Directory (Azure AD) с хранилищем BLOB-объектов Azure или хранилищем очередей является то, что ваши учетные данные больше не нужно хранить в коде. Вместо этого можно запросить маркер доступа OAuth 2,0 на платформе Microsoft Identity (прежнее название — Azure AD). Azure AD выполняет проверку подлинности субъекта безопасности (пользователя, группы или субъекта-службы), на котором выполняется приложение. Если проверка подлинности прошла удачно, Azure AD возвращает маркер доступа приложению, а приложение может использовать маркер доступа для авторизации запросов к хранилищу BLOB-объектов Azure или хранилищу очередей.

В этой статье показано, как настроить собственное приложение или веб-приложение для проверки подлинности с помощью Microsoft Identity Platform 2,0. В примерах кода используется .NET, но в других языках применяется аналогичный подход. Дополнительные сведения о платформе Microsoft Identity Platform 2,0 см. в статье [Обзор платформы Microsoft Identity Platform (v 2.0)](../../active-directory/develop/v2-overview.md).

Общие сведения о процессе предоставления кода OAuth 2.0 представлены в разделе [Авторизация доступа к веб-приложениям Azure Active Directory с помощью потока предоставления кода OAuth 2.0](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Назначение роли субъекту безопасности Azure AD

Чтобы обеспечить проверку подлинности субъекта безопасности из приложения службы хранилища Azure, нужно сначала настроить параметры управления доступом на основе ролей (RBAC) для этого субъекта безопасности. Служба хранилища Azure определяет встроенные роли RBAC, охватывающие разрешения для контейнеров и очередей. При назначении роли RBAC субъекту безопасности ему предоставляется доступ к соответствующему ресурсу. Дополнительные сведения см. в статье [Управление правами доступа к данным большого двоичного объекта Azure и очереди с помощью RBAC](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Регистрация приложения в клиенте Azure AD

Первым шагом в использовании Azure AD для авторизации доступа к ресурсам хранилища является регистрация клиентского приложения в клиенте Azure AD из [портал Azure](https://portal.azure.com). При регистрации клиентского приложения вы предоставляете сведения о приложении в Azure AD. После этого служба Azure AD предоставляет идентификатор клиента (также называемый *идентификатором приложения*), позволяющий связать с ней приложение во время выполнения. Дополнительные сведения об идентификаторе клиента см. в статье [Объекты приложения и субъекта-службы в Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md).

Чтобы зарегистрировать приложение службы хранилища Azure, выполните действия, описанные в разделе [Краткое руководство по регистрации приложения на платформе Microsoft Identity](../../active-directory/develop/quickstart-configure-app-access-web-apis.md). На следующем рисунке показаны общие параметры регистрации веб-приложения.

![Снимок экрана, показывающий, как зарегистрировать приложение хранилища в Azure AD](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> При регистрации приложения как собственного вы можете указать любой допустимый универсальный код ресурса (URI) в качестве **URI перенаправления**. Для собственных приложений это значение не обязательно должно быть действительным URL-адресом. Для веб-приложений URI перенаправления должен быть допустимым URI, так как он указывает URL-адрес, на который предоставляются токены.

После регистрации приложения в разделе **Параметры** отобразится идентификатор приложения (идентификатор клиента).

![Снимок экрана, показывающий идентификатор клиента](./media/storage-auth-aad-app/app-registration-client-id.png)

Дополнительные сведения о регистрации приложения в Azure AD см. в разделе [Интеграция приложений с Azure Active Directory](../../active-directory/develop/quickstart-v2-register-an-app.md).

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Предоставление зарегистрированному приложению разрешений для службы хранилища Azure

Затем предоставьте своим приложениям разрешения на вызов API-интерфейсов службы хранилища Azure. Этот шаг позволяет приложению авторизовать запросы к службе хранилища Azure с помощью Azure AD.

1. На странице **Обзор** зарегистрированного приложения выберите **просмотреть разрешения API**.
1. В разделе **разрешения API** выберите **Добавить разрешение** и выберите **API-интерфейсы Майкрософт**.
1. Выберите **хранилище Azure** в списке результатов, чтобы отобразить панель **разрешений API запроса** .
1. В **каком типе разрешений требуется ваше приложение?** Обратите внимание на то, что доступный тип разрешений — **делегированные разрешения**. Этот параметр выбран по умолчанию.
1. В разделе **Выбор разрешений** на панели **разрешения API запроса** установите флажок рядом с **user_impersonation**, а затем нажмите кнопку **Добавить разрешения**.

    ![Снимок экрана, показывающий разрешения для хранилища](media/storage-auth-aad-app/registered-app-permissions-1.png)

Панель **разрешений API** теперь показывает, что зарегистрированное приложение Azure AD имеет доступ как к Microsoft Graph, так и к хранилищу Azure. Разрешения предоставляются Microsoft Graph автоматически при первой регистрации приложения в Azure AD.

![Снимок экрана с разрешениями на регистрацию приложения](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>Создание секрета клиента

Приложению требуется секрет клиента для подтверждения его личности при запросе маркера. Чтобы добавить секрет клиента, выполните следующие действия.

1. Перейдите к регистрации приложения в портал Azure.
1. Выберите параметр **сертификаты & секреты** .
1. В разделе **секреты клиента**щелкните **новый секрет клиента** , чтобы создать новый секрет.
1. Введите описание секрета и выберите требуемый интервал срока действия.
1. Немедленно скопируйте значение нового секрета в безопасное расположение. Полное значение отображается только один раз.

    ![Снимок экрана, показывающий секрет клиента](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>Клиентские библиотеки для получения маркера

После регистрации приложения и предоставления ему разрешений на доступ к данным в хранилище BLOB-объектов Azure или хранилище очередей можно добавить код в приложение для проверки подлинности субъекта безопасности и получения маркера OAuth 2,0. Для проверки подлинности и получения маркера можно использовать одну из [библиотек проверки подлинности платформы идентификации Microsoft Identity](../../active-directory/develop/reference-v2-libraries.md) или другую библиотеку с открытым исходным кодом, которая поддерживает openid connect Connect 1,0. Приложение может использовать маркер доступа для авторизации запроса к хранилищу BLOB-объектов Azure или хранилищу очередей.

Список сценариев, для которых поддерживается получение маркеров, см. в разделе « [потоки проверки подлинности](/en-us/azure/active-directory/develop/msal-authentication-flows) » [содержимого библиотеки проверки подлинности Майкрософт](/azure/active-directory/develop/msal-overview).

## <a name="well-known-values-for-authentication-with-azure-ad"></a>Известные значения для аутентификации с помощью Azure AD

Чтобы выполнить аутентификацию субъекта безопасности с помощью Azure AD, необходимо добавить в код некоторые известные значения.

### <a name="azure-ad-authority"></a>Центр Azure AD

Ниже приведен базовый центр Azure AD для общедоступного облака Microsoft, где *tenant-id* — это идентификатор клиента Active Directory (или идентификатор каталога).

`https://login.microsoftonline.com/<tenant-id>/`

Идентификатор клиента определяет клиент Azure AD для аутентификации. Он также называется ИДЕНТИФИКАТОРом каталога. Чтобы получить идентификатор клиента, перейдите на страницу **обзора** для регистрации приложения в портал Azure и скопируйте значение из него.

### <a name="azure-storage-resource-id"></a>Идентификатор ресурса хранилища Azure

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Пример кода .NET: создание блочного BLOB-объекта

В примере кода показано, как получить маркер доступа из Azure AD. Маркер доступа используется для аутентификации указанного пользователя и последующей авторизации запроса на создание блочного BLOB-объекта. Чтобы этот пример работал, сначала выполните инструкции в предыдущих разделах.

Чтобы запросить маркер, вам потребуются следующие значения из регистрации приложения:

- Имя домена Azure AD. Получите это значение на странице **обзора** Azure Active Directory.
- Идентификатор клиента (или каталога). Получите это значение на странице **обзора** регистрации приложения.
- Идентификатор клиента (или приложения). Получите это значение на странице **обзора** регистрации приложения.
- URI перенаправления клиента. Извлеките это значение из параметров **проверки подлинности** для регистрации приложения.
- Значение секрета клиента. Извлеките это значение из расположения, в которое было скопировано ранее.

### <a name="create-a-storage-account-and-container"></a>Создание учетной записи хранения и контейнера

Чтобы запустить пример кода, создайте учетную запись хранения в той же подписке, в которой находится Azure Active Directory. Затем создайте контейнер в этой учетной записи хранения. Пример кода создаст блочный BLOB-объект в этом контейнере.

Затем явно назначьте роль **участника данных BLOB-объекта хранилища** учетной записи пользователя, под которой будет выполняться пример кода. Инструкции по назначению этой роли в портал Azure см. [в разделе Предоставление доступа к данным большого двоичного объекта Azure и очереди с помощью RBAC в портал Azure](storage-auth-aad-rbac-portal.md).

> [!NOTE]
> При создании учетной записи хранения Azure вы не назначаете разрешения на доступ к данным через Azure AD автоматически. Для службы хранилища Azure вы должны назначить себе роль RBAC явным образом. Вы можете назначить ее на уровне подписки, группы ресурсов, учетной записи хранения, контейнера или очереди.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Создание веб-приложения, которое разрешает доступ к хранилищу больших двоичных объектов с помощью Azure AD

Когда приложение обращается к службе хранилища Azure, оно делает это от имени пользователя, то есть доступ к ресурсам большого двоичного объекта или очереди осуществляется с помощью разрешений пользователя, выполнившего вход. Чтобы испытать этот пример кода, вам потребуется веб-приложение, предлагающее пользователю выполнить вход с помощью удостоверения Azure AD. Вы можете создать собственный или использовать пример приложения, предоставляемый корпорацией Майкрософт.

Готовый пример веб-приложения, который получает маркер и использует его для создания большого двоичного объекта в службе хранилища Azure, доступен на сайте [GitHub](https://aka.ms/aadstorage). Просмотр и выполнение завершенного примера может оказаться полезным для понимания примеров кода. Инструкции по выполнению завершенного примера см. в разделе [представление с именем и запуск завершенного примера](#view-and-run-the-completed-sample).

#### <a name="add-references-and-using-statements"></a>Добавление ссылок и инструкций using  

В Visual Studio установите клиентскую библиотеку службы хранилища Azure. В меню **Сервис** выберите **Диспетчер пакетов NuGet**, а затем — **Консоль диспетчера пакетов**. Введите следующие команды в окне консоли, чтобы установить необходимые пакеты из клиентской библиотеки службы хранилища Azure для .NET:

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

Затем добавьте следующие операторы using в файл HomeController.cs:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

#### <a name="create-a-block-blob"></a>Создание блочного BLOB-объекта

Добавьте следующий фрагмент кода для создания блочного BLOB-объекта:

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
> Для авторизации операций BLOB-объектов и очередей с маркером OAuth 2,0 необходимо использовать протокол HTTPS.

В приведенном выше примере клиентская библиотека .NET обрабатывает авторизацию запроса для создания блочного BLOB-объекта. Клиентские библиотеки службы хранилища Azure для других языков также выполняют авторизацию запроса. но если вы вызываете операцию Службы хранилища Azure через REST API с помощью маркера OAuth, то авторизовать запрос нужно также с помощью маркера OAuth.

Чтобы вызвать операции служб очередей и BLOB-объектов с помощью маркеров доступа OAuth, передайте маркер доступа в заголовок **Authorization** с помощью схемы **Bearer** и укажите версию службы 2017-11-09 (или новее), как показано в примере ниже.

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-oauth-token-from-azure-ad"></a>Получение токена OAuth из Azure AD

Затем добавьте метод, который запрашивает маркер из Azure AD от имени пользователя. Этот метод определяет область, для которой должны быть предоставлены разрешения. Дополнительные сведения о разрешениях и областях см. [в разделе разрешения и согласие в конечной точке платформы Microsoft Identity](../../active-directory/develop/v2-permissions-and-consent.md).

Используйте идентификатор ресурса для создания области, для которой необходимо получить маркер. В примере область создается с использованием идентификатора ресурса вместе со встроенной областью `user_impersonation`, которая указывает, что маркер запрашивается от имени пользователя.

Помните, что может потребоваться предоставить пользователю интерфейс, позволяющий пользователю получить согласие на запрос маркера от его имени. Когда требуется согласие, в примере перехватывается **мсалуирекуиредексцептион** и вызывается другой метод для упрощения запроса согласия:

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
        AuthenticationProperties properties =
            BuildAuthenticationPropertiesForIncrementalConsent(scopes, ex);
        return Challenge(properties);
    }
}
```

Согласие — это процесс предоставления пользователем разрешения приложению получать доступ к защищенным ресурсам от имени пользователя. Платформа Microsoft Identity Platform 2,0 поддерживает последовательное согласие, а это значит, что участник безопасности может сначала запросить минимальный набор разрешений, а при необходимости добавить разрешения. Когда код запрашивает маркер доступа, укажите область разрешений, необходимых приложению в любой момент времени, в параметре `scope`. Дополнительные сведения о последовательном согласии см. в разделе, посвященном **добавочному и динамическому согласию** по поводу [обновления платформы Microsoft Identity Platform (v 2.0)](../../active-directory/develop/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent).

Следующий метод конструирует свойства проверки подлинности для запроса добавочного согласия:

```csharp
private AuthenticationProperties BuildAuthenticationPropertiesForIncrementalConsent(string[] scopes,
                                                                                    MsalUiRequiredException ex)
{
    AuthenticationProperties properties = new AuthenticationProperties();

    // Set the scopes, including the scopes that ADAL.NET or MSAL.NET need for the Token cache.
    string[] additionalBuildInScopes = new string[] { "openid", "offline_access", "profile" };
    properties.SetParameter<ICollection<string>>(OpenIdConnectParameterNames.Scope,
                                                 scopes.Union(additionalBuildInScopes).ToList());

    // Attempt to set the login_hint so that the logged-in user is not presented
    // with an account selection dialog.
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

## <a name="view-and-run-the-completed-sample"></a>Просмотр и запуск завершенного примера

Чтобы запустить пример приложения, сначала скопируйте или скачайте его из [GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal). Затем обновите приложение, как описано в следующих разделах.

### <a name="provide-values-in-the-settings-file"></a>Указание значений в файле параметров

Затем обновите файл *appSettings. JSON* собственными значениями, как показано ниже.

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

### <a name="update-the-storage-account-and-container-name"></a>Обновление учетной записи хранения и имени контейнера

В файле *HomeController.CS* обновите универсальный код ресурса (URI), который ссылается на блочный BLOB-объект, чтобы использовать имя учетной записи хранения и контейнера:

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>Включить неявный поток предоставления разрешений

Чтобы запустить пример, может потребоваться настроить неявный поток предоставления для регистрации приложения. Выполните следующие действия:

1. Перейдите к регистрации приложения в портал Azure.
1. В разделе Управление выберите параметр **Проверка подлинности** .
1. В разделе **Дополнительные параметры**в разделе **неявное предоставление** установите флажки, чтобы включить маркеры доступа и маркеры идентификации, как показано на следующем рисунке:

    ![Снимок экрана, показывающий, как включить параметры для неявного потока предоставления разрешений](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>Обновление порта, используемого localhost

При запуске образца может оказаться, что необходимо обновить URI перенаправления, указанный в регистрации приложения, чтобы использовать порт *localhost* , назначенный во время выполнения. Чтобы обновить URI перенаправления для использования назначенного порта, выполните следующие действия.

1. Перейдите к регистрации приложения в портал Azure.
1. В разделе Управление выберите параметр **Проверка подлинности** .
1. В разделе **URI перенаправления**измените порт так, чтобы он совпадал с используемым образцом приложения, как показано на следующем рисунке:

    ![Снимок экрана, показывающий URI перенаправления для регистрации приложения](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о платформе Microsoft Identity см. в разделе [платформа Microsoft Identity](https://docs.microsoft.com/azure/active-directory/develop/).
- Дополнительные сведения о ролях RBAC для службы хранилища Azure см. в статье [Управление правами доступа к данным хранилища с помощью RBAC](storage-auth-aad-rbac.md).
- Дополнительные сведения об использовании управляемых удостоверений для ресурсов Azure в службе хранилища Azure см. в статье [Проверка подлинности доступа к BLOB-объектам и очередям с Azure Active Directory и управляемыми удостоверениями для ресурсов Azure](storage-auth-aad-msi.md).
