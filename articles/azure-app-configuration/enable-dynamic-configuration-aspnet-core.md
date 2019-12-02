---
title: Руководство по использованию динамической конфигурации Azure App Configuration в приложении ASP.NET Core | Документация Майкрософт
description: Из этого руководства вы узнаете, как динамически обновлять данные конфигурации для приложений ASP.NET Core.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: f49161531753c217e31d0681bcd19043cb47de75
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185264"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Руководство по Использование динамической конфигурации в приложении ASP.NET Core

ASP.NET Core имеет подключаемую систему конфигурации, которая может считывать данные конфигурации из различных источников. ASP.NET Core может мгновенно обрабатывать изменения без необходимости перезапускать приложение. ASP.NET Core поддерживает привязку параметров конфигурации к строго типизированным классам .NET. ASP.NET Core внедряет их в код, используя различные шаблоны `IOptions<T>`. Один из таких шаблонов (`IOptionsSnapshot<T>`) автоматически перезагружает конфигурацию приложения в случае изменения базовых данных. Вы можете внедрить шаблон `IOptionsSnapshot<T>` в контроллеры вашего приложения, чтобы получить доступ к самой последней конфигурации в Azure App Configuration.

Вы также можете настроить клиентскую библиотеку конфигурации приложений ASP.NET Core, чтобы выполнить динамическое обновление набора параметров конфигурации с помощью ПО промежуточного слоя. Пока веб-приложение продолжает получать запросы, параметры конфигурации продолжают обновляться с помощью хранилища конфигураций.

Чтобы поддерживать параметры в актуальном состоянии и избегать слишком большого количества обращений к хранилищу конфигураций, для каждого параметра используется кэш. До истечения срока действия кэшированного значения, операция обновления не выполняет обновление значения, даже если значение изменилось в хранилище конфигураций. Стандартное время истечения для каждого запроса составляет 30 секунд, но при необходимости его можно переопределить.

Из этого руководства вы узнаете, как реализовать динамические обновления конфигурации в коде. При этом в качестве основы используется код веб-приложения, представленный в кратких руководствах. Прежде чем продолжить, ознакомьтесь со статьей [Краткое руководство. Создание приложения ASP.NET Core с помощью службы "Конфигурация приложений Azure"](./quickstart-aspnet-core-app.md).

Вы можете выполнять шаги в этом учебнике с помощью любого редактора кода. [Visual Studio Code](https://code.visualstudio.com/) является отличным вариантом, который доступен на платформах Windows, macOS и Linux.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * настройка приложения на обновление конфигурации при изменении данных в хранилище конфигураций приложений;
> * внедрение последней конфигурации в контроллеры приложения.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим учебником установите [пакет SDK для .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Прежде чем продолжить, ознакомьтесь со статьей [Краткое руководство. Создание приложения ASP.NET Core с помощью службы "Конфигурация приложений Azure"](./quickstart-aspnet-core-app.md).

## <a name="reload-data-from-app-configuration"></a>Перезагрузка данных из App Configuration

1. Добавьте ссылку на пакет NuGet `Microsoft.Azure.AppConfiguration.AspNetCore`, выполнив следующую команду:

    ```CLI
        dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 2.0.0-preview-010060003-1250
    ```

1. Откройте файл *Program.cs* и обновите метод `CreateWebHostBuilder`, чтобы добавить метод `config.AddAzureAppConfiguration()`.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .ConfigureRefresh(refresh =>
                           {
                               refresh.Register("TestApp:Settings:BackgroundColor")
                                      .Register("TestApp:Settings:FontColor")
                                      .Register("TestApp:Settings:Message");
                           });
                });
            })
            .UseStartup<Startup>();
    ```

    Метод `ConfigureRefresh` используется, чтобы указать параметры, используемые для обновления данных конфигурации в хранилище службы "Конфигурация приложений" при запуске операции обновления. Чтобы непосредственно запускать операцию обновления, для приложения необходимо настроить ПО промежуточного слоя, чтобы при любых изменениях выполнить обновление данных конфигурации.

2. Добавьте файл *Settings.cs*, который определяет и реализует новый класс `Settings`.

    ```csharp
    namespace TestAppConfig
    {
        public class Settings
        {
            public string BackgroundColor { get; set; }
            public long FontSize { get; set; }
            public string FontColor { get; set; }
            public string Message { get; set; }
        }
    }
    ```

3. Откройте файл *Startup.cs* и обновите метод `ConfigureServices`, чтобы привязать данные конфигурации к классу `Settings`.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

4. Обновите метод `Configure`, чтобы добавить ПО промежуточного слоя, позволяющее обновлять зарегистрированные для обновления параметры конфигурации, в то время как веб-приложение ASP.NET Core продолжает получать запросы.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();
        app.UseMvc();
    }
    ```
    
    ПО промежуточного слоя использует обновление конфигурации, заданное в методе `AddAzureAppConfiguration`, в `Program.cs`, чтобы активировать обновление для каждого запроса, полученного веб-приложением ASP.NET Core. Операция обновления запускается для каждого запроса, а клиентская библиотека проверяет, не истек ли срок действия кэшированного значения для зарегистрированных параметров конфигурации. Кэшированные значения для параметров с истекшим сроком действия обновляются с помощью хранилища службы "Конфигурация приложений", а остальные значения остаются неизменными.
    
    > [!NOTE]
    > Стандартное время истечения срока действия кэша для параметра конфигурации составляет 30 секунд, но его можно переопределить, вызвав метод `SetCacheExpiration` в инициализаторе параметров, переданном в качестве аргумента методу `ConfigureRefresh`.

