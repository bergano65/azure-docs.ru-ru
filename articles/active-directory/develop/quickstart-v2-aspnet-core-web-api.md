---
title: Краткое руководство. Защита веб-API ASP.NET Core с помощью платформы удостоверений Майкрософт | Azure
titleSuffix: Microsoft identity platform
description: В этом кратком руководстве описаны загрузка и изменение примера кода, который демонстрирует, как защитить веб-API ASP.NET Core с помощью платформы удостоверений Майкрософт для авторизации.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: dc0cdca2355403bc8f5409d9a6ca7f4ae89caf25
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "90945593"
---
# <a name="quickstart-protect-an-aspnet-core-web-api-with-microsoft-identity-platform"></a>Краткое руководство. Защита веб-API ASP.NET Core с помощью платформы удостоверений Майкрософт

В этом кратком руководстве описано, как использовать пример кода, чтобы защитить веб-API ASP.NET Core, чтобы доступ к нему получали только полномочные пользователи. Учетные записи могут быть личными (hotmail.com, outlook.com и т. д.), а также рабочими и учебными учетными записями в любом экземпляре Azure Active Directory (Azure AD).

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Предварительные требования
>
> - Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно.
> - [Клиент Azure Active Directory](quickstart-create-new-tenant.md)
> - [Пакет SDK для .NET Core 3.1 +](https://dotnet.microsoft.com/)
> - [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) или [Visual Studio Code](https://code.visualstudio.com/)
>
> ## <a name="step-1-register-the-application"></a>Шаг 1. Регистрация приложения
>
> Для начала зарегистрируйте веб-API в клиенте Azure AD и добавьте область, выполнив следующие действия:
>
> 1. Войдите на [портал Azure](https://portal.azure.com).
> 1. Если у вас есть доступ к нескольким клиентам, в верхнем меню используйте фильтр **Каталог и подписка** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::, чтобы выбрать клиент, в котором следует зарегистрировать приложение.
> 1. Найдите и выберите **Azure Active Directory**.
> 1. В разделе **Управление** щелкните **Регистрация приложений** и выберите пункт **Новая регистрация**.
> 1. Введите **имя** приложения, например `AspNetCoreWebApi-Quickstart`. Пользователи приложения могут видеть это имя. Вы можете изменить его позже.
> 1. Выберите **Зарегистрировать**.
> 1. В разделе **Управление** выберите **Предоставление API**
> 1. Выберите **Добавить область** и щелкните **Сохранить и продолжить**, чтобы принять **URI идентификатора приложения по умолчанию**.
> 1. В области **Добавить область** выберите следующие значения:
>    - **Имя области**: `access_as_user`
>    - **Кто может давать согласие?** : **Администраторы и пользователи**
>    - **Отображаемое имя согласия администратора**: `Access AspNetCoreWebApi-Quickstart`
>    - **Описание согласия администратора**: `Allows the app to access AspNetCoreWebApi-Quickstart as the signed-in user.`
>    - **Отображаемое имя согласия пользователя**: `Access AspNetCoreWebApi-Quickstart`
>    - **Описание согласия пользователя**: `Allow the application to access AspNetCoreWebApi-Quickstart on your behalf.`
>    - **Состояние**. **Enabled**
> 1. Выберите **Добавить область**, чтобы завершить добавление области.

## <a name="step-2-download-the-aspnet-core-project"></a>Шаг 2. Скачивание проекта ASP.NET Core

> [!div renderon="docs"]
> [Скачивание решения ASP.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/archive/aspnetcore3-1.zip) с сайта GitHub.

> [!div renderon="docs"]
> ## <a name="step-3-configure-the-aspnet-core-project"></a>Шаг 3. Настройка проекта ASP.NET Core
>
> На этом шаге настройте пример кода, чтобы работать с регистрацией приложения, созданной ранее.
>
> 1. Извлеките ZIP-архив в папку рядом с корнем диска. Например, в папку *C:\Azure-Samples*.
> 1. Откройте решение в папке *webapi* в редакторе кода.
> 1. Откройте файл *appsettings.json* и измените следующие значения:
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "Enter_the_Tenant_Info_Here"
>    ```
>
>    - Замените `Enter_the_Application_Id_here`  **идентификатором приложения (клиента)** , зарегистрированного на портале Azure. **Идентификатор приложения (клиента)** можно найти на странице **Обзор** приложения.
>    - Замените `Enter_the_Tenant_Info_Here` одним из следующих элементов:
>       - Если приложение поддерживает вариант **Учетные записи только в этом каталоге организации**, замените это значение **идентификатором каталога (клиента)** (GUID) или **именем клиента** (например, `contoso.onmicrosoft.com`). Вы можете найти **идентификатор каталога (клиента)** на странице **Обзор** приложения.
>       - Если ваше приложение поддерживает вариант **Учетные записи в любом каталоге организации**, замените это значение на `organizations`.
>       - Если приложение поддерживает вариант **Все пользователи с учетными записями Майкрософт**, оставьте это значение как `common`
>
> Для целей этого руководства изменение других значений в файле *appsettings.json* не требуется.

## <a name="how-the-sample-works"></a>Как работает этот пример

Веб-API получает маркер из клиентского приложения, а код в веб-API проверяет маркер. Этот сценарий более подробно описан в статье [Сценарий: защищенный веб-API](scenario-protected-web-api-overview.md).

### <a name="startup-class"></a>Класс Startup

ПО промежуточного слоя *Microsoft.AspNetCore.Authentication* использует класс `Startup`, который выполняется при инициализации хост-процесса. В его методе `ConfigureServices` вызывается метод расширения `AddMicrosoftIdentityWebApi`, предоставленный *Microsoft.Identity.Web*.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApi(Configuration, "AzureAd");
    }
```

С помощью метода `AddAuthentication()` службу можно настроить так, чтобы она добавляла проверку подлинности на основе JwtBearer.

Строка, содержащая `.AddMicrosoftIdentityWebApi`, добавляет в веб-API функцию авторизации платформы удостоверений Майкрософт. Затем она настраивается для проверки маркеров доступа, созданных конечной точкой платформы удостоверений Майкрософт на основе сведений в разделе `AzureAD` файла конфигурации *appsettings.json*:

| ключ *appsettings.json* | Описание                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | **Идентификатор приложения (клиента)** , зарегистрированного на портале Azure.                                                                                       |
| `Instance`             | Конечная точка службы токенов безопасности для проверки подлинности пользователей. Обычно это значение `https://login.microsoftonline.com/`, указывающее на общедоступное облако Azure. |
| `TenantId`             | Имя клиента, идентификатор клиента (GUID) или *общие* пользователи с рабочими или учебными учетными записями или личными учетными записями Майкрософт.                             |

Метод `Configure()` содержит два важных метода: `app.UseAuthentication()` и `app.UseAuthorization()`, которые обеспечивают упомянутые функциональные возможности.

```csharp
// This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // more code
    app.UseAuthentication();
    app.UseAuthorization();
    // more code
}
```

### <a name="protect-a-controller-a-controllers-method-or-a-razor-page"></a>Защита контроллера, метода контроллера или страницы Razor

Контроллер или его методы можно защитить с помощью атрибута `[Authorize]`. Этот атрибут разрешает доступ к контроллеру или методам только аутентифицированным пользователям. Это значит, что для доступа к контроллеру можно создавать запрос на аутентификацию, если пользователь не прошел аутентификацию.

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
```

### <a name="validate-the-scope-in-the-controller"></a>Проверка области в контроллере

Затем код в API проверяет, указаны ли требуемые области в токене, с помощью `HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);`

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        // The Web API will only accept tokens 1) for users, and 2) having the "access_as_user" scope for this API
        static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

        [HttpGet]
        public IEnumerable<WeatherForecast> Get()
        {
            HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

            // some code here
        }
    }
}
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Дальнейшие действия

В репозитории GitHub, содержащем это руководство по примеру кода веб-API ASP.NET Core, есть также инструкции и дополнительные примеры кода, демонстрирующие выполнение следующих действий.

- Добавление проверки подлинности в новый веб-API ASP.NET Core
- Использование веб-API в классическом приложении
- Вызов исходящих API, таких как Microsoft Graph и других API Майкрософт

> [!div class="nextstepaction"]
> [Учебники по веб-API ASP.NET Core на GitHub](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2)
