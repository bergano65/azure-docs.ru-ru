---
title: Приобретение токена из Azure AD для авторизации запросов из клиентского приложения
titleSuffix: Azure Storage
description: Используйте Active Directory Azure для проверки подлинности в клиентском приложении, приобретения токена OAuth 2.0 и авторизации запросов на хранение и хранение очереди Azure Blob.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d3ee211298598d78f423d88fd4df1c58ed4bfa29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268487"
---
# <a name="acquire-a-token-from-azure-ad-for-authorizing-requests-from-a-client-application"></a>Приобретение токена из Azure AD для авторизации запросов из клиентского приложения

Ключевым преимуществом использования Active Directory Azure (Azure AD) с хранением Azure Blob или хранилищем очередей является то, что учетные данные больше не должны храниться в коде. Вместо этого вы можете запросить токен доступа OAuth 2.0 от платформы идентификации Майкрософт (ранее Azure AD). Azure AD проверяет подлинность принципаслужбы безопасности (пользователя, группы или основного обслуживания), работая с приложением. Если аутентификация удается, Azure AD возвращает маркер доступа в приложение, и приложение может использовать маркер доступа для авторизации запросов в хранилище Azure Blob или хранилище очереди.

В этой статье показано, как настроить нативное приложение или веб-приложение для проверки подлинности с помощью платформы идентификации Microsoft 2.0. В примерах кода используется .NET, но в других языках применяется аналогичный подход. Для получения дополнительной информации о платформе [Microsoft identity platform (v2.0) overview](../../active-directory/develop/v2-overview.md)идентификации Microsoft 2.0 см.