## <a name="use-the-latest-configuration-data"></a>Использование последних данных конфигурации

1. Откройте файл *HomeController.cs* в каталоге "Контроллеры" и добавьте в него ссылку на пакет `Microsoft.Extensions.Options`.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Обновите класс `HomeController` для получения класса `Settings` путем внедрения зависимости и воспользуйтесь его значениями.

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        public HomeController(IOptionsSnapshot<Settings> settings)
        {
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }
    }
    ```

3. Откройте файл *Index.cshtml* в каталоге "Представления > Главная" и замените его содержимое следующим сценарием.

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"];
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@ViewData["Message"]</h1>
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Создание и запуск приложения локально

1. Чтобы создать приложение с помощью .NET Core CLI, выполните следующую команду в оболочке командной строки:

        dotnet build

2. Когда создание завершится, запустите веб-приложение локально с помощью следующей команды:

        dotnet run

3. Откройте окно браузера и перейдите по адресу `http://localhost:5000`, который является URL-адресом по умолчанию для веб-приложения, размещенного локально.

    ![Краткое руководство. Запуск приложения, размещенного локально](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. Войдите на [портале Azure](https://portal.azure.com). Щелкните **Все ресурсы** и выберите экземпляр хранилища Конфигурации приложений, который вы создали по инструкциям из краткого руководства.

5. Выберите **Configuration Explorer** (Обозреватель конфигураций) и измените значения следующих ключей.

    | Ключ | Значение |
    |---|---|
    | TestApp:Settings:BackgroundColor | green |
    | TestApp:Settings:FontColor | lightGray |
    | TestApp:Settings:FontSize | Данные из Azure App Configuration — теперь с обновлениями в реальном времени! |

6. Обновите страницу браузера, чтобы просмотреть новые параметры конфигурации. Чтобы отразить изменения, может потребоваться более одного обновления страницы браузера.

    ![Краткое руководство. Обновление размещенного локально приложения](./media/quickstarts/aspnet-core-app-launch-local-after.png)
    
    > [!NOTE]
    > Так как параметры конфигурации кэшируются со стандартным сроком действия 30 секунд, любые изменения, внесенные в параметры в хранилище службы "Конфигурация приложений", будут отражены в веб-приложении только после истечения срока действия кэша.

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Дополнительная информация

В рамках этого учебника вы включили в веб-приложении ASP.NET Core динамическое обновление параметров конфигурации из службы "Конфигурация приложения". Чтобы узнать, как с помощью удостоверения, управляемого Azure, упростить доступ к службе "Конфигурация приложений Azure", перейдите к следующему учебнику.

> [!div class="nextstepaction"]
> [Руководство по интеграции с управляемыми удостоверениями Azure](./howto-integrate-azure-managed-service-identity.md)
