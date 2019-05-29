---
title: Создание веб-интерфейса API для .NET, который интегрируется с Azure AD для аутентификации и авторизации | Документация Майкрософт
description: Практическое руководство по созданию веб-API для .NET MVC, который интегрируется с Azure AD для аутентификации и авторизации.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: ryanwi
ms.reviewer: jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e2eca253bc5d1495d26506e0e6f8a83762e8bc5
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2019
ms.locfileid: "66001111"
---
# <a name="quickstart-build-a-net-web-api-that-integrates-with-azure-ad-for-authentication-and-authorization"></a>Краткое руководство. Создание веб-интерфейса API для .NET, который интегрируется с Azure AD для аутентификации и авторизации

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

Если вы создаете приложение, которое предоставляет доступ к конфиденциальным ресурсам, то необходимо знать, как предотвратить несанкционированный доступ к этим ресурсам. Azure Active Directory (Azure AD) упрощает для разработчика процесс защиты веб-API с помощью токенов носителя OAuth 2.0 — потребуется добавить всего несколько строк кода.

В веб-приложениях ASP.NET эту защиту можно реализовать с помощью поддерживаемого сообществом ПО промежуточного слоя OWIN корпорации Майкрософт, включенного в .NET Framework 4.5. Мы будем использовать OWIN для создания веб-API To Do List, который:

* указывает, какие API защищены;
* проверяет, содержат ли вызовы веб-API действительный маркер доступа.

В этом кратком руководстве описано, как создать API To Do List и выполнить следующие задачи:

1. зарегистрировать приложение в Azure AD;
2. Настроить приложение для использования конвейера аутентификации OWIN.
3. Настроить клиентское приложение для вызова веб-API.

## <a name="prerequisites"></a>Предварительные требования

Чтобы начать работу, выполните следующие предварительные требования:

