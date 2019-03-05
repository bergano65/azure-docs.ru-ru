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
ms.openlocfilehash: 44ae922b182874eef378d4868fb278c3c76252db
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884418"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Руководство. Использование динамической конфигурации в приложении ASP.NET Core

ASP.NET Core имеет подключаемую систему конфигурации, которая может считывать данные конфигурации из различных источников, а также динамически обрабатывать изменения без необходимости перезапускать приложение. Она поддерживает привязку параметров конфигурации к строго типизированным классам .NET и внедрение таких классов в ваш код с помощью различных шаблонов `IOptions<T>`. Один из таких шаблонов (`IOptionsSnapshot<T>`) обеспечивает автоматическую перезагрузку конфигурации приложения в случае изменения базовых данных. Вы можете внедрить шаблон `IOptionsSnapshot<T>` в контроллеры вашего приложения, чтобы получить доступ к самой последней конфигурации в Azure App Configuration. Кроме того, вы можете настроить клиентскую библиотеку ASP.NET Core в App Configuration на непрерывное отслеживание и извлечение любых изменений в хранилище конфигураций приложений путем его опроса через определенные вами периоды времени.

Из этого руководства вы узнаете, как реализовать динамические обновления конфигурации в коде. При этом в качестве основы используется код веб-приложения, представленный в кратких руководствах. Прежде чем продолжить, прочитайте краткое руководство [Создание приложения ASP.NET Core с помощью службы "Конфигурация приложений Azure"](./quickstart-aspnet-core-app.md).

Вы можете использовать любой редактор кода для выполнения шагов в этом кратком руководстве. Однако [Visual Studio Code](https://code.visualstudio.com/) является отличным вариантом, доступным на платформах Windows, MacOS и Linux.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * настройка приложения на обновление конфигурации при изменении данных в хранилище конфигураций приложений;
> * внедрение последней конфигурации в контроллеры приложения.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством установите [пакет SDK для .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Перезагрузка данных из App Configuration

1. Откройте файл *Program.cs* и обновите метод `CreateWebHostBuilder`, добавив метод `config.AddAzureAppConfiguration()`.

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

    Второй параметр в методе `.Watch` — это интервал опроса, с которым клиентская библиотека ASP.NET опрашивает хранилище конфигураций приложений на наличие изменений в конкретном параметре конфигурации.

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

## <a name="use-the-latest-configuration-data"></a>Использование последних данных конфигурации

1. Откройте файл *HomeController.cs* в каталоге *Controllers*, обновите класс `HomeController` на получение класса `Settings` через внедрение зависимости и воспользуйтесь его значениями.

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

2. Откройте файл *Index.cshtml* в каталоге *Views* > *Home* и замените его содержимое следующим кодом:

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

2. После того как создание завершится, чтобы запустить веб-приложение локально, выполните следующую команду:

        dotnet run

3. Запустите окно браузера и перейдите по адресу `http://localhost:5000`, который является URL-адресом по умолчанию для размещенного локально веб-приложения.

    ![Краткое руководство. Запуск приложения, размещенного локально](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. Войдите на [портал Azure](https://aka.ms/azconfig/portal), щелкните **Все ресурсы** и выберите экземпляр хранилища конфигураций приложений, который вы создали при работе с кратким руководством.

5. Щелкните **Обозреватель ключей и значений** и обновите значения следующих ключей:

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

По инструкциям из этого руководства вы добавили управляемое удостоверение службы Azure, чтобы упростить доступ к службе "Конфигурация приложений" и улучшить управление учетными данными для вашего приложения. Чтобы узнать больше об использовании службы "Конфигурация приложений", перейдите к статье "Azure CLI samples" (Примеры скриптов Azure CLI).

> [!div class="nextstepaction"]
> [Примеры для CLI](./cli-samples.md)
