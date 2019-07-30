---
title: Использование управляющей программы .NET Core с платформой удостоверений Майкрософт | Azure
description: Узнайте, как процесс .NET Core может получить маркер доступа и вызвать API, защищенный конечной точкой платформы удостоверений Майкрософт, с помощью собственного удостоверения приложения.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd29c24aa0da83b67f0c0d943312adc5c2558f8f
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335577"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-console-app-using-apps-identity"></a>Краткое руководство. Получение маркера безопасности и вызов API Microsoft Graph из консольного приложения с помощью удостоверения приложения

В этом кратком руководстве вы узнаете, как создать приложение .NET Core, которое может получить маркер доступа, используя собственное удостоверение приложения, а затем вызвать API Microsoft Graph для отображения [списка пользователей](https://docs.microsoft.com/graph/api/user-list) в каталоге. Этот сценарий полезен в ситуациях, где автономное, автоматическое задание или службу Windows необходимо запустить с использованием удостоверения приложения, а не удостоверения пользователя.

![Схема работы приложения, создаваемого в этом кратком руководстве](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится [.NET Core 2.2](https://www.microsoft.com/net/download/dotnet-core/2.2).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Регистрация и скачивание приложения, используемого в этом кратком руководстве

> [!div renderon="docs" class="sxs-lookup"]
>
> У вас есть два варианта запуска приложения, используемого в этом кратком руководстве:
> * [Экспресс-способ] [Вариант 1. Регистрация и автоматическая настройка приложения, а затем скачивание примера кода](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Вручную] [Вариант 2. Регистрация и настройка приложения и примера кода вручную](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Вариант 1. Регистрация и автоматическая настройка приложения, а затем скачивание примера кода
>
> 1. Откройте на [портале Azure новую панель регистрации приложений](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs).
> 1. Введите имя приложения и нажмите кнопку **Зарегистрировать**.
> 1. Следуйте инструкциям, чтобы быстро скачать и автоматически настроить новое приложение.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Вариант 2. Регистрация и настройка приложения и примера кода вручную

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Шаг 1. Регистрация приложения
> Чтобы зарегистрировать приложение и добавить сведения о его регистрации в решение вручную, сделайте следующее:
>
> 1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
> 1. Если учетная запись предоставляет доступ нескольким клиентам, выберите свою учетную запись в правом верхнем углу и нужный клиент Azure AD для этого сеанса портала.
> 1. Перейдите на страницу [Регистрация приложений](https://go.microsoft.com/fwlink/?linkid=2083908) Платформы удостоверений Майкрософт для разработчиков.
> 1. Выберите **Новая регистрация**.
> 1. Когда откроется страница **Регистрация приложения**, введите сведения о регистрации приложения. 
> 1. В разделе **Имя** введите понятное имя приложения, которое будет отображаться пользователям приложения, например `Daemon-console`, а затем выберите **Зарегистрировать**, чтобы создать приложение.
> 1. После регистрации выберите меню **Сертификаты и секреты**.
> 1. В разделе **Секреты клиента** выберите **+ Новый секрет клиента**. Назначьте ему имя и выберите **Добавить**. Скопируйте секрет в безопасное место. Его нужно будет использовать в коде.
> 1. Теперь выберите меню **Разрешения API**, нажмите кнопку **+ Добавить разрешение** и выберите **Microsoft Graph**.
> 1. Выберите **Разрешения приложений**.
> 1. В узле **Пользователь** выберите **User.Read.All**, а затем щелкните **Добавить разрешения**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Скачивание и настройка приложения, используемого в этом кратком руководстве
> 
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Шаг 1. Настройка приложения на портале Azure
> Для работы примера кода в этом кратком руководстве необходимо создать секрет клиента и добавить разрешение приложения API Graph **User.Read.All**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Внести эти изменения для меня]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Уже настроено](media/quickstart-v2-windows-desktop/green-check.png). Ваше приложение настроено с помощью этих атрибутов.

#### <a name="step-2-download-your-visual-studio-project"></a>Шаг 2. Скачивание проекта Visual Studio

[Скачайте проект Visual Studio](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/msal3x.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Шаг 3. Настройка проекта Visual Studio

1. Извлеките ZIP-файл в локальную папку, расположенную как можно ближе к корню диска (например, **C:\Azure-Samples**).
1. Откройте решение в Visual Studio — **daemon-console.sln** (необязательно).
1. Измените файл **appsettings.json**, заменив значения полей `ClientId`, `Tenant` и `ClientSecret` следующими:

    ```json
    "Tenant": "Enter_the_Tenant_Id_Here",
    "ClientId": "Enter_the_Application_Id_Here",
    "ClientSecret": "Enter_the_Client_Secret_Here"
    ```
    > > [!div renderon="portal" id="certandsecretspage" class="sxs-lookup"]
    > > [Создание секрета клиента]()
    
    > [!div class="sxs-lookup" renderon="portal"]
    > > [!NOTE]
    > > В этом кратком руководстве поддерживается Enter_the_Supported_Account_Info_Here.
    
    > [!div renderon="docs"]
    >> Описание
    >> * `Enter_the_Application_Id_Here` — это **идентификатор приложения (клиента)** , которое вы зарегистрировали.
    >> * `Enter_the_Tenant_Id_Here` — замените это значение на **идентификатор клиента** или **имя клиента** (например, contoso.microsoft.com).
    >> * `Enter_the_Client_Secret_Here` — замените это значение на секрет клиента, созданный на шаге 1.

    > [!div renderon="docs"]
    > > [!TIP]
    > > Чтобы найти значения параметров **Идентификатор приложения (клиента)** и **Идентификатор каталога (клиента)** , на портале Azure перейдите на страницу приложения **Обзор**. Чтобы создать ключ, перейдите на страницу **Сертификаты и секреты**.
    
#### <a name="step-4-admin-consent"></a>Шаг 4. Согласие администратора

Если попытаться запустить приложение на этом этапе, вы получите ошибку *HTTP 403 — Forbidden* (запрещено): `Insufficient privileges to complete the operation`. Это происходит, так как *разрешение только для приложения* предоставляется с согласия администратора. Следовательно, глобальный администратор каталога должен предоставить такое согласие приложению. Выберите один из следующих вариантов с учетом своей роли:

##### <a name="global-tenant-administrator"></a>Глобальный администратор клиента

> [!div renderon="docs"]
> Если вы являетесь глобальным администратором клиента, перейдите на страницу **Разрешения API** в разделе регистрации приложения (предварительная версия) на портале Azure и выберите **Предоставить согласие администратора для {имя каталога}** .

> [!div renderon="portal" class="sxs-lookup"]
> Если вы являетесь глобальным администратором, перейдите на страницу **Разрешения API** и выберите **Предоставить согласия администратора для имя_клиента**.
> > [!div id="apipermissionspage"]
> > [Переход на страницу "Разрешения API"]()

##### <a name="standard-user"></a>Обычный пользователь

Если вы являетесь обычным пользователем клиента, попросите глобального администратора предоставить согласие администратора для вашего приложения. Чтобы сделать это, предоставьте следующий URL-адрес администратору:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Описание
>> * `Enter_the_Tenant_Id_Here` — замените это значение на **идентификатор клиента** или **имя клиента** (например, contoso.microsoft.com).
>> * `Enter_the_Application_Id_Here` — это **идентификатор приложения (клиента)** , которое вы зарегистрировали.

> [!NOTE]
> После предоставления согласия для приложения с использованием предыдущего URL-адреса может появиться ошибка *AADSTS50011: для приложения не зарегистрирован адрес ответа*. Это происходит из-за того, что у этого приложения и URL-адреса нет URI перенаправления. Игнорируйте эту ошибку.

#### <a name="step-5-run-the-application"></a>Шаг 5. Выполнение приложения

Если вы используете Visual Studio, для запуска приложения используйте клавишу **F5** или командную строку либо консоль.

```console
cd {ProjectFolder}\daemon-console
dotnet run
```

> Описание
> * *{ProjectFolder}* — это папка, куда вы извлекли ZIP-файл. Например, **C:\Azure-Samples\active-directory-dotnetcore-daemon-v2**.

В результате вы увидите список пользователей в каталоге Azure AD.

> [!IMPORTANT]
> В этом кратком руководстве приложение использует секрет клиента для собственной идентификации в качестве конфиденциального клиента. Так как секрет клиента добавляется в качестве обычного текста в файлы проекта, из соображениям безопасности рекомендуется использовать сертификат вместо секрета клиента, прежде чем использовать приложение в качестве рабочего. См. дополнительные сведения о том, как [использовать сертификат](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates) в репозитории GitHub.

## <a name="more-information"></a>Дополнительные сведения

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) — это библиотека, используемая для выполнения входа пользователей и запросов маркеров, которые нужны для доступа к API, защищенному платформой удостоверений Майкрософт. Как описано выше, в этом кратком руководстве маркеры запрашиваются с использованием собственного удостоверения приложения вместо делегированных разрешений. Поток проверки подлинности, используемый в данном случае, называется *[потоком учетных данных клиента OAuth](v2-oauth2-client-creds-grant-flow.md)* . См. подробнее об [использовании MSAL.NET с потоком учетных данных клиента](https://aka.ms/msal-net-client-credentials).

 MSAL.NET можно установить, выполнив в **консоли диспетчера пакетов** Visual Studio следующую команду.

```powershell
Install-Package Microsoft.Identity.Client -Pre
```

Кроме того, если вы не используете Visual Studio, выполните следующую команду, чтобы добавить MSAL в проект:

```console
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
);
```

> | Описание ||
> |---------|---------|
> | `config.ClientSecret` | Секрет клиента, созданный для приложения на портале Azure. |
> | `config.ClientId` | **Идентификатор приложения (клиента)** , зарегистрированного на портале Azure. Это значение можно найти на странице приложения **Обзор** на портале Azure. |
> | `config.Authority`    | (Необязательно.) Конечная точка службы токенов безопасности для проверки подлинности пользователей. Обычно <https://login.microsoftonline.com/{tenant}> для общедоступного облака, где {tenant} — имя или идентификатор вашего клиента.|

Дополнительные сведения см. в [справочной документации по `ConfidentialClientApplication`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.iconfidentialclientapplication?view=azure-dotnet).

### <a name="requesting-tokens"></a>Запрос маркеров

Чтобы запросить маркер с помощью удостоверение приложения, используйте метод `AcquireTokenForClient`.

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

> |Описание| |
> |---------|---------|
> | `scopes` | Содержит запрошенные области. Для конфиденциальных клиентов следует использовать формат, аналогичный `{Application ID URI}/.default`, который указывает, что запрашиваемые области — это те, которые статически определены в объекте приложения, заданном на портале Azure (для Microsoft Graph `{Application ID URI}` указывает на `https://graph.microsoft.com`). Для пользовательских веб-API `{Application ID URI}` определяется в разделе **Предоставление API** в разделе регистрации приложения (предварительная версия) на портале Azure. |

Дополнительные сведения см. в [справочной документации по `AcquireTokenForClient`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient?view=azure-dotnet#Microsoft_Identity_Client_ConfidentialClientApplication_AcquireTokenForClientAsync_System_Collections_Generic_IEnumerable_System_String__).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Пример управляющей программы .NET Core](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)

Дополнительные сведения о разрешениях и согласии:

> [!div class="nextstepaction"]
> [Разрешения и согласие](v2-permissions-and-consent.md)

Чтобы узнать больше о потоке проверки подлинности для этого сценария, ознакомьтесь с потоком учетных данных клиента OAuth 2.0:

> [!div class="nextstepaction"]
> [Поток учетных данных клиента OAuth](v2-oauth2-client-creds-grant-flow.md)

> [!div class="nextstepaction"]
> [Потоки учетных данных клиента с использованием MSAL.NET](https://aka.ms/msal-net-client-credentials)

Помогите нам улучшить платформу Microsoft Identity. Поделитесь своим мнением, ответив на два вопроса.

> [!div class="nextstepaction"]
> [Опрос по платформе удостоверений Майкрософт](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)