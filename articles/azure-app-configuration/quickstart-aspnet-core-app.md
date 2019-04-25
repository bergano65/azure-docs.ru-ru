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
ms.openlocfilehash: 29cea7e72d6bd7f64f6cf2a68b7620090ea4eef3
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995939"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Краткое руководство. Создание приложения ASP.NET Core с помощью службы "Конфигурация приложений Azure"

Конфигурация приложений Azure — это служба для управления конфигурациями в Azure. С ее помощью вы можете централизовано хранить и администрировать все параметры приложения отдельно от кода. В этом кратком руководстве показано, как включить службу в веб-приложение ASP.NET Core. 

ASP.NET Core создает один объект конфигурации на основе пары "ключ — значение" с использованием параметров из одного или нескольких источников данных, указанных приложением. Эти источники данных называются *поставщиками конфигурации*. Так как в качестве такого поставщика реализован клиент .NET Core службы "Конфигурация приложений", служба выглядит как другой источник данных.

Шаги из этого краткого руководства можно выполнять в любом редакторе кода. [Visual Studio Code](https://code.visualstudio.com/) является отличным вариантом, который доступен на платформах Windows, macOS и Linux.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством установите [пакет SDK для .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Создание хранилища конфигураций приложений

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Выберите **Обозреватель ключей и значений** > **+ Создать**, чтобы добавить следующие пары "ключ-значение":

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

        dotnet new mvc

## <a name="add-secret-manager"></a>Добавление диспетчера секретов

Добавьте в проект [инструмент "Диспетчер секретов"](https://docs.microsoft.com/aspnet/core/security/app-secrets). Инструмент "Диспетчер секретов" хранит конфиденциальные данные для разработки вне вашего дерева проектов. Этот подход помогает предотвратить случайный обмен секретами приложений в исходном коде.

- Откройте файл с расширением *CSPROJ*. Добавьте элемент `UserSecretsId`, как показано здесь, и замените его своим значением, которое обычно является идентификатором GUID. Сохраните файл.

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

## <a name="connect-to-an-app-configuration-store"></a>Подключение к хранилищу конфигураций приложений

1. Добавьте ссылку на пакет NuGet `Microsoft.Extensions.Configuration.AzureAppConfiguration`, выполнив следующую команду:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration --version 1.0.0-preview-007830001

2. Выполните следующую команду, чтобы восстановить пакеты проекта:

        dotnet restore

3. Добавьте секрет с именем *ConnectionStrings:AppConfig* в диспетчер секретов.

    Этот секрет содержит строку подключения для получения доступа к хранилищу конфигураций приложений. Замените значение в следующей команде строкой подключения для вашего хранилища конфигураций приложений.

    Эта команда должна выполняться в том же каталоге, что и файл *CSPROJ*.

        dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>

    Диспетчер секретов используется только для локальной проверки веб-приложения. При развертывании приложения (например, в [Службе приложений Azure](https://azure.microsoft.com/services/app-service/web)) используйте параметр приложения (например, **Строки подключения** в Службе приложений). Используйте этот параметр вместо хранения строки подключения с помощью диспетчера секретов.

    Доступ к этому секрету осуществляется с помощью API конфигурации. Двоеточие (:) используется в имени конфигурации с API конфигурации на всех поддерживаемых платформах. Дополнительные сведения см. в статье [Конфигурация в .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

4. Откройте файл *Program.cs* и добавьте ссылку на поставщик конфигурации .NET Core для службы "Конфигурация приложений".

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
                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .SetOfflineCache(new OfflineFileCache());
                });
            })
            .UseStartup<Startup>();
    ```

6. Откройте файл Index.cshtml в каталоге "Представления" > "Домашняя страница" и замените его содержимое следующим кодом.

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@Configuration["TestApp:Settings:Message"]</h1>
    </body>
    </html>
    ```

7. Откройте файл _Layout.cshtml в каталоге "Представления" > "Общие" и замените его содержимое следующим кодом.

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

    ![Краткое руководство. Запуск приложения, размещенного локально](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы создали хранилище конфигураций приложения и использовали его с веб-приложением ASP.NET Core с помощью [поставщика Конфигурации приложений](https://go.microsoft.com/fwlink/?linkid=2074664). Ознакомьтесь с дополнительными сведениями об использовании службы "Конфигурация приложений" в следующем учебнике, посвященном проверке подлинности.

> [!div class="nextstepaction"]
> [Руководство по интеграции с управляемыми удостоверениями Azure](./howto-integrate-azure-managed-service-identity.md)
