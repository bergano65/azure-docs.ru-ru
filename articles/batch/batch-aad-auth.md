---
title: Проверка подлинности служб пакетной службы Azure с помощью Azure Active Directory
description: Пакетная служба поддерживает Azure AD для аутентификации из пакетной службы. Узнайте, как пройти проверку подлинности одним из двух способов.
ms.topic: article
ms.date: 01/28/2020
ms.openlocfilehash: e1f95871788b4b9848ba622da1c8eb0cc83c07aa
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116185"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Аутентификация решений пакетной службы с помощью Active Directory

Пакетная служба Azure поддерживает аутентификацию [Azure Active Directory][aad_about] (Azure AD). Azure AD — многопользовательский облачный каталог и служба управления удостоверениями корпорации Майкрософт. Инфраструктура Azure использует Azure AD для аутентификации клиентов, администраторов служб и пользователей организации.

Процесс аутентификации Azure AD в пакетной службе Azure можно выполнить двумя способами:

- Аутентификация пользователя, взаимодействующего с приложением, с использованием **встроенной аутентификации**. Приложение, использующее встроенную аутентификацию, собирает учетные данные пользователя и использует их для аутентификации доступа к ресурсам пакетной службы.
- Аутентификация автоматического приложения с использованием **субъекта-службы**. Субъект-служба определяет политику и разрешения приложения, чтобы представить приложение при получении доступа к ресурсам в среде выполнения.

