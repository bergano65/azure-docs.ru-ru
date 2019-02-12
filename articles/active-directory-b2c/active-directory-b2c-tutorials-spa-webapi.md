---
title: Руководство. Предоставление доступа к веб-API ASP.NET Core из одностраничного приложения. Azure Active Directory B2C | Документация Майкрософт
description: Руководство по использованию Active Directory B2C для защиты веб-API .NET Core и его вызова из одностраничного приложения.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 13cbf1e81e0d203c181efb0881ec2a437cbaef24
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55752183"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Руководство. Предоставление доступа к веб-API ASP.NET Core из одностраничного приложения с помощью Azure Active Directory B2C

В этом руководстве показано, как вызывать защищенный с помощью Azure Active Directory (Azure AD) B2C ресурс веб-API ASP.NET Core из одностраничного приложения.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Добавление приложения веб-API
> * Настройка областей для веб-API.
> * Предоставление разрешения на использование веб-API.
> * Настройка примера для использования приложения.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

Выполните действия и необходимые условия в статье [Руководство. Включение в одностраничном приложении аутентификации на основе учетных записей с помощью Azure Active Directory B2C](active-directory-b2c-tutorials-spa.md).

## <a name="add-a-web-api-application"></a>Добавление приложения веб-API

Ресурсы веб-API необходимо зарегистрировать в клиенте, чтобы они могли принимать запросы защищенных ресурсов от клиентских приложений, которые представляют токен доступа, и отвечать на них.

