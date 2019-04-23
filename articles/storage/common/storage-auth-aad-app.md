---
title: Проверка подлинности с помощью Azure Active Directory для доступа к данным больших двоичных объектов и очереди из приложений | Документация Майкрософт
description: Использование Azure Active Directory для проверки подлинности из приложения, а затем авторизовать запросов для больших двоичных объектов и очередей.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: c4959d6abd1b33c81f731ad0479bd59d271c7797
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149169"
---
# <a name="authenticate-with-azure-active-directory-from-an-application-for-access-to-blobs-and-queues"></a>Пройти проверку подлинности с помощью Azure Active Directory с приложения для доступа к большим двоичным объектам и очередям

Ключевым преимуществом использования Azure Active Directory (Azure AD) со службой хранилища Azure является то, что учетные данные больше не нужно хранить в коде. Вместо этого можно запросить маркер доступа OAuth 2.0 из Azure AD. Azure AD осуществляет аутентификацию субъекта безопасности (пользователя, группы или субъекта-службы), запустившего приложение. Если аутентификация пройдена успешно, Azure AD возвращает маркер доступа приложению, и затем приложение может использовать его для авторизации запросов к службе хранилища Azure.

В этой статье показано, как настроить приложение для использования аутентификации на основе Azure AD. В примерах кода используется .NET, но в других языках применяется аналогичный подход.

Для проверки подлинности участника безопасности из своего приложения службы хранилища Azure, Настройка параметров управления ДОСТУПОМ на основе ролей доступа для этого участника безопасности. Служба хранилища Azure определяет роли RBAC, которые включают в себя разрешения для контейнеров и очередей. При назначении роли RBAC субъекту безопасности ему предоставляется доступ к соответствующему ресурсу. Дополнительные сведения см. в разделе [управление права доступа к данным хранилища с помощью RBAC](storage-auth-aad-rbac.md).

Общие сведения о процессе предоставления кода OAuth 2.0 представлены в разделе [Авторизация доступа к веб-приложениям Azure Active Directory с помощью потока предоставления кода OAuth 2.0](../../active-directory/develop/v1-protocols-oauth-code.md).

Для авторизации операций с большими двоичными объектами и очередями с использованием токена OAuth необходимо использовать протокол HTTPS.

## <a name="assign-an-rbac-role-to-an-azure-ad-security-principal"></a>Назначение роли RBAC для субъекта безопасности Azure AD

