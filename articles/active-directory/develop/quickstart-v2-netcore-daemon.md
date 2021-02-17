---
title: Краткое руководство. Получение маркера и вызов Microsoft Graph в консольном приложении | Azure
titleSuffix: Microsoft identity platform
description: В этом кратком руководстве демонстрируется, как пример приложения .NET Core может использовать поток учетных данных клиента для получения маркера и вызова Microsoft Graph.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/05/2020
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 99dcd81cd24f762a5c2b55f5f2977aaf61bc26e8
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103542"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-using-console-apps-identity"></a>Краткое руководство. Получение маркера безопасности и вызов API Microsoft Graph из консольного приложения с помощью удостоверения приложения

При работе с этим кратким руководством вы скачаете и выполните пример кода. Такой пример кода демонстрирует, как в консольном приложении .NET Core реализовать получение маркера доступа для вызова API Microsoft Graph и отобразить [список пользователей](/graph/api/user-list) в каталоге, а также как задание или служба Windows могут выполняться с удостоверением приложения, а не пользователя. 

Иллюстрацию см. в разделе [Как работает этот пример](#how-the-sample-works).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится [.NET Core 3.1](https://www.microsoft.com/net/download/dotnet-core).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Регистрация и скачивание приложения, используемого в этом кратком руководстве

> [!div renderon="docs" class="sxs-lookup"]
>
> У вас есть два варианта запуска приложения, используемого в этом кратком руководстве: оперативно (вариант 1) и вручную (вариант 2).
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Вариант 1. Регистрация и автоматическая настройка приложения, а затем скачивание примера кода
>
> 1. Откройте страницу <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs" target="_blank">регистрации приложений</a> на портале Azure и приступите к работе.
> 1. Введите имя приложения и нажмите кнопку **Зарегистрировать**.
> 1. Следуйте инструкциям, чтобы быстро скачать и автоматически настроить новое приложение.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Вариант 2. Регистрация и настройка приложения и примера кода вручную

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Шаг 1. Регистрация приложения
> Чтобы зарегистрировать приложение и добавить сведения о его регистрации в решение вручную, сделайте следующее:
>
> 1. Войдите на <a href="https://portal.azure.com/" target="_blank">портал Azure</span></a>.
> 1. Если у вас есть доступ к нескольким клиентам, в верхнем меню используйте фильтр **Каталог и подписка** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::, чтобы выбрать клиент, в котором следует зарегистрировать приложение.
> 1. Найдите и выберите **Azure Active Directory**.
> 1. В разделе **Управление** выберите **Регистрация приложений** > **Создать регистрацию**.
> 1. Введите **имя** приложения, например `Daemon-console`. Пользователи приложения могут видеть это имя. Вы можете изменить его позже.
> 1. Выберите **Зарегистрировать**, чтобы создать приложение.
> 1. В разделе **Управление** выберите **Сертификаты и секреты**.
> 1. В разделе **Секреты клиента** выберите **Новый секрет клиента**, введите имя, а затем нажмите кнопку **Добавить**. Сохраните значение секрета в надежном месте, так как это значение потребуется вам в дальнейшем.
> 1. В разделе **Управление** выберите **Разрешения API** > **Добавить разрешение**. Выберите **Microsoft Graph**.
> 1. Выберите **Разрешения приложений**.
> 1. В узле **Пользователь** выберите **User.Read.All**, а затем щелкните **Добавить разрешения**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Скачивание и настройка приложения, используемого в этом кратком руководстве
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Шаг 1. Настройка приложения на портале Azure
> Для работы примера кода, приведенного в этом кратком руководстве, необходимо создать секрет клиента и добавить разрешение приложения API Graph **User.Read.All**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Внести эти изменения для меня]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Уже настроено](media/quickstart-v2-netcore-daemon/green-check.png). Ваше приложение настроено с помощью этих атрибутов.

#### <a name="step-2-download-your-visual-studio-project"></a>Шаг 2. Скачивание проекта Visual Studio