* Скачайте [схему приложения](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) или [готовый пример](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Каждый из них является решением Visual Studio 2013.
* Вам также потребуется клиент Azure AD для регистрации приложения. Если у вас нет клиента, [узнайте, как его получить](quickstart-create-new-tenant.md).

## <a name="step-1-register-an-application-with-azure-ad"></a>Шаг 1. зарегистрировать приложение в Azure AD;

Для защиты приложения необходимо сначала создать приложение в клиенте и предоставить Azure AD некоторую важную информации.

1. Войдите на [портале Azure](https://portal.azure.com).
2. Выберите клиент Azure AD. Для этого выберите учетную запись в правом верхнем углу страницы, щелкните элемент навигации **Переключение каталога** и выберите соответствующий клиент.
    * Пропустите этот шаг, если в вашей учетной записи только один клиент Azure AD или если вы уже выбрали соответствующий клиент Azure AD.

3. В области навигации слева выберите **Azure Active Directory**.
4. Щелкните **Регистрация приложений** и выберите **Новая регистрация**.
5. Когда откроется страница **Register an application** (Регистрация приложения), введите имя приложения.
В разделе **Поддерживаемые типы учетных записей** выберите **Accounts in any organizational directory and personal Microsoft accounts** (Учетные записи в любом каталоге организации и личные учетные записи Майкрософт).
6. Для платформы выберите тип **Веб** в разделе **URI перенаправления** и установите значение `https://localhost:44321/` (расположение, в которое Azure AD будет возвращать маркеры).
7. По завершении щелкните **Зарегистрировать**. На странице приложения **Обзор** запишите **идентификатор приложения (клиента)** .
6. Выберите **Предоставление API**, а затем обновите URI идентификатора приложения, щелкнув **Задать**. Введите идентификатор конкретного клиента. Например, введите `https://contoso.onmicrosoft.com/TodoListService`.
7. Сохраните конфигурацию. Не закрывайте портал, так как через некоторое время потребуется зарегистрировать клиентское приложение.

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>Шаг 2. Настройка приложения для использования конвейера аутентификации OWIN

Чтобы проверять входящие запросы и токены, необходимо настроить приложение для взаимодействия с Azure AD.

1. Для начала откройте решение и добавьте пакеты NuGet для ПО промежуточного слоя OWIN в проект TodoListService с помощью консоли диспетчера пакетов.

    ```
    PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
    PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
    ```

2. Добавьте класс OWIN Startup в проект TodoListService под именем `Startup.cs`.  Щелкните проект правой кнопкой мыши и выберите **Добавить > Новый элемент**, после чего найдите **OWIN**. При запуске вашего приложения промежуточный слой OWIN вызовет метод `Configuration(…)` .

3. Замените объявление класса `public partial class Startup`. Часть этого класса уже была реализована в другом файле. В методе `Configuration(…)` отправьте вызов в `ConfgureAuth(…)`, чтобы настроить аутентификацию для веб-приложения.

    ```csharp
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
    ```

4. Откройте файл `App_Start\Startup.Auth.cs` и реализуйте метод `ConfigureAuth(…)`. Параметры, указанные в `WindowsAzureActiveDirectoryBearerAuthenticationOptions`, будут служить координатами приложения для взаимодействия с Azure AD. Для их использования необходимо использовать классы в пространстве имен `System.IdentityModel.Tokens`.

    ```csharp
    using System.IdentityModel.Tokens;
    ```

    ```csharp
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseWindowsAzureActiveDirectoryBearerAuthentication(
            new WindowsAzureActiveDirectoryBearerAuthenticationOptions
            {
                 Tenant = ConfigurationManager.AppSettings["ida:Tenant"],
                 TokenValidationParameters = new TokenValidationParameters
                 {
                    ValidAudience = ConfigurationManager.AppSettings["ida:Audience"]
                 }
            });
    }
    ```

5. Используйте атрибуты `[Authorize]` для защиты контроллеров и действий с помощью аутентификации носителей JSON Web Token (JWT). Отметьте класс `Controllers\TodoListController.cs` тегом авторизации. Пользователь будет вынужден войти в систему, прежде чем сможет обратиться к этой странице.

    ```csharp
    [Authorize]
    public class TodoListController : ApiController
    {
    ```

    Когда авторизованный вызывающий объект успешно вызывает один из интерфейсов API `TodoListController` , может потребоваться доступ к сведениям о вызывающем объекте. OWIN предоставляет доступ к утверждениям внутри маркера защиты посредством объекта `ClaimsPrincpal` .  

6. Общим требованием для веб-интерфейсов API является проверка областей в маркере, чтобы гарантировать, что конечный пользователь предоставил разрешения, необходимые для доступа к службе To Do List:

    ```csharp
    public IEnumerable<TodoItem> Get()
    {
        // user_impersonation is the default permission exposed by applications in Azure AD
        if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
        {
            throw new HttpResponseException(new HttpResponseMessage {
              StatusCode = HttpStatusCode.Unauthorized,
              ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
            });
        }
        ...
    }
    ```

7. Откройте файл `web.config` в корне проекта TodoListService и введите значения конфигурации в разделе `<appSettings>`.
    * `ida:Tenant` — это имя вашего клиента Azure AD, например contoso.onmicrosoft.com.
    * `ida:Audience` — это URI идентификатора приложения, введенное на портале Azure.

## <a name="step-3-configure-a-client-application-and-run-the-service"></a>Шаг 3. Настройка клиентского приложения и запуск службы

Чтобы увидеть службу списка дел в действии, необходимо настроить клиент списка дел для получения токенов от Azure AD и выполнения вызовов службы.

1. Вернитесь на [портал Azure](https://portal.azure.com).
1. Создайте регистрацию приложения в клиенте Azure AD.  Введите **имя**, описывающее ваше приложение для пользователей, введите `http://TodoListClient/` в качестве **URI перенаправления** и выберите в раскрывающемся списке пункт **Общедоступный клиент (мобильный и классический)** .
1. Когда вы завершите регистрацию, Azure AD присвоит приложению уникальный идентификатор приложения. Это значение вам понадобится для следующих действий, поэтому скопируйте его на странице приложения.
1. Выберите **Разрешения API приложения**, а затем — **Добавить разрешение**.  Найдите и выберите службу списка дел, добавьте разрешение **user_impersonation Access TodoListService** в списке **Делегированные разрешения**, а затем щелкните **Добавить разрешения**.
1. В Visual Studio откройте файл `App.config` в проекте TodoListClient и введите значения конфигурации в раздел `<appSettings>`.

    * `ida:Tenant` — это имя вашего клиента Azure AD, например contoso.onmicrosoft.com.
    * `ida:ClientId` — это идентификатор приложения, скопированный с портала Azure.
    * `todo:TodoListResourceId` — это URI идентификатора приложения службы списка дел, введенный на портале Azure.

1. Выполните очистку, компиляцию и запуск каждого проекта.
1. Если вы еще это не сделали, создайте пользователя в своем клиенте с доменом *.onmicrosoft.com.
1. Войдите в клиент списка дел от имени данного пользователя и добавьте несколько задач в его список дел.

## <a name="next-steps"></a>Дополнительная информация

* Скачайте для справки готовый пример (без ваших значений конфигурации) с сайта [GitHub](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Теперь можно приступить к другим сценариям идентификации.
