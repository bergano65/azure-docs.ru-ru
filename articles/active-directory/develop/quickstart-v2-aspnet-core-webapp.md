---
title: Краткое руководство. Добавление возможности входа в веб-приложение ASP.NET Core с помощью учетной записи Майкрософт | Azure
titleSuffix: Microsoft identity platform
description: Из этого краткого руководства вы узнаете, как реализовать вход с помощью учетной записи Майкрософт в веб-приложении ASP.NET Core с помощью OpenID Connect.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/11/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 98d2b4ed4b0d3cef2cde156dc05ebb314edff365
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94592266"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Краткое руководство. Добавление возможности входа в веб-приложение ASP.NET Core с помощью учетной записи Майкрософт

Из этого краткого руководства вы узнаете, как скачать и выполнить пример кода, в котором показана реализация входа в веб-приложение ASP.NET Core для пользователей из любой организации Azure Active Directory (Azure AD).  

Иллюстрацию см. в разделе [Как работает этот пример](#how-the-sample-works).

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Предварительные требования
>
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) или [Visual Studio Code](https://code.visualstudio.com/)
> * [Пакет SDK для .NET Core 3.1 +](https://dotnet.microsoft.com/download)
>
> ## <a name="register-and-download-your-quickstart-app"></a>Регистрация и скачивание приложения, используемого в этом кратком руководстве
> У вас есть два варианта запуска приложения, используемого в этом кратком руководстве:
> * [Экспресс-способ] [Вариант 1. Регистрация и автоматическая настройка приложения, а затем скачивание примера кода](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Вручную] [Вариант 2. Регистрация и настройка приложения и примера кода вручную](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Вариант 1. Регистрация и автоматическая настройка приложения, а затем скачивание примера кода
>
> 1. Откройте [Регистрация приложений](https://aka.ms/aspnetcore2-1-aad-quickstart-v2) на портале Azure.
> 1. Введите имя приложения и нажмите кнопку **Зарегистрировать**.
> 1. Следуйте инструкциям для загрузки и автоматической настройки нового приложения одним щелчком мыши.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Вариант 2. Регистрация и настройка приложения и примера кода вручную
>
> #### <a name="step-1-register-your-application"></a>Шаг 1. Регистрация приложения
> Чтобы зарегистрировать приложение и добавить сведения о его регистрации в решение вручную, сделайте следующее:
>
> 1. Войдите на [портал Azure](https://portal.azure.com).
> 1. Если у вас есть доступ к нескольким клиентам, в верхнем меню используйте фильтр **Каталог и подписка** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::, чтобы выбрать клиент, в котором следует зарегистрировать приложение.
> 1. Найдите и выберите **Azure Active Directory**.
> 1. В разделе **Управление** щелкните **Регистрация приложений** и выберите пункт **Новая регистрация**.
> 1. Введите **имя** приложения, например `AspNetCore-Quickstart`. Пользователи приложения могут видеть это имя. Вы можете изменить его позже.
> 1. Введите **URI перенаправления** `https://localhost:44321/`
> 1. Выберите **Зарегистрировать**.
> 1. В разделе **Управление** выберите **Проверка подлинности**.
> 1. В разделе **URI перенаправления** выберите **Добавить URI** и введите `https://localhost:44321/signin-oidc`
> 1. Введите **URL-адрес выхода** `https://localhost:44321/signout-oidc`
> 1. В разделе **Неявное предоставление разрешения** установите флажок **Токены идентификатора**.
> 1. Щелкните **Сохранить**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Шаг 1. Настройка приложения на портале Azure
> Чтобы пример кода для этого краткого руководства работал, необходимо добавить URL-адреса ответа `https://localhost:44321/` и `https://localhost:44321/signin-oidc`, URL-адрес выхода `https://localhost:44321/signout-oidc` и запросить выдачу токенов идентификации конечной точкой авторизации.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Внести это изменение для меня]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Уже настроено](media/quickstart-v2-aspnet-webapp/green-check.png). Ваше приложение настроено с помощью этих атрибутов.

#### <a name="step-2-download-your-aspnet-core-project"></a>Шаг 2. Скачивание проекта ASP.NET Core

> [!div renderon="docs"]
> [Скачивание решения ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Запустите проект.

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Скачивание примера кода](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Шаг 3. Приложение настроено и готово к запуску
> Мы уже настроили для проекта нужные значения свойств приложения, и его можно запускать.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-configure-your-aspnet-core-project"></a>Шаг 3. Настройка проекта ASP.NET Core
> 1. Извлеките ZIP-архив в локальную папку рядом с корнем диска. Например, в папку *C:\Azure-Samples*.
> 1. Откройте решение в Visual Studio 2019.
> 1. Откройте файл *appsettings.json* и измените следующие значения:
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "common",
>    ```
>
>    - Замените `Enter_the_Application_Id_here`  **идентификатором приложения (клиента)** , зарегистрированного на портале Azure. **Идентификатор приложения (клиента)** можно найти на странице **Обзор** приложения.
>    - Замените `common` одним из следующих элементов:
>       - Если приложение поддерживает вариант **Учетные записи только в этом каталоге организации**, замените это значение **идентификатором каталога (клиента)** (GUID) или **именем клиента** (например, `contoso.onmicrosoft.com`). Вы можете найти **идентификатор каталога (клиента)** на странице **Обзор** приложения.
>       - Если ваше приложение поддерживает вариант **Учетные записи в любом каталоге организации**, замените это значение на `organizations`.
>       - Если приложение поддерживает вариант **Все пользователи с учетными записями Майкрософт**, оставьте это значение как `common`
>
> Для целей этого руководства изменение других значений в файле *appsettings.json* не требуется.
>
> #### <a name="step-4-build-and-run-the-application"></a>Шаг 4. Сборка и запуск приложения
>
> Выполните сборку и запустите приложение в Visual Studio, выбрав меню **Отладка** > **Начать отладку** или нажав кнопку `F5`.
>
> Вы увидите запрос на ввод учетных данных, а затем запрос на предоставление согласия на разрешения, необходимые вашему приложению. Выберите **Принять** в запросе на согласие.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-01-consent.png" alt-text="Диалоговое окно согласия с разрешениями, запрашиваемыми приложением от > пользователя":::
>
> После предоставления согласия на запрошенные разрешения в приложении отобразится оповещение об успешном входе с использованием учетных данных Azure Active Directory.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-02-signed-in.png" alt-text="Веб-браузер, отображающий работающее веб-приложение и вошедшего в него пользователя":::

## <a name="more-information"></a>Дополнительные сведения

В этом разделе представлен код, используемый для выполнения входа пользователей. Это может быть полезно для рассмотрения принципов работы кода и основных аргументов. Также вы поймете, нужно ли добавлять функцию входа в существующее приложение ASP.NET Core.

### <a name="how-the-sample-works"></a>Как работает этот пример
![Схема работы приложения, создаваемого в этом кратком руководстве](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Класс Startup

ПО промежуточного слоя *Microsoft.AspNetCore.Authentication* использует класс `Startup`, который выполняется при инициализации хост-процесса:

```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"));

      services.AddControllersWithViews(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
          options.Filters.Add(new AuthorizeFilter(policy));
      });
      services.AddRazorPages()
          .AddMicrosoftIdentityUI();
  }
```

Метод `AddAuthentication()` настраивает службу для добавления аутентификации на основе файлов cookie, которая используется при работе с браузером, и определения запроса для OpenID Connect.

Строка, содержащая `.AddMicrosoftIdentityWebApp`, добавляет в ваше приложение функцию аутентификации платформы удостоверений Майкрософт. Затем она настраивается для входа с использованием конечной точки платформы удостоверений Майкрософт на основе сведений в разделе `AzureAD` файла конфигурации *appsettings.json*:

| Ключ *appsettings.json* | Описание                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | **Идентификатор приложения (клиента)** , зарегистрированного на портале Azure.                                                                                       |
| `Instance`             | Конечная точка службы токенов безопасности для проверки подлинности пользователей. Обычно это значение `https://login.microsoftonline.com/`, указывающее на общедоступное облако Azure. |
| `TenantId`             | Имя клиента, идентификатор клиента (GUID) или *общие* пользователи с рабочими или учебными учетными записями или личными учетными записями Майкрософт.                             |

Метод `Configure()` содержит два важных метода: `app.UseAuthentication()` и `app.UseAuthorization()`, которые обеспечивают упомянутые функциональные возможности. Кроме того, в методе `Configure()` необходимо зарегистрировать маршруты Microsoft Identity Web по крайней мере с одним вызовом `endpoints.MapControllerRoute()` или вызовом `endpoints.MapControllers()`.

```csharp
app.UseAuthentication();
app.UseAuthorization();

app.UseEndpoints(endpoints =>
{

    endpoints.MapControllerRoute(
        name: "default",
        pattern: "{controller=Home}/{action=Index}/{id?}");
    endpoints.MapRazorPages();
});

// endpoints.MapControllers(); // REQUIRED if MapControllerRoute() isn't called.
```

### <a name="protect-a-controller-or-a-controllers-method"></a>Защита контроллера или метода контроллера

Контроллер или его методы можно защитить с помощью атрибута `[Authorize]`. Этот атрибут разрешает доступ к контроллеру или методам только аутентифицированным пользователям. Это значит, что для доступа к контроллеру можно создавать запрос на аутентификацию, если пользователь не прошел аутентификацию.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Дальнейшие действия

В репозитории GitHub, содержащем это руководство ASP.NET Core, есть также инструкции и дополнительные примеры кода, демонстрирующие выполнение следующих действий.

- Добавление проверки подлинности в новое веб-приложение ASP.NET Core
- Вызов Microsoft Graph, других API Майкрософт или собственных веб-API
- Добавление авторизации
- Вход пользователей в национальные облака или вход с помощью удостоверений социальных сетей

> [!div class="nextstepaction"]
> [Учебники по веб-приложениям ASP.NET Core в GitHub](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