> [!div renderon="docs"]
> [Скачайте проект Visual Studio](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)
>
> Вы можете работать с этим проектом в Visual Studio или Visual Studio для Mac.


> [!div class="sxs-lookup" renderon="portal"]
> Запустите проект с помощью Visual Studio 2019.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Скачивание примера кода](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>Шаг 3. Настройка проекта Visual Studio
>
> 1. Извлеките ZIP-файл в локальную папку, расположенную как можно ближе к корню диска (например, **C:\Azure-Samples**).
> 1. Откройте решение в Visual Studio — **1-Call-MSGraph\daemon-console.sln** (необязательно).
> 1. Измените файл **appsettings.json**, заменив значения полей `ClientId`, `Tenant` и `ClientSecret` следующими:
>
>    ```json
>    "Tenant": "Enter_the_Tenant_Id_Here",
>    "ClientId": "Enter_the_Application_Id_Here",
>    "ClientSecret": "Enter_the_Client_Secret_Here"
>    ```
>   Где:
>   - `Enter_the_Application_Id_Here` — это **идентификатор приложения (клиента)** , которое вы зарегистрировали.
>   - `Enter_the_Tenant_Id_Here` — замените это значение на **идентификатор клиента** или **имя клиента** (например, contoso.microsoft.com).
>   - `Enter_the_Client_Secret_Here` — замените это значение на секрет клиента, созданный на шаге 1.

> [!div renderon="docs"]
> > [!TIP]
> > Чтобы найти значения параметров **Идентификатор приложения (клиента)** и **Идентификатор каталога (клиента)** , на портале Azure перейдите на страницу приложения **Обзор**. Чтобы создать ключ, перейдите на страницу **Сертификаты и секреты**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Шаг 3. Согласие администратора

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Шаг 4. Согласие администратора

Если попытаться запустить приложение на этом этапе, вы получите ошибку *HTTP 403 — Forbidden* (запрещено): `Insufficient privileges to complete the operation`. Это происходит, так как *разрешение только для приложения* предоставляется с согласия администратора. Следовательно, глобальный администратор каталога должен предоставить такое согласие приложению. Выберите один из следующих вариантов с учетом своей роли:

##### <a name="global-tenant-administrator"></a>Глобальный администратор клиента

> [!div renderon="docs"]
> Если вы являетесь глобальным администратором клиента, на портале Azure выберите **Корпоративные приложения** > выберите регистрацию своего приложения > щелкните **Разрешения** в разделе "Безопасность" в области навигации слева. Нажмите большую кнопку **Предоставить согласие администратора для {Tenant Name}** (где {Tenant Name} — это название вашего каталога).

> [!div renderon="portal" class="sxs-lookup"]
> Если вы являетесь глобальным администратором, перейдите на страницу **Разрешения API** и выберите **Предоставить согласия администратора для имя_клиента**.
> > [!div id="apipermissionspage"]
> > [Переход на страницу "Разрешения API"]()

##### <a name="standard-user"></a>Обычный пользователь

Если вы являетесь обычным пользователем арендатора, попросите глобального администратора предоставить согласие администратора для вашего приложения. Чтобы сделать это, предоставьте следующий URL-адрес администратору:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Где:
>> * `Enter_the_Tenant_Id_Here` — замените это значение на **идентификатор клиента** или **имя клиента** (например, contoso.microsoft.com).
>> * `Enter_the_Application_Id_Here` — это **идентификатор приложения (клиента)** , которое вы зарегистрировали.

> [!NOTE]
> После предоставления согласия для приложения с использованием предыдущего URL-адреса может появиться ошибка *AADSTS50011: для приложения не зарегистрирован адрес ответа*. Это происходит из-за того, что у этого приложения и URL-адреса нет URI перенаправления. Игнорируйте эту ошибку.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Шаг 4. Выполнение приложения

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Шаг 5. Выполнение приложения

Если вы используете Visual Studio или Visual Studio для Mac, для запуска приложения используйте клавишу **F5**, командную строку, консоль или терминал:

```dotnetcli
cd {ProjectFolder}\1-Call-MSGraph\daemon-console
dotnet run
```

> Где:
> * *{ProjectFolder}* — это папка, куда вы извлекли ZIP-файл. Например, **C:\Azure-Samples\active-directory-dotnetcore-daemon-v2**.

В результате вы увидите список пользователей в каталоге Azure AD.

> [!IMPORTANT]
> В этом кратком руководстве приложение использует секрет клиента для собственной идентификации в качестве конфиденциального клиента. Так как секрет клиента добавляется в качестве обычного текста в файлы проекта, из соображениям безопасности рекомендуется использовать сертификат вместо секрета клиента, прежде чем использовать приложение в качестве рабочего. См. дополнительные сведения о том, как [использовать сертификат](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates) в репозитории GitHub.

## <a name="more-information"></a>Дополнительные сведения

### <a name="how-the-sample-works"></a>Как работает этот пример
![Схема работы приложения, создаваемого в этом кратком руководстве](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) — это библиотека, используемая для выполнения входа пользователей и запросов маркеров, которые нужны для доступа к API, защищенному платформой удостоверений Майкрософт. Как описано выше, в этом кратком руководстве маркеры запрашиваются с использованием собственного удостоверения приложения вместо делегированных разрешений. Поток проверки подлинности, используемый в данном случае, называется *[потоком учетных данных клиента OAuth](v2-oauth2-client-creds-grant-flow.md)* . См. подробнее об [использовании MSAL.NET с потоком учетных данных клиента](https://aka.ms/msal-net-client-credentials).

 MSAL.NET можно установить, выполнив в **консоли диспетчера пакетов** Visual Studio следующую команду.

```dotnetcli
dotnet add package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>Инициализация MSAL

Добавив следующий код, вы можете добавить ссылку на MSAL.

```csharp
using Microsoft.Identity.Client;
```

Затем выполните инициализацию MSAL с помощью следующего кода.

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientSecret(config.ClientSecret)
                                          .WithAuthority(new Uri(config.Authority))
                                          .Build();
```

> | Где: | Описание |
> |---------|---------|
> | `config.ClientSecret` | Секрет клиента, созданный для приложения на портале Azure. |
> | `config.ClientId` | **Идентификатор приложения (клиента)** , зарегистрированного на портале Azure. Это значение можно найти на странице приложения **Обзор** на портале Azure. |
> | `config.Authority`    | (Необязательно.) Конечная точка службы токенов безопасности для проверки подлинности пользователей. Обычно `https://login.microsoftonline.com/{tenant}` для общедоступного облака, где {tenant} — имя или идентификатор вашего клиента.|

Дополнительные сведения см. в [справочной документации по `ConfidentialClientApplication`](/dotnet/api/microsoft.identity.client.iconfidentialclientapplication).

### <a name="requesting-tokens"></a>Запрос маркеров

Чтобы запросить маркер с помощью удостоверение приложения, используйте метод `AcquireTokenForClient`.

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

> |Где:| Описание |
> |---------|---------|
> | `scopes` | Содержит запрошенные области. Для конфиденциальных клиентов следует использовать формат, аналогичный `{Application ID URI}/.default`, который указывает, что запрашиваемые области — это те, которые статически определены в объекте приложения, заданном на портале Azure (для Microsoft Graph `{Application ID URI}` указывает на `https://graph.microsoft.com`). Для пользовательских веб-API `{Application ID URI}` определяется в разделе **Предоставление API** в разделе регистрации приложения (предварительная версия) на портале Azure. |

Дополнительные сведения см. в [справочной документации по `AcquireTokenForClient`](/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об управляющих программах см. следующий обзор сценария:

> [!div class="nextstepaction"]
> [Создание управляющей программы, которая вызывает веб-API](scenario-daemon-overview.md)
