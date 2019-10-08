---
title: Учебник. Предоставление доступа к веб-API ASP.NET Core из одностраничного приложения. Azure Active Directory B2C
description: Узнайте, как использовать Active Directory B2C для защиты веб-API .NET Core и вызова API из одностраничного приложения Node.js.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/24/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 60fe9569b0e6e92ae161271439ecbf1b04788ed4
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71694582"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Руководство по Предоставление доступа к веб-API ASP.NET Core из одностраничного приложения с помощью Azure Active Directory B2C

В этом руководстве показано, как вызывать защищенный с помощью Azure Active Directory (Azure AD) B2C ресурс веб-API ASP.NET Core из одностраничного приложения.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Добавление приложения веб-API
> * Настройка областей для веб-API.
> * Предоставление разрешения на использование веб-API.
> * Настройка примера для использования приложения.

## <a name="prerequisites"></a>Предварительные требования

* Выполните действия и необходимые условия в статье [Руководство. Включение в одностраничном приложении аутентификации с помощью Azure Active Directory B2C](active-directory-b2c-tutorials-spa.md).
* Visual Studio 2019 или более поздней версии либо Visual Studio Code
* .NET Core 2.2 или более поздней версии
* Node.js

## <a name="add-a-web-api-application"></a>Добавление приложения веб-API

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Настройка областей

Области предоставляют способ контроля доступа к защищенным ресурсам. Области используются веб-API для реализации управления доступом на уровне области. Например, некоторые пользователи могут иметь доступ на чтение и запись, тогда как другие пользователи могут иметь разрешения только на чтение. В этом учебнике вы определяете разрешения на чтение и запись для веб-API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Запишите **значение полной области**, которое будет использоваться в области `demo.read` на следующих шагах при настройке одностраничного приложения. Значение полной области аналогично `https://yourtenant.onmicrosoft.com/api/demo.read`.

## <a name="grant-permissions"></a>Предоставление разрешений

Чтобы вызвать защищенный веб-API из другого приложения, необходимо предоставить приложению разрешения на доступ к веб-API.

Выполняя предварительные требования, вы создали веб-приложение *webapp1*. В этом учебнике вы настроите это приложение для вызова веб-API *webapi1*, созданного в предыдущем разделе.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Одностраничное веб-приложение зарегистрировано для вызова защищенного веб-API. Пользователь выполняет проверку подлинности в Azure AD B2C для использования одностраничного приложения. Одностраничное приложение получает предоставление авторизации из Azure AD B2C для доступа к защищенному веб-API.

## <a name="configure-the-sample"></a>Настройка примера

Теперь, когда веб-API зарегистрирован и определены области, необходимо настроить код веб-API для использования клиента Azure AD B2C. В рамках этого учебника вы настроите пример веб-приложения .NET Core, который можно скачать из репозитория GitHub.

[Скачайте \*архив ZIP](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) или клонируйте пример проекта веб-API с GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Настройка веб-API

1. Откройте файл *B2C-WebApi/ **appsettings.json*** в Visual Studio или Visual Studio Code.
1. Измените блок `AzureAdB2C`, чтобы он отражал имя клиента, идентификатор приложения веб-API, имя политики входа или регистрации, а также определенные ранее области. Блок должен выглядеть следующим образом (с соответствующими значениями `Tenant` и `ClientId`):

    ```json
    "AzureAdB2C": {
      "Tenant": "<your-tenant-name>.onmicrosoft.com",
      "ClientId": "<webapi-application-ID>",
      "Policy": "B2C_1_signupsignin1",

      "ScopeRead": "demo.read",
      "ScopeWrite": "demo.write"
    },
    ```

#### <a name="enable-cors"></a>Включение CORS