Дополнительные сведения о службе Azure AD см. в [документации по Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="endpoints-for-authentication"></a>Конечные точки аутентификации

Чтобы выполнить аутентификацию приложений пакетной службы с помощью Azure AD, необходимо включить некоторые известные конечные точки в код.

### <a name="azure-ad-endpoint"></a>Конечная точка Azure AD

Базовая конечная точка авторизации Azure AD:

`https://login.microsoftonline.com/`

Чтобы выполнить аутентификацию с помощью Azure AD, вам потребуется эта конечная точка и идентификатор клиента (идентификатором каталога). Идентификатор клиента определяет клиент Azure AD для аутентификации. Чтобы получить идентификатор клиента, выполните указания, описанные в разделе [Получение идентификатора клиента для Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE] 
> В процессе аутентификации с использованием субъекта-службы требуется конечная точка определенного клиента. 
> 
> При аутентификации с помощью встроенной аутентификации эта конечная точка является не обязательной. Тем не менее мы советуем использовать ее. Но вы также можете использовать общую конечную точку Azure AD, которая предоставляет универсальный интерфейс для сбора общих учетных данных в случае отсутствия конкретного клиента. Общая конечная точка выглядит так: `https://login.microsoftonline.com/common`.
>
>

Дополнительные сведения о конечных точках Azure AD см. в статье [Сценарии аутентификации в Azure Active Directory][aad_auth_scenarios].

### <a name="batch-resource-endpoint"></a>Конечная точка ресурса пакетной службы

**Конечная точка ресурса пакетной службы Azure** позволяет получить токен аутентификации запросов для пакетной службы:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Регистрация приложения в клиенте

При использовании Azure AD для аутентификации в первую очередь необходимо зарегистрировать приложение в клиенте Azure AD. Зарегистрировав приложение, вы сможете вызвать [библиотеку аутентификации Azure Active Directory ][aad_adal] (ADAL) из кода. Эта библиотека предоставляет API для аутентификации с помощью Azure AD в приложении. Регистрация приложения необходима, если вы планируете использовать встроенную аутентификацию или аутентификацию с использованием субъекта-службы.

При регистрации приложения вы отправляете сведения о приложении в Azure AD. После этого служба Azure AD предоставит идентификатор приложения (*идентификатор клиента*), позволяющий связать с ней приложение во время выполнения. Дополнительные сведения об идентификаторе приложения см. в статье [Объекты приложения и субъекта-службы в Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Чтобы зарегистрировать приложение пакетной службы, выполните инструкции, приведенные в разделе [Добавление приложения](../active-directory/develop/quickstart-register-app.md) статьи [Интеграция приложений с Azure Active Directory][aad_integrate]. При регистрации приложения как собственного вы можете указать любой допустимый универсальный код ресурса (URI) в качестве **URI перенаправления**. Реальную конечную точку указывать необязательно.

После регистрации приложения отобразится его идентификатор:

![Регистрация приложения пакетной службы в Azure AD](./media/batch-aad-auth/app-registration-data-plane.png)

Дополнительные сведения о регистрации приложения в Azure AD см. в статье [Сценарии аутентификации в Azure Active Directory](../active-directory/develop/authentication-scenarios.md).

## <a name="get-the-tenant-id-for-your-active-directory"></a>Получение идентификатора клиента для Active Directory

Идентификатор клиента определяет клиент Azure AD, который предоставляет службы аутентификации приложению. Чтобы получить идентификатор клиента, сделайте следующее:

1. На портале Azure выберите Active Directory.
1. Выберите **Свойства**.
1. Скопируйте значение GUID, указанное для параметра **Идентификатор каталога**. Это значение также называется идентификатором клиента.

![Копирование идентификатора каталога](./media/batch-aad-auth/aad-directory-id.png)

## <a name="use-integrated-authentication"></a>Использование встроенной аутентификации

Чтобы выполнить аутентификацию с помощью встроенной аутентификации, необходимо предоставить приложению разрешения на подключение к API пакетной службы. Это позволит ему выполнить аутентификацию вызовов к API пакетной службы с помощью Azure AD.

Чтобы предоставить приложению доступ к пакетной службе, после регистрации приложения выполните следующие действия на портале Azure:

1. На портале Azure на панели навигации слева выберите **Все службы**. Выберите **Регистрация приложений**.
1. Найдите имя приложения в списке зарегистрированных приложений:

    ![Поиск имени приложения](./media/batch-aad-auth/search-app-registration.png)

1. Выберите приложение и щелкните **разрешения API**.
1. В разделе **разрешения API** выберите **Добавить разрешение**.
1. В разделе **Выбор API** найдите API пакетной службы. Продолжайте поиск для каждой из этих строк, пока не найдете API:
    1. **пакетная служба Microsoft Azure**
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** — это идентификатор API пакетной службы.
1. Найдя API пакетной службы, выберите его и нажмите **кнопку Выбрать**.
1. На странице **Выбор разрешений**установите флажок для **доступа к пакетной службе Azure** и нажмите кнопку **Добавить разрешения**.

В разделе **разрешений API** теперь показано, что приложение Azure AD имеет доступ как к Microsoft Graph, так и к API пакетной службы. Разрешения предоставляются Microsoft Graph автоматически при первой регистрации приложения в Azure AD.

![Предоставление разрешений API](./media/batch-aad-auth/required-permissions-data-plane.png)

## <a name="use-a-service-principal"></a>Использование субъекта-службы

Чтобы выполнить аутентификацию приложения, которое выполняется автоматически, необходимо использовать субъект-службу. После регистрации приложения выполните приведенные ниже действия на портале Azure, чтобы настроить субъект-службу.

1. Запросите секрет для своего приложения.
1. Назначьте приложению управление доступом на основе ролей (RBAC).

### <a name="request-a-secret-for-your-application"></a>Запрос секрета для приложения

Когда приложение проходит проверку подлинности с помощью субъекта-службы, оно отправляет идентификатор приложения и секрет в Azure AD. Вам необходимо создать и скопировать секретный ключ, чтобы использовать его из кода.

На портале Azure сделайте следующее:

1. На портале Azure на панели навигации слева выберите **Все службы**. Выберите **Регистрация приложений**.
1. Выберите приложение в списке регистраций приложений.
1. Выберите приложение, а затем выберите **сертификаты & секреты**. В разделе **секреты клиента** выберите **новый секрет клиента**.
1. Чтобы создать секрет, введите описание секрета. Затем выберите срок действия секрета в один год, два года или без истечения срока действия.
1. Выберите **Добавить** , чтобы создать и отобразить секрет. Скопируйте значение секрета в надежное место, так как вы не сможете снова получить доступ к нему после выхода из страницы.

    ![Создание секретного ключа](./media/batch-aad-auth/secret-key.png)

### <a name="assign-rbac-to-your-application"></a>Назначение RBAC приложению

Для проверки подлинности с помощью субъекта-службы необходимо назначить для приложения RBAC. Выполните следующие действия.

1. На портале Azure перейдите к учетной записи пакетной службы, используемой приложением.
1. В разделе **Параметры** учетной записи пакетной службы выберите **Управление доступом (IAM)**.
1. Выберите вкладку **Назначения ролей**.
1. Выберите **добавить назначение ролей**.
1. В раскрывающемся списке **Роль** выберите роль приложения — *Участник* или *Читатель*. Дополнительные сведения об этих ролях см. в статье [Начало работы с управлением доступом на основе ролей на портале Azure](../role-based-access-control/overview.md).  
1. В поле **Выбрать** введите имя приложения. Выберите приложение из списка, а затем нажмите кнопку **сохранить**.

После этого приложение с назначенной ролью RBAC должно появиться в параметрах контроля доступа.

![Назначение приложению роли RBAC](./media/batch-aad-auth/app-rbac-role.png)

### <a name="assign-a-custom-role"></a>Назначение настраиваемой роли

Пользовательская роль предоставляет детализированное разрешение пользователю на отправку заданий, задач и т. д. Это дает возможность запретить пользователям выполнять операции, влияющие на стоимость, например создание пулов или изменение узлов.

Пользовательскую роль можно использовать для предоставления разрешений пользователю Azure AD, группе или субъекту-службе для следующих операций RBAC:

- Microsoft.Batch/batchAccounts/pools/write
- Microsoft.Batch/batchAccounts/pools/delete
- Microsoft.Batch/batchAccounts/pools/read
- Microsoft. Batch/Батчаккаунтс/Жобсчедулес/запись
- Microsoft. Batch/Батчаккаунтс/Жобсчедулес/удаление
- Microsoft. Batch/Батчаккаунтс/Жобсчедулес/чтение
- Microsoft. Batch/Батчаккаунтс/задания/запись
- Microsoft. Batch/Батчаккаунтс/задания/Удаление
- Microsoft. Batch/Батчаккаунтс/задания/чтение
- Microsoft.Batch/batchAccounts/certificates/write
- Microsoft.Batch/batchAccounts/certificates/delete
- Microsoft.Batch/batchAccounts/certificates/read
- Microsoft. Batch/Батчаккаунтс/Read (для любой операции чтения)
- Microsoft. Batch/Батчаккаунтс/listKeys/действие (для любой операции)

Пользовательские роли предназначены для пользователей, прошедших проверку подлинности в Azure AD, а не учетных данных учетной записи пакетной службы (общий ключ). Обратите внимание, что учетная запись пакетной службы предоставляет полный доступ к учетной записи пакетной службы. Также обратите внимание, что для заданий, использующих автопул, требуются разрешения уровня пула.

Ниже приведен пример пользовательского определения роли.

```json
{
 "properties":{
    "roleName":"Azure Batch Custom Job Submitter",
    "type":"CustomRole",
    "description":"Allows a user to submit jobs to Azure Batch but not manage pools",
    "assignableScopes":[
      "/subscriptions/88888888-8888-8888-8888-888888888888"
    ],
    "permissions":[
      {
        "actions":[
          "Microsoft.Batch/*/read",
          "Microsoft.Authorization/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*",
          "Microsoft.Insights/alertRules/*"
        ],
        "notActions":[

        ],
        "dataActions":[
          "Microsoft.Batch/batchAccounts/jobs/*",
          "Microsoft.Batch/batchAccounts/jobSchedules/*"
        ],
        "notDataActions":[

        ]
      }
    ]
  }
}
```

Общие сведения о создании настраиваемой роли см. в статье [пользовательские роли для ресурсов Azure](../role-based-access-control/custom-roles.md).

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Получение идентификатора клиента для Azure Active Directory

Идентификатор клиента определяет клиент Azure AD, который предоставляет службы аутентификации приложению. Чтобы получить идентификатор клиента, сделайте следующее:

1. На портале Azure выберите Active Directory.
1. Выберите **Свойства**.
1. Скопируйте значение GUID, указанное для параметра **Идентификатор каталога**. Это значение также называется идентификатором клиента.

![Копирование идентификатора каталога](./media/batch-aad-auth/aad-directory-id.png)

## <a name="code-examples"></a>Примеры кода

В примерах кода в этом разделе показано, как выполнить аутентификацию в Azure AD, используя встроенную и субъект-службу. В большинстве примеров кода используется .NET, но общие концепции актуальны и для других языков.

> [!NOTE]
> Срок действия маркера проверки подлинности Azure AD истекает через час. При использовании долговременного объекта **BatchClient** мы советуем получать маркер из ADAL при каждом запросе, чтобы у вас всегда был допустимый маркер. 
>
>
> Чтобы достичь этого в .NET, напишите метод, который получает маркер из Azure AD, и передайте этот метод в качестве делегата в объект **BatchTokenCredentials**. Чтобы гарантировать предоставление допустимого маркера, при каждом запросе к пакетной службе вызывается метод делегата. По умолчанию ADAL кэширует маркеры, поэтому новый маркер извлекается из Azure AD только при необходимости. Дополнительные сведения о маркерах в Azure AD см. в статье [Сценарии аутентификации в Azure Active Directory][aad_auth_scenarios].
>
>

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Пример кода. Использование встроенной аутентификации Azure AD с библиотекой .NET для пакетной службы

Чтобы выполнить аутентификацию с использованием встроенной аутентификации из библиотеки .NET для пакетной службы, укажите ссылку на пакет [.NET для пакетной службы Azure](https://www.nuget.org/packages/Microsoft.Azure.Batch/) и пакет [библиотеки ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

Добавьте в код следующие инструкции `using`:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Укажите ссылку на конечную точку Azure AD в коде, в том числе идентификатор клиента. Чтобы получить идентификатор клиента, выполните указания, описанные в разделе [Получение идентификатора клиента для Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Укажите ссылку на конечную точку ресурса пакетной службы:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Укажите ссылку на учетную запись пакетной службы:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Укажите идентификатор приложения (идентификатор клиента) для вашего приложения. Идентификатор приложения становится доступен после регистрации приложения на портале Azure:

```csharp
private const string ClientId = "<application-id>";
```

Скопируйте также указанный URI перенаправления, если приложение зарегистрировано в качестве собственного приложения. URI перенаправления, указанный в коде, должен соответствовать URI перенаправления, который был указан при регистрации приложения:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Напишите метод обратного вызова, чтобы получить маркер проверки подлинности из Azure AD. Приведенный здесь метод обратного вызова **GetAuthenticationTokenAsync** вызывает библиотеку ADAL для аутентификации пользователя, взаимодействующего с приложением. Предоставленный ADAL метод **AcquireTokenAsync** запрашивает учетные данные пользователя. Как только пользователь введет их, приложение продолжит работу (если его учетные данные не кэшированы).

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    var authContext = new AuthenticationContext(AuthorityUri);

    // Acquire the authentication token from Azure AD.
    var authResult = await authContext.AcquireTokenAsync(BatchResourceUri, 
                                                        ClientId, 
                                                        new Uri(RedirectUri), 
                                                        new PlatformParameters(PromptBehavior.Auto));

    return authResult.AccessToken;
}
```

Создайте объект **BatchTokenCredentials**, который принимает делегат в качестве параметра. Используйте эти учетные данные, чтобы открыть объект **BatchClient**. Вы можете использовать объект **BatchClient** для последующих операций в пакетной службе.

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Пример кода. Использование субъекта-службы Azure AD с библиотекой .NET для пакетной службы

Чтобы выполнить аутентификацию с использованием субъекта-службы из библиотеки .NET для пакетной службы, укажите ссылки на пакет [.NET для пакетной службы Azure](https://www.nuget.org/packages/Azure.Batch/) и пакет [библиотеки ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

Добавьте в код следующие инструкции `using`:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Укажите ссылку на конечную точку Azure AD в коде, в том числе идентификатор клиента. При использовании субъекта-службы необходимо указать конечную точку определенного клиента. Чтобы получить идентификатор клиента, выполните указания, описанные в разделе [Получение идентификатора клиента для Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Укажите ссылку на конечную точку ресурса пакетной службы:  

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Укажите ссылку на учетную запись пакетной службы:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Укажите идентификатор приложения (идентификатор клиента) для вашего приложения. Идентификатор приложения становится доступен после регистрации приложения на портале Azure:

```csharp
private const string ClientId = "<application-id>";
```

Укажите секретный ключ, скопированный на портале Azure:

```csharp
private const string ClientKey = "<secret-key>";
```

Напишите метод обратного вызова, чтобы получить маркер проверки подлинности из Azure AD. Приведенный здесь метод обратного вызова **GetAuthenticationTokenAsync** вызывает библиотеку ADAL для автоматической аутентификации:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Создайте объект **BatchTokenCredentials**, который принимает делегат в качестве параметра. Используйте эти учетные данные, чтобы открыть объект **BatchClient**. Применяйте этот объект **BatchClient** для всех последующих операций в пакетной службе.

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>Пример кода. Использование субъекта-службы Azure AD с библиотекой Python для пакетной службы

Чтобы выполнить аутентификацию с использованием субъекта-службы из библиотеки Python для пакетной службы, установите модули [azure-batch](https://pypi.org/project/azure-batch/) и [azure-common](https://pypi.org/project/azure-common/) и укажите ссылки на них.

```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

Если вы используете субъект-службу, обязательно укажите идентификатор клиента. Чтобы получить идентификатор клиента, выполните указания, описанные в разделе [Получение идентификатора клиента для Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```python
TENANT_ID = "<tenant-id>"
```

Укажите ссылку на конечную точку ресурса пакетной службы:  

```python
RESOURCE = "https://batch.core.windows.net/"
```

Укажите ссылку на учетную запись пакетной службы:

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com"
```

Укажите идентификатор приложения (идентификатор клиента) для вашего приложения. Идентификатор приложения становится доступен после регистрации приложения на портале Azure:

```python
CLIENT_ID = "<application-id>"
```

Укажите секретный ключ, скопированный на портале Azure:

```python
SECRET = "<secret-key>"
```

Создайте объект **ServicePrincipalCredentials**.

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

Используйте учетные данные субъекта-службы, чтобы открыть объект **BatchServiceClient**. Используйте этот объект **BatchClient** для всех последующих операций в пакетной службе.

```python
    batch_client = BatchServiceClient(
    credentials,
    base_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>Дальнейшие шаги

- Дополнительные сведения о службе Azure AD см. в [документации по Azure Active Directory](https://docs.microsoft.com/azure/active-directory/). Подробные примеры, показывающие, как использовать ADAL, доступны в библиотеке [примеров кода Azure](https://azure.microsoft.com/resources/samples/?service=active-directory).

- Дополнительные сведения о субъекте-службе см. в статье [Объекты приложения и субъекта-службы в Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). Дополнительные сведения о создании субъекта-службы с помощью портала Azure см. в статье [Создание приложения Azure Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](../active-directory/develop/howto-create-service-principal-portal.md). Вы также можете создать субъект-службу с помощью PowerShell или Azure CLI.

- Дополнительные сведения об аутентификации решений по управлению пакетной службой с помощью Azure AD см. в [этой статье](batch-aad-auth-management.md).

- Пример Python для создания клиента пакетной службы, подлинность которого проверяется с помощью маркера Azure AD, см. в примере [Deploying Azure Batch Custom Image with a Python Script](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md) (Развертывание пользовательского образа пакетной службы Azure с помощью скрипта Python).

[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Что такое Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Сценарии проверки подлинности в Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Интеграция приложений с Azure Active Directory"
[azure_portal]: https://portal.azure.com
