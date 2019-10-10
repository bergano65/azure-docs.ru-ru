---
title: Краткое руководство по Конфигурации приложения Azure с ASP.NET Core | Документация Майкрософт
description: Краткое руководство по использованию Конфигурации приложения Azure с приложениями ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET Core
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: a2764c8e634fd8d827cba9fa7ec9cb61cc6c40af
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035301"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Краткое руководство. Создание приложения ASP.NET Core с помощью службы "Конфигурация приложений Azure"

В этом кратком руководстве описано, как добавить службу конфигурации приложений Azure в приложение ASP.NET Core, чтобы обеспечить централизованное хранение параметров приложения и управление ими отдельно от кода. ASP.NET Core создает один объект конфигурации на основе пары "ключ — значение" с использованием параметров из одного или нескольких источников данных, указанных приложением. Эти источники данных называются *поставщиками конфигурации*. Так как в качестве такого поставщика реализован клиент .NET Core службы "Конфигурация приложений", служба выглядит как другой источник данных.

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
- [Базовый пакет SDK для .NET](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Создание хранилища конфигураций приложений

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Выберите **Configuration Explorer** (Обозреватель конфигураций)  >  **+ Создать**, чтобы добавить следующие пары "ключ-значение".

    | Ключ | Значение |
    |---|---|
    | TestApp:Settings:BackgroundColor | Белый |
    | TestApp:Settings:FontSize | 24 |
    | TestApp:Settings:FontColor | Черный |
    | TestApp:Settings:FontSize | Данные из конфигурации приложения Azure |

    Поля **Метка** и **Тип контента** пока заполнять не нужно.

## <a name="create-an-aspnet-core-web-app"></a>Создание веб-приложения ASP.NET Core

Используйте [интерфейс командной строки .NET Core](https://docs.microsoft.com/dotnet/core/tools/) для создания проекта веб-приложения MVC для ASP.NET Core. Преимущество использования .NET Core CLI по сравнению с Visual Studio заключается в том, что он доступен на платформах Windows, macOS и Linux.

1. Создайте новый каталог для своего проекта В этом кратком руководстве назовите его *TestAppConfig*.

2. В новой папке выполните следующую команду, чтобы создать проект веб-приложения MVC для ASP.NET Core:

        dotnet new mvc --no-https

## <a name="add-secret-manager"></a>Добавление диспетчера секретов

Чтобы использовать диспетчер секретов, добавьте элемент `UserSecretsId` в файл *.csproj*.

- Откройте *CSPROJ*-файл. Добавьте элемент `UserSecretsId`, как показано здесь. Вы можете использовать тот же GUID или заменить это значение собственным. Сохраните файл.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.App" />
        <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
    </ItemGroup>

    </Project>
    ```

Инструмент "Диспетчер секретов" хранит конфиденциальные данные для разработки вне вашего дерева проектов. Этот подход помогает предотвратить случайный обмен секретами приложений в исходном коде. См. сведения о диспетчере секретов в руководстве по [безопасному хранению секретов приложений при разработке с помощью ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/app-secrets).

## <a name="connect-to-an-app-configuration-store"></a>Подключение к хранилищу конфигураций приложений

1. Добавьте ссылку на пакет NuGet `Microsoft.Azure.AppConfiguration.AspNetCore`, выполнив следующую команду:

        dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 2.0.0-preview-010060003-1250

2. Выполните следующую команду, чтобы восстановить пакеты проекта:

        dotnet restore

3. Добавьте секрет с именем *ConnectionStrings:AppConfig* в диспетчер секретов.

    Этот секрет содержит строку подключения для получения доступа к хранилищу конфигураций приложений. Замените значение в следующей команде строкой подключения для вашего хранилища конфигураций приложений.

    Эта команда должна выполняться в том же каталоге, что и файл *CSPROJ*.

        dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>

    > [!IMPORTANT]
    > Некоторые оболочки усекают строки подключения, которые не заключены в кавычки. Убедитесь, что вывод команды `dotnet user-secrets` отображает полную строку подключения. В противном случае повторно выполните команду, заключив строку подключения в кавычки.

    Диспетчер секретов используется только для локальной проверки веб-приложения. При развертывании приложения, например в [Службе приложений Azure](https://azure.microsoft.com/services/app-service/web), вы будете использовать параметр приложения **Строки подключения** в Службе приложений, а не хранить строку подключения с помощью диспетчера секретов.

    Доступ к этому секрету осуществляется с помощью API конфигурации. Двоеточие (:) используется в имени конфигурации с API конфигурации на всех поддерживаемых платформах. Дополнительные сведения см. в статье [Конфигурация в .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

4. Откройте файл *Program.cs* и добавьте ссылку на поставщик конфигурации приложений .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

5. Обновите метод `CreateWebHostBuilder`, чтобы использовать службу "Конфигурация приложений", путем вызова метода `config.AddAzureAppConfiguration()`.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

6. Откройте файл *Index.cshtml* в каталоге "Представления > Главная" и замените его содержимое следующим кодом.

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]</h1>
    ```

7. Откройте файл *_Layout.cshtml* в каталоге "Представления > Общие" и замените его содержимое следующим кодом.

    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>@ViewData["Title"] - hello_world</title>

        <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
        <link rel="stylesheet" href="~/css/site.css" />
    </head>
    <body>
        <div class="container body-content">
            @RenderBody()
        </div>

        <script src="~/lib/jquery/dist/jquery.js"></script>
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>

        @RenderSection("Scripts", required: false)
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Создание и запуск приложения локально

1. Чтобы создать приложение с помощью .NET Core CLI, выполните следующую команду в оболочке командной строки:

        dotnet build

2. Когда создание завершится, запустите веб-приложение локально с помощью следующей команды:

        dotnet run

3. Откройте окно браузера и перейдите по адресу `http://localhost:5000`, который является URL-адресом по умолчанию для веб-приложения, размещенного локально.

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы создали хранилище конфигураций приложения и использовали его с веб-приложением ASP.NET Core с помощью [поставщика Конфигурации приложений](https://go.microsoft.com/fwlink/?linkid=2074664). См. сведения об использовании Конфигурации приложений в следующем руководстве, где описано, как сделать так, чтобы веб-приложение динамически обновляло параметры конфигурации.

> [!div class="nextstepaction"]
> [Использование динамической конфигурации в приложении ASP.NET Core](./enable-dynamic-configuration-aspnet-core.md)