Чтобы обеспечить проверку подлинности субъекта безопасности из приложения службы хранилища Azure, нужно сначала настроить параметры управления доступом на основе ролей (RBAC) для этого субъекта безопасности. Служба хранилища Azure определяет роли RBAC, которые включают в себя разрешения для контейнеров и очередей. При назначении роли RBAC субъекту безопасности ему предоставляется доступ к соответствующему ресурсу. Дополнительные сведения см. в разделе [управление права доступа к данным BLOB-объектов Azure и очереди с помощью RBAC](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Регистрация приложения в клиенте Azure AD

При использовании Azure AD для предоставления доступа к ресурсам хранилища в первую очередь необходимо зарегистрировать клиентское приложение в клиенте Azure AD. Зарегистрировав приложение, вы сможете вызвать [библиотеку аутентификации Azure Active Directory ](../../active-directory/active-directory-authentication-libraries.md) (ADAL) из кода. Эта библиотека предоставляет API для аутентификации с помощью Azure AD в приложении. Регистрация приложения также дает возможность авторизовать вызовы из этого приложения к интерфейсам API службы хранилища Azure с помощью маркера доступа.

При регистрации приложения вы отправляете сведения о приложении в Azure AD. После этого служба Azure AD предоставляет идентификатор клиента (также называемый *идентификатором приложения*), позволяющий связать с ней приложение во время выполнения. Дополнительные сведения об идентификаторе клиента см. в статье [Объекты приложения и субъекта-службы в Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md).

Чтобы зарегистрировать приложение службы хранилища Azure, выполните инструкции, приведенные в разделе [Добавление приложения](../../active-directory/develop/quickstart-v1-add-azure-ad-app.md) статьи [Интеграция приложений с Azure Active Directory](../../active-directory/active-directory-integrating-applications.md). При регистрации приложения как собственного вы можете указать любой допустимый универсальный код ресурса (URI) в качестве **URI перенаправления**. Реальную конечную точку указывать необязательно.

![Снимок экрана, показывающий, как зарегистрировать приложение хранилища в Azure AD](./media/storage-auth-aad-app/app-registration.png)

После регистрации приложения в разделе **Параметры** отобразится идентификатор приложения (идентификатор клиента).

![Снимок экрана, показывающий идентификатор клиента](./media/storage-auth-aad-app/app-registration-client-id.png)

Дополнительные сведения о регистрации приложения в Azure AD см. в разделе [Интеграция приложений с Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). 

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Предоставление зарегистрированному приложению разрешений для службы хранилища Azure

Далее необходимо предоставить приложению разрешения для вызова интерфейсов API службы хранилища Azure. Это позволит вашему приложению авторизовать вызовы к службе хранилища Azure с помощью Azure AD.

1. В области навигации слева на портале Azure выберите **Все службы** и выполните поиск элемента **Регистрация приложений**.
2. Найдите имя зарегистрированного приложения, созданное на предыдущем шаге.
3. Выберите это приложение и щелкните **Параметры**. В разделе **Доступ через API** выберите **Необходимые разрешения**.
4. В колонке **Необходимые разрешения** нажмите кнопку **Добавить**.
5. В разделе **Select an API** (Выбор API) выполните поиск фразы "Служба хранилища Azure" и выберите элемент **Служба хранилища Azure** из списка результатов.

    ![Снимок экрана: отображение разрешения для хранилища](media/storage-auth-aad-app/registered-app-permissions-1.png)

6. В разделе **Выбор разрешений** установите флажок рядом с элементом **Access Azure Storage** (Доступ к службе хранилища Azure) и щелкните **Выбрать**.
7. Нажмите кнопку **Done**(Готово).

Теперь в окне **Необходимые разрешения** вы можете видеть, что приложение Azure AD имеет доступ к Azure Active Directory и службе хранилища Azure. Разрешения службе Azure AD предоставляются автоматически во время первой регистрации приложения в Azure AD.

![Снимок экрана, показывающий зарегистрировать разрешения приложения](media/storage-auth-aad-app/registered-app-permissions-2.png)

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

Идентификатор клиента определяет клиент Azure AD для аутентификации. Чтобы получить идентификатор клиента, выполните указания, описанные в разделе **Получение идентификатора клиента для Azure Active Directory**.

#### <a name="storage-resource-id"></a>Идентификатор ресурса хранилища

Идентификатор ресурса службы хранилища Azure позволяет получить маркер для аутентификации запросов к службе хранилища Azure.

`https://storage.azure.com/`

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Получение идентификатора клиента для Azure Active Directory

Чтобы получить идентификатор клиента, сделайте следующее:

1. На портале Azure выберите Active Directory.
2. Щелкните **Свойства**.
3. Скопируйте значение GUID, указанное для параметра **Идентификатор каталога**. Это значение также называется идентификатором клиента.

![Снимок экрана, показывающий, как скопировать идентификатор клиента](./media/storage-auth-aad-app/aad-tenant-id.png)

### <a name="add-references-and-using-statements"></a>Добавление ссылок и инструкций using  

Из Visual Studio установите клиентскую библиотеку хранилища Azure. В меню **Сервис** выберите **Диспетчер пакетов NuGet**, а затем — **Консоль диспетчера пакетов**. Введите следующую команду в консоли, чтобы установить последнюю версию клиентской библиотеки для .NET:

```
Install-Package WindowsAzure.Storage
```

Также установите последнюю версию ADAL:

```
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

Далее добавьте приведенные ниже инструкции using в код.

```dotnet
using System.Globalization;
using Microsoft.IdentityModel.Clients.ActiveDirectory; //ADAL client library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

### <a name="get-an-oauth-token-from-azure-ad"></a>Получение токена OAuth из Azure AD

Добавьте метод, который запрашивает токен из Azure AD. Чтобы запросить токен, вызовите метод [AuthenticationContext.AcquireTokenAsync](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory.authenticationcontext.acquiretokenasync). Убедитесь, что вы получили следующие значения на выполненных ранее шагах:

- идентификатор клиента (каталога);
- идентификатор клиента (приложения);
- URI перенаправления клиента.

```dotnet
static string GetUserOAuthToken()
{
    const string ResourceId = "https://storage.azure.com/";
    const string AuthInstance = "https://login.microsoftonline.com/{0}/";
    const string TenantId = "<tenant-id>"; // Tenant or directory ID

    // Construct the authority string from the Azure AD OAuth endpoint and the tenant ID. 
    string authority = string.Format(CultureInfo.InvariantCulture, AuthInstance, TenantId);
    AuthenticationContext authContext = new AuthenticationContext(authority);

    // Acquire an access token from Azure AD. 
    AuthenticationResult result = authContext.AcquireTokenAsync(ResourceId, 
                                                                "<client-id>", 
                                                                new Uri(@"<client-redirect-uri>"), 
                                                                new PlatformParameters(PromptBehavior.Auto)).Result;

    return result.AccessToken;
}
```

### <a name="create-the-block-blob"></a>Создание блочного BLOB-объекта

Наконец использование маркера доступа для создания новых учетных данных хранилища и использовать эти учетные данные для создания большого двоичного объекта. Имейте в виду, что для авторизации операций BLOB-объектов и очередей с помощью токена OAuth, необходимо использовать протокол HTTPS.:

```dotnet
// Get the access token.
string accessToken = GetUserOAuthToken();

// Use the access token to create the storage credentials.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using those credentials
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);

blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
```

> [!NOTE]
> Интеграция Azure AD со службой хранилища Azure требует использования протокола HTTPS для операций службы хранилища Azure.

В приведенном выше примере клиентская библиотека .NET обрабатывает авторизацию запроса для создания блочного BLOB-объекта. Другие клиентские библиотеки хранилища также обрабатывают авторизацию запроса, но если вы вызываете операцию Службы хранилища Azure через REST API с помощью маркера OAuth, то авторизовать запрос нужно также с помощью маркера OAuth.   

Чтобы вызвать операции служб очередей и BLOB-объектов с помощью маркеров доступа OAuth, передайте маркер доступа в заголовок **Authorization** с помощью схемы **Bearer** и укажите версию службы 2017-11-09 (или новее), как показано в примере ниже.

```
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

Дополнительные сведения об авторизации операций в хранилище Azure из REST, см. в разделе [проверка подлинности с помощью Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory).

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о ролях RBAC для службы хранилища Azure, см. в разделе [управление права доступа к данным хранилища с помощью RBAC](storage-auth-aad-rbac.md).
- Дополнительные сведения об использовании управляемых удостоверений для ресурсов Azure со службой хранилища Azure, см. в разделе [проверки подлинности доступа к BLOB-объектов и очередей с Azure Active Directory и управляемых удостоверений для ресурсов Azure](storage-auth-aad-msi.md).
- Чтобы узнать, как для выполнения команд Azure CLI и PowerShell учетные данные Azure AD, см. в разделе [команды запуска интерфейса командной строки Azure или PowerShell учетные данные Azure AD для доступа к данным BLOB-объектов или очередей](storage-auth-aad-script.md).
