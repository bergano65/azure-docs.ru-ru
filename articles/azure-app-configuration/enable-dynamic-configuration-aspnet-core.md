---
title: Руководство по использованию динамической конфигурации Azure App Configuration в приложении ASP.NET Core | Документация Майкрософт
description: Из этого руководства вы узнаете, как динамически обновлять данные конфигурации для приложений ASP.NET Core.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: d5e1e5989f9902d9ab8dcc3e6c2b9d2a71f12df9
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224385"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Руководство по Использование динамической конфигурации в приложении ASP.NET Core

ASP.NET Core имеет подключаемую систему конфигурации, которая может считывать данные конфигурации из различных источников. ASP.NET Core может мгновенно обрабатывать изменения без необходимости перезапускать приложение. ASP.NET Core поддерживает привязку параметров конфигурации к строго типизированным классам .NET. ASP.NET Core внедряет их в код, используя различные шаблоны `IOptions<T>`. Один из таких шаблонов (`IOptionsSnapshot<T>`) автоматически перезагружает конфигурацию приложения в случае изменения базовых данных. 

Вы можете внедрить шаблон `IOptionsSnapshot<T>` в контроллеры вашего приложения, чтобы получить доступ к самой последней конфигурации в Azure App Configuration. Вы также можете настроить в клиентской библиотеке ASP.NET Core в службе конфигурации приложений непрерывное отслеживание и извлечение любых изменений в хранилище конфигураций приложений. Вы определяете периодический интервал для опроса.

Из этого руководства вы узнаете, как реализовать динамические обновления конфигурации в коде. При этом в качестве основы используется код веб-приложения, представленный в кратких руководствах. Прежде чем продолжить, ознакомьтесь со статьей [Краткое руководство. Создание приложения ASP.NET Core с помощью службы "Конфигурация приложений Azure"](./quickstart-aspnet-core-app.md).

Шаги из этого краткого руководства можно выполнять в любом редакторе кода. [Visual Studio Code](https://code.visualstudio.com/) является отличным вариантом, который доступен на платформах Windows, macOS и Linux.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * настройка приложения на обновление конфигурации при изменении данных в хранилище конфигураций приложений;
> * внедрение последней конфигурации в контроллеры приложения.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством установите [пакет SDK для .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Перезагрузка данных из App Configuration

1. Откройте файл Program.cs и обновите метод `CreateWebHostBuilder`, добавив метод `config.AddAzureAppConfiguration()`.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(o => o.Connect(settings["ConnectionStrings:AppConfig"])
                    .Watch("TestApp:Settings:BackgroundColor", TimeSpan.FromSeconds(1))
                    .Watch("TestApp:Settings:FontColor", TimeSpan.FromSeconds(1))
                    .Watch("TestApp:Settings:Message", TimeSpan.FromSeconds(1)));
            })
            .UseStartup<Startup>();
    ```

    Второй параметр в методе `.Watch` — это интервал опроса, с которым клиентская библиотека ASP.NET опрашивает хранилище конфигураций приложений. Клиентская библиотека проверяет конкретный параметр конфигурации, чтобы увидеть, произошло ли какое-либо изменение.

2. Добавьте файл Settings.cs, который определяет и реализует новый класс `Settings`.

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

3. Откройте файл Startup.cs и обновите метод `ConfigureServices`, чтобы привязать данные конфигурации к классу `Settings`.

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

## <a name="use-the-latest-configuration-data"></a>Использование последних данных конфигурации

1. Откройте файл HomeController.cs в каталоге контроллеров. Обновите класс `HomeController` для получения класса `Settings` путем внедрения зависимости и воспользуйтесь его значениями.

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

2. Откройте Index.cshtml в каталоге "Представления > Главная" и замените его содержимое следующим скриптом.

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

4. Войдите на [портале Azure](https://aka.ms/azconfig/portal). Щелкните **Все ресурсы** и выберите экземпляр хранилища конфигураций приложений, который вы создали по инструкциям из краткого руководства.

5. Выберите **Обозреватель ключей и значений** и обновите значения следующих ключей:

    | Ключ | Значение |
    |---|---|
    | TestAppSettings:BackgroundColor | blue |
    | TestAppSettings:FontColor | lightGray |
    | TestAppSettings:Message | Данные из Azure App Configuration — теперь с обновлениями в реальном времени! |

6. Обновите страницу браузера, чтобы просмотреть новые параметры конфигурации.

    ![Краткое руководство. Обновление размещенного локально приложения](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Дополнительная информация

По инструкциям из этого руководства вы добавили управляемое удостоверение службы Azure, чтобы упростить доступ к службе "Конфигурация приложений" и улучшить управление учетными данными для вашего приложения. Чтобы узнать больше об использовании службы "Конфигурация приложений", перейдите к примерам скриптов Azure CLI.

> [!div class="nextstepaction"]
> [Примеры интерфейса командной строки](./cli-samples.md)