Общие сведения о процессе предоставления кода OAuth 2.0 представлены в разделе [Авторизация доступа к веб-приложениям Azure Active Directory с помощью потока предоставления кода OAuth 2.0](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Назначить роль директору службы безопасности Azure AD

Чтобы обеспечить проверку подлинности субъекта безопасности из приложения службы хранилища Azure, нужно сначала настроить параметры управления доступом на основе ролей (RBAC) для этого субъекта безопасности. Azure Storage определяет встроенные роли RBAC, которые охватывают разрешения для контейнеров и очередей. При назначении роли RBAC субъекту безопасности ему предоставляется доступ к соответствующему ресурсу. Для получения дополнительной информации [см. Управление правами доступа к данным Azure Blob и Очереди с помощью RBAC.](storage-auth-aad-rbac.md)

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Регистрация приложения в клиенте Azure AD

Первым шагом в использовании Azure AD для авторизации доступа к ресурсам хранения является регистрация клиентского приложения с помощью клиента Azure AD-на [портале Azure.](https://portal.azure.com) При регистрации клиентского приложения вы предоставляете информацию о приложении ВAzure AD. После этого служба Azure AD предоставляет идентификатор клиента (также называемый *идентификатором приложения*), позволяющий связать с ней приложение во время выполнения. Дополнительные сведения об идентификаторе клиента см. в статье [Объекты приложения и субъекта-службы в Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md).

Чтобы зарегистрировать приложение Azure Storage, выполните действия, указанные в [веб-сайте: Зарегистрируйте приложение на платформе идентификации Майкрософт.](../../active-directory/develop/quickstart-configure-app-access-web-apis.md) Следующее изображение показывает общие настройки для регистрации веб-приложения:

![Скриншот, показывающий, как зарегистрировать приложение хранения с помощью Azure AD](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> Если вы регистрируете свою заявку в качестве нативной заявки, вы можете указать любой действительный URI для **Redirect URI.** Для натимного приложения это значение не должно быть реальным URL- Для веб-приложений перенаправление URI должно быть действительным URI, поскольку он определяет URL,к которому предоставляются токены.

После регистрации приложения в разделе **Параметры** отобразится идентификатор приложения (идентификатор клиента).

![Скриншот с указанием идентификатора клиента](./media/storage-auth-aad-app/app-registration-client-id.png)

Дополнительные сведения о регистрации приложения в Azure AD см. в разделе [Интеграция приложений с Azure Active Directory](../../active-directory/develop/quickstart-v2-register-an-app.md).

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Предоставление зарегистрированному приложению разрешений для службы хранилища Azure

Далее предоставьте приложению разрешение на вызов APIs хранения данных Azure. Этот шаг позволяет приложению авторизовать запросы в Хранилище Azure с помощью Azure AD.

1. На странице **«Обзор»** для зарегистрированного приложения выберите **Разрешения API просмотра—**—
1. В разделе **разрешений API** выберите **добавление разрешения** и выберите **API Майкрософт.**
1. Выберите **хранилище Azure** из списка результатов для отображения панели **разрешений API Запроса.**
1. В соответствии с **какимтип разрешениям требуется ваше приложение?**, обратите внимание, что доступный тип разрешения **делегирует разрешения.** Эта опция выбрана для вас по умолчанию.
1. В разделе **Разрешения Select разрешений** **разрешения Запросa API** панели, выберите флажок рядом с **user_impersonation,** а затем нажмите **Добавить разрешения.**

    ![Скриншот с указанием разрешений на хранение](media/storage-auth-aad-app/registered-app-permissions-1.png)

Панель **разрешений API** теперь показывает, что зарегистрированное приложение Azure AD имеет доступ как к Microsoft Graph, так и к хранилищем Azure. Разрешения выдаются Microsoft Graph автоматически при первой регистрации приложения в Azure AD.

![Скриншот, показывающий разрешения приложения регистра](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>Создание секрета клиента

Приложению нужна секрет клиента, чтобы доказать свою личность при запросе токена. Чтобы добавить секрет клиента, выполните следующие действия:

1. Перейдите к регистрации приложения на портале Azure.
1. Выберите настройку **сертификатов & секретов.**
1. Под **секретами клиента,** нажмите **Новый секрет клиента,** чтобы создать новый секрет.
1. Предоставьте описание секрета и выберите желаемый интервал истечения срока действия.
1. Немедленно скопируйте значение нового секрета в безопасное место. Полная стоимость отображается вам только один раз.

    ![Скриншот, показывающий секрет клиента](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>Библиотеки клиентов для приобретения токенов

После регистрации приложения и предоставления ему разрешений на доступ к данным в хранилище Azure Blob или в очереди можно добавить код в ваше приложение для проверки подлинности принципа безопасности и приобретения токена OAuth 2.0. Для проверки подлинности и приобретения токена можно использовать одну из [библиотек аутентификации платформы майкрософт](../../active-directory/develop/reference-v2-libraries.md) или другую библиотеку с открытым исходным кодом, поддерживающую OpenID Connect 1.0. Затем приложение может использовать маркер доступа для авторизации запроса в отношении хранилища Azure Blob или хранения очереди.

Список сценариев, для которых поддерживается приобретение [authentication flows](/en-us/azure/active-directory/develop/msal-authentication-flows) токенов, [Microsoft Authentication Library content](/azure/active-directory/develop/msal-overview)см.

## <a name="well-known-values-for-authentication-with-azure-ad"></a>Известные значения для аутентификации с помощью Azure AD

Чтобы выполнить аутентификацию субъекта безопасности с помощью Azure AD, необходимо добавить в код некоторые известные значения.

### <a name="azure-ad-authority"></a>Центр Azure AD

Ниже приведен базовый центр Azure AD для общедоступного облака Microsoft, где *tenant-id* — это идентификатор клиента Active Directory (или идентификатор каталога).

`https://login.microsoftonline.com/<tenant-id>/`

Идентификатор клиента определяет клиент Azure AD для аутентификации. Он также называется идентификатором каталога. Чтобы получить идентификатор клиента, перейдите на страницу **«Обзор»** для регистрации приложения на портале Azure и скопируйте значение оттуда.

### <a name="azure-storage-resource-id"></a>Идентификатор ресурса Azure Storage

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Пример кода .NET: создание блочного BLOB-объекта

В примере кода показано, как получить маркер доступа из Azure AD. Маркер доступа используется для аутентификации указанного пользователя и последующей авторизации запроса на создание блочного BLOB-объекта. Чтобы этот пример работал, сначала выполните инструкции в предыдущих разделах.

Чтобы запросить токен, вам потребуются следующие значения из регистрации приложения:

- Имя домена Azure AD. Извлеките это значение со страницы **«Обзор»** в ашего активном каталоге Azure.
- Идентификатор клиента (или каталога). Извлеките это значение со страницы **«Обзор»** регистрации приложения.
- Идентификатор клиента (или приложения). Извлеките это значение со страницы **«Обзор»** регистрации приложения.
- Перенаправление клиента URI. Извлеките это значение из настроек **аутентификации** для регистрации приложения.
- Ценность секрета клиента. Извлеките это значение из места, в которое вы ранее скопировали его.

### <a name="create-a-storage-account-and-container"></a>Создание учетной записи хранения и контейнера

Чтобы запустить образец кода, создайте учетную запись хранилища в той же подписке, что и каталог Active Azure. Затем создайте контейнер в этом хранилище. Образец кода создаст блок капли в этом контейнере.

Далее явно назначайте роль **вкладчика Хранилища данных** учетной записи пользователя, под которой вы будете запускать пример кода. Для получения инструкций о том, как назначить [Grant access to Azure blob and queue data with RBAC in the Azure portal](storage-auth-aad-rbac-portal.md)эту роль на портале Azure, см.

> [!NOTE]
> При создании учетной записи хранилища Azure автоматически не назначаются разрешения на доступ к данным через Azure AD. Для службы хранилища Azure вы должны назначить себе роль RBAC явным образом. Вы можете назначить ее на уровне подписки, группы ресурсов, учетной записи хранения, контейнера или очереди.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Создание веб-приложения, которое разрешает доступ к хранению Blob с помощью Azure AD

Когда приложение получает доступ к Хранилище Azure, оно делает это от имени пользователя, что означает, что ресурсы blob или очереди доступны с помощью разрешений пользователя, который входит в систему. Чтобы попробовать этот пример кода, вам нужно веб-приложение, которое предлагает пользователю войти в систему с помощью итогового индекса Azure AD. Вы можете создать свой собственный, или использовать образец приложения, предоставленного корпорацией Майкрософт.

Завершенный образец веб-приложения, который приобретает токен и использует его для создания капли в Azure Storage, доступен на [GitHub.](https://aka.ms/aadstorage) Просмотр и запуск завершенного образца может быть полезным для понимания примеров кода. Для получения инструкций о том, как запустить завершенный образец, смотрите раздел под названием [View и запустите завершенный образец.](#view-and-run-the-completed-sample)

#### <a name="add-references-and-using-statements"></a>Добавление ссылок и инструкций using  

Из Visual Studio установите клиентскую библиотеку Azure Storage. Из меню **Инструменты,** выберите **NuGet менеджер пакетов**, а затем **консоль менеджер пакетов**. Введите следующие команды в окне консоли для установки необходимых пакетов из клиентской библиотеки Azure Storage для .NET:

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

Затем добавьте следующие операторы с помощью в файл HomeController.cs:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

#### <a name="create-a-block-blob"></a>Создание блочного BLOB-объекта

Добавьте следующий фрагмент кода, чтобы создать блок капли:

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
> Чтобы разрешить операции blob и очереди с токеном OAuth 2.0, необходимо использовать HTTPS.

В приведенном выше примере клиентская библиотека .NET обрабатывает авторизацию запроса для создания блочного BLOB-объекта. Клиентские библиотеки Azure Storage для других языков также обрабатывают авторизацию запроса для вас. но если вы вызываете операцию Службы хранилища Azure через REST API с помощью маркера OAuth, то авторизовать запрос нужно также с помощью маркера OAuth.

Чтобы вызвать операции служб очередей и BLOB-объектов с помощью маркеров доступа OAuth, передайте маркер доступа в заголовок **Authorization** с помощью схемы **Bearer** и укажите версию службы 2017-11-09 (или новее), как показано в примере ниже.

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-oauth-token-from-azure-ad"></a>Получение токена OAuth из Azure AD

Затем добавьте метод, который запрашивает токен из Azure AD от имени пользователя. Этот метод определяет область, на которую должны быть предоставлены разрешения. Для получения дополнительной информации о разрешениях и областях смотрите [Разрешения и согласие в конечном пункте платформы идентификации Майкрософт.](../../active-directory/develop/v2-permissions-and-consent.md)

Используйте идентификатор ресурсов, чтобы построить область, для которой приобрести токен. Пример конструирует область, используя идентификатор `user_impersonation` ресурса вместе со встроенным областью, что указывает на то, что токен запрашивается от имени пользователя.

Имейте в виду, что вам может понадобиться представить пользователю интерфейс, который позволяет пользователю дать согласие на запрос маркера от его имени. Когда необходимо согласие, пример ловит **MsalUiRequiredException** и вызывает другой метод для облегчения запроса на согласие:

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

Согласие — это процесс предоставления пользователем разрешения приложению получать доступ к защищенным ресурсам от имени пользователя. Платформа идентификации Майкрософт 2.0 поддерживает постепенное согласие, что означает, что директор службы безопасности может запросить минимальный набор разрешений на начальном этапе и добавить разрешения с течением времени по мере необходимости. Когда код запрашивает токен доступа, укажите область разрешений, которые нужны `scope` вашему приложению в любой момент времени в параметре. Для получения дополнительной информации о поэтапном согласии смотрите раздел под названием **"Инкрементное и динамическое согласие** в [разделе Почему обновление до платформы идентификации Microsoft (v2.0)?.](../../active-directory/azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent)

Следующий метод строит свойства аутентификации для запроса поэтапного согласия:

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

## <a name="view-and-run-the-completed-sample"></a>Просмотр и запуск завершенного образца

Чтобы запустить образец приложения, сначала клонировать или скачать его с [GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal). Затем обновите приложение, как описано в следующих разделах.

### <a name="provide-values-in-the-settings-file"></a>Предоставление значений в файле настроек

Далее обновите файл *appsettings.json* с вашими собственными значениями, следующим образом:

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

### <a name="update-the-storage-account-and-container-name"></a>Обновление учетной записи хранилища и имени контейнера

В *HomeController.cs* файле обновите URI, который ссылается на блок капли, чтобы использовать имя вашей учетной записи хранения и контейнера:

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>Включить неявный поток грантов

Для выполнения выборки может потребоваться настроить неявный поток грантов для регистрации приложения. Выполните следующие действия.

1. Перейдите к регистрации приложения на портале Azure.
1. В разделе Управление выберите настройку **аутентификации.**
1. В **разделе Расширенные настройки**в разделе **Неявный грант** выберите флажки для включения токенов доступа и идентификационных токенов, как показано на следующем изображении:

    ![Скриншот, показывающий, как включить настройки для неявного потока грантов](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>Обновление порта, используемого местным хозяином

При запуске образца может возникнуть необходимость обновления перенаправления URI, указанного в регистрации приложения, чтобы использовать порт *локального,* назначенный во время выполнения. Чтобы обновить перенаправление URI на использование назначенного порта, выполните следующие действия:

1. Перейдите к регистрации приложения на портале Azure.
1. В разделе Управление выберите настройку **аутентификации.**
1. Под **перенаправлением URIs**отспособьте порт в соответствии с тем, что используется в примере приложения, как показано на следующем изображении:

    ![Скриншот, показывающий перенаправление URIs для регистрации приложений](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы узнать больше о платформе [Microsoft identity platform](https://docs.microsoft.com/azure/active-directory/develop/)идентификации Майкрософт, см.
- Чтобы узнать больше о роли RBAC для хранения данных Azure, [см.](storage-auth-aad-rbac.md)
- Чтобы узнать об использовании управляемых идентификаторов для ресурсов Azure с помощью ресурсов Azure, см. [Аутентификацию доступа к каплям и очередям с помощью каталога Azure Active И управляемых идентификаторов для ресурсов Azure.](storage-auth-aad-msi.md)