Чтобы разрешить одностраничному приложению вызывать веб-API ASP.NET Core, необходимо включить [CORS](https://docs.microsoft.com/aspnet/core/security/cors) в веб-API.

1. В файле *Startup.cs* добавьте CORS в метод `ConfigureServices()`.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddCors();
    ```

1. Кроме того, в методе `ConfigureServices()` задайте значение `jwtOptions.Authority` для следующего URI поставщика токенов.

    Замените `<your-tenant-name>` именем клиента B2C.

    ```csharp
    jwtOptions.Authority = $"https://<your-tenant-name>.b2clogin.com/{Configuration["AzureAdB2C:Tenant"]}/{Configuration["AzureAdB2C:Policy"]}/v2.0";
    ```

1. Настройте CORS в методе `Configure()`.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        app.UseCors(builder =>
            builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

1. (Только Visual Studio) В обозревателе решений в разделе **Свойства** откройте файл *launchSettings.json* и найдите блок `iisExpress`.
1. (Только Visual Studio) Замените значение `applicationURL` номером порта, указанным при регистрации приложения *webapi1* на предыдущем шаге. Например:

    ```json
    "iisExpress": {
      "applicationUrl": "http://localhost:5000/",
      "sslPort": 0
    }
    ```

### <a name="configure-the-single-page-application"></a>Настройка одностраничного приложения

Одностраничное приложение (SPA) из [предыдущего учебника](active-directory-b2c-tutorials-spa.md) этой серии использует Azure AD B2C для регистрации и входа пользователя, а также вызывает веб-API ASP.NET Core, защищенный демонстрационным клиентом *frabrikamb2c*.

В этом разделе вы обновите одностраничное приложение для вызова веб-API ASP.NET Core, защищенного *вашим* клиентом Azure AD B2C и запущенного на локальном компьютере.

Чтобы изменить параметры в SPA, выполните приведенные ниже действия.

1. Откройте файл *index.html* в проекте [active-directory-b2c-javascript-msal-singlepageapp][github-js-spa], скачанном или клонированном в предыдущем учебнике.
1. Настройте пример с помощью URI для области *demo.read*, созданной ранее, а также URL-адрес веб-API.
    1. В определении `appConfig` замените значение `b2cScopes` полным URI для области (**значение полной области**, записанное ранее).
    1. Замените значение `webApi` значением `applicationURL`, указанным в предыдущем разделе.

    Определение `appConfig` должно выглядеть как следующий блок кода (с именем клиента вместо `<your-tenant-name>`):

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var appConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/"
    };
    ```

## <a name="run-the-spa-and-web-api"></a>Запуск SPA и веб-API

Наконец, вы запускаете веб-API ASP.NET Core и одностраничное приложение Node.js на локальном компьютере. Затем войдите в одностраничное приложение и нажмите кнопку, чтобы инициировать запрос к защищенному API.

Хотя в этом учебнике оба приложения запущены локально, они используют Azure AD B2C для безопасной регистрации и входа, а также для предоставления доступа к защищенному веб-API.

### <a name="run-the-aspnet-core-web-api"></a>Запуск веб-API ASP.NET Core

Нажмите клавишу **F5** для создания и отладки решения *B2C-WebAPI.sln* в Visual Studio. При запуске проекта в браузере, используемом по умолчанию, откроется веб-страница с объявлением о том, что веб-API доступен для запросов.

Если вы предпочитаете использовать CLI `dotnet` вместо Visual Studio, выполните приведенные ниже действия.

1. Откройте окно консоли и перейдите в каталог, содержащий файл *\*.csproj*. Например:

    `cd active-directory-b2c-dotnetcore-webapi/B2C-WebApi`

1. Создайте и запустите веб-API, выполнив `dotnet run`.

    Когда API запущен и работает, должен появиться результат, аналогичный следующему (для данного учебника любые предупреждения `NETSDK1059` можно игнорировать):

    ```console
    $ dotnet run
    Hosting environment: Production
    Content root path: /home/user/active-directory-b2c-dotnetcore-webapi/B2C-WebApi
    Now listening on: http://localhost:5000
    Application started. Press Ctrl+C to shut down.
    ```

### <a name="run-the-single-page-app"></a>Запуск одностраничного приложения

1. Откройте окно консоли и перейдите в каталог, содержащий пример Node.js. Например:

    `cd active-directory-b2c-javascript-msal-singlepageapp`

1. Выполните следующие команды:

    ```console
    npm install && npm update
    node server.js
    ```

    В окне консоли отобразится номер порта, связанный с размещаемым приложением.

    ```console
    Listening on port 6420...
    ```

1. Перейдите к `http://localhost:6420` в браузере для просмотра приложения.
1. Выполните вход с помощью адреса электронной почты и пароля, которые вы использовали в [предыдущем учебнике](active-directory-b2c-tutorials-spa.md). После успешного входа отобразится сообщение `User 'Your Username' logged-in`.
1. Нажмите кнопку **Call Web API** (Вызов веб-API). SPA получает предоставление авторизации из Azure AD B2C, а затем — доступ к защищенному веб-API для отображения содержимого своей страницы индексов:

    ```Output
    Web APi returned:
    "<html>\r\n<head>\r\n  <title>Azure AD B2C API Sample</title>\r\n ...
    ```

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Добавление приложения веб-API
> * Настройка областей для веб-API.
> * Предоставление разрешения на использование веб-API.
> * настройка примера для использования приложения;

Теперь, когда вы узнали, как SPA выполняет запрос ресурса из защищенного веб-API, вы можете получить более глубокое представление о том, как эти типы приложений взаимодействуют друг с другом и с Azure AD B2C.

> [!div class="nextstepaction"]
> [Application types that can be used in Active Directory B2C](active-directory-b2c-apps.md) (Типы приложений, используемые в Azure Active Directory B2C)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