1. Войдите на [портале Azure](https://portal.azure.com).
2. Убедитесь, что используете каталог, содержащий клиент Azure AD B2C, щелкнув **Фильтр каталога и подписки** в верхнем меню и выбрав каталог, содержащий ваш клиент.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
4. Щелкните **Приложения**, а затем выберите **Добавить**.
5. Введите имя приложения. Например, *webapi1*.
6. Для поля **Включить веб-приложение или веб-API** и **Разрешить неявный поток** выберите **Да**.
7. Для **URL-адреса ответа** введите конечные точки, куда Azure AD B2C возвращает все маркеры, запрашиваемые вашим приложением. В этом руководстве пример выполняется локально и ожидает передачи данных по адресу `https://localhost:5000`.
8. Для поля **URI идентификатора приложения** выберите идентификатор, используемый для веб-API. Полный URI код с доменом создается автоматически. Например, `https://contosotenant.onmicrosoft.com/api`.
9. Нажмите кнопку **Создать**.
10. На странице свойств запишите идентификатор приложения, который будет использоваться при настройке веб-приложения.

## <a name="configure-scopes"></a>Настройка областей

Области предоставляют способ контроля доступа к защищенным ресурсам. Области используются веб-API для реализации управления доступом на уровне области. Например, некоторые пользователи могут иметь доступ на чтение и запись, тогда как другие пользователи могут иметь разрешения только на чтение. В этом руководстве вы определяете разрешения на чтение для веб-API.

1. Щелкните **Приложения**, а затем выберите *webapi1*.
2. Выберите **Опубликованные области**.
3. Для **области** введите `Hello.Read`, а для описания — `Read access to hello`.
4. Для **области** введите `Hello.Write`, а для описания — `Write access to hello`.
5. Выберите команду **Сохранить**.

Опубликованные области можно использовать для предоставления приложению клиента разрешения на доступ к веб-API.

## <a name="grant-permissions"></a>Предоставление разрешений

Чтобы вызвать защищенный веб-API из приложения, необходимо предоставить приложению разрешения на доступ к API. Как часть предварительного требования, вы создали веб-приложение *webapp1* в Azure AD B2C, следуя руководству. Используйте это приложение для вызова веб-API.

1. Щелкните **Приложения**, а затем выберите *webapp1*.
2. Щелкните **Доступ к API**, а затем выберите **Добавить**.
3. В раскрывающемся списке **Выбрать API** выберите *webapi1*.
4. В раскрывающемся списке **Выбрать области** выберите области **Hello.Read** и **Hello.Write**, определенные ранее.
5. Последовательно выберите **ОК**.

Приложение **My sample single page app** зарегистрировано для вызова защищенного **Hello Core API**. Пользователь выполняет аутентификацию в Azure AD B2C для использования одностраничного приложения. Одностраничное приложение получает предоставление авторизации из Azure AD B2C для доступа к защищенному веб-API.

## <a name="configure-the-sample"></a>Настройка примера

Теперь, когда веб-API зарегистрирован и определены области, необходимо настроить код веб-API для использования клиента Azure AD B2C. С помощью этого руководства вы настроите пример веб-приложения .NET Core, который можно скачать из репозитория GitHub. [Загрузите ZIP-файл](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) или клонируйте пример приложения с GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Настройка веб-API

1. Откройте решение **B2C-WebAPI.sln** в Visual Studio.
2. Откройте файл **appsettings.json**. Обновите следующие значения, чтобы настроить веб-API для использования со своим клиентом:

    ```javascript
    "AzureAdB2C": 
      {
        "Tenant": "<your tenant name>.onmicrosoft.com", 
        "ClientId": "<application-ID>",
        "Policy": "B2C_1_signupsignin1>",
        "ScopeRead": "Hello.Read"  
      },
    ```

#### <a name="enable-cors"></a>Включение CORS

Чтобы разрешить одностраничному приложению вызывать веб-API ASP.NET Core, необходимо включить [CORS](https://docs.microsoft.com/aspnet/core/security/cors).

1. В файле **Startup.cs** добавьте CORS в метод `ConfigureServices()`.

    ```C#
    public void ConfigureServices(IServiceCollection services) {
      services.AddCors();
    ```

2. В файле **Startup.cs** настройте CORS в методе `Configure()`.

    ```C#
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory) {
      app.UseCors(builder =>
        builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

3. Откройте файл **launchSettings.json** в разделе **Свойства**, найдите параметр **iisSettings** *applicationURL* и задайте номер порта, зарегистрированный для URL-адреса ответа API `http://localhost:5000`.

### <a name="configure-the-single-page-application"></a>Настройка одностраничного приложения

Одностраничное приложение использует Azure AD B2C для регистрации и входа пользователей, а также вызывает защищенный веб-API ASP.NET Core. Для вызова веб-API .NET Core нужно внести изменения в одностраничное приложение.

Чтобы изменить параметры приложения:

1. Откройте файл `index.html` .
2. Настройте пример, указав сведения о регистрации клиента Azure AD B2C. В следующем коде добавьте имя клиента в **b2cScopes** и измените значение **webApi** на ранее сохраненное значение *applicationURL*:

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var applicationConfig = {
        clientID: '<application-ID>',
        authority: "https://<your-tenant-name>.b2clogin.com/tfp/<your-tenant-name>.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://<Your tenant name>.onmicrosoft.com/api/Hello.Read"],
        webApi: 'http://localhost:5000/api/values',
    };
    ```

## <a name="run-the-spa-application-and-web-api"></a>Запуск одностраничного приложения и веб-API

Теперь нужно запустить одностраничное приложение Node.js и веб-API .NET Core.

### <a name="run-the-aspnet-core-web-api"></a>Запуск веб-API ASP.NET Core 

Нажмите клавишу **F5** для отладки решения **B2C-WebAPI.sln** в Visual Studio.

При запуске проекта в браузере, используемом по умолчанию, откроется веб-страница с объявлением о том, что веб-API доступен для запросов.

### <a name="run-the-single-page-app"></a>Запуск одностраничного приложения

1. Откройте командную строку Node.js.
2. Перейдите в каталог, содержащий пример приложения Node.js. Например `cd c:\active-directory-b2c-javascript-msal-singlepageapp`
3. Выполните следующие команды:
    ```
    npm install && npm update
    node server.js
    ```

    В окне консоли отобразится номер порта, связанный с размещаемым приложением.
    
    ```
    Listening on port 6420...
    ```

4. С помощью браузера перейдите по адресу `http://localhost:6420`, чтобы просмотреть приложение.
5. Войдите с помощью адреса электронной почты и пароля, которые использовались при работе с руководством по [проверке подлинности пользователей с помощью Azure Active Directory B2C в одностраничном приложении (JavaScript)](active-directory-b2c-tutorials-spa.md).
6. Щелкните **Call API** (Вызов API).

После регистрации или входа с учетной записью пользователя пример приложения вызовет защищенный веб-API и вернет результат.

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Добавление приложения веб-API
> * Настройка областей для веб-API.
> * Предоставление разрешения на использование веб-API.
> * Настройка примера для использования приложения.

> [!div class="nextstepaction"]
> [Руководство по добавлению поставщиков удостоверений для приложений в Azure Active Directory B2C](tutorial-add-identity-providers.md)
