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
ms.openlocfilehash: ce19041b29d567f061dde59fbe041adf61f889a0
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961488"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Краткое руководство. Создание приложения ASP.NET Core с помощью службы "Конфигурация приложений Azure"

Конфигурация приложений Azure — это служба для управления конфигурациями в Azure. Она позволяет централизовано хранить и администрировать все конфигурации вашего приложения отдельно от кода. В этом кратком руководстве показано, как включить службу в веб-приложение ASP.NET Core. ASP.NET Core создает один объект конфигурации на основе значения ключа с использованием параметров из одного или нескольких источников данных, известных как *поставщики конфигурации*, указанных приложением. Так как в качестве такого поставщика реализован клиент .NET Core Конфигурации приложения, служба выглядит как другой источник данных.

Вы можете использовать любой редактор кода для выполнения шагов в этом кратком руководстве. Однако [Visual Studio Code](https://code.visualstudio.com/) является отличным вариантом, доступным на платформах Windows, MacOS и Linux.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством установите [пакет SDK для .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Создание хранилища конфигураций приложений

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>Создание веб-приложения ASP.NET Core

Вы будете использовать [интерфейс командной строки .NET Core](https://docs.microsoft.com/dotnet/core/tools/) для создания проекта веб-приложения MVC для ASP.NET Core. Преимущество использования .NET Core CLI по сравнению с Visual Studio заключается в том, что он доступен на платформах Windows, MacOS и Linux.

1. Создайте новый каталог для своего проекта В этом кратком руководстве мы назовем его *TestAppConfig*.

2. В новой папке выполните следующую команду, чтобы создать новый проект веб-приложения MVC для ASP.NET Core.

        dotnet new mvc

## <a name="add-secret-manager"></a>Добавление диспетчера секретов

Вы добавите [инструмент "Диспетчер секретов"](https://docs.microsoft.com/aspnet/core/security/app-secrets) в проект. Инструмент "Диспетчер секретов" хранит конфиденциальные данные для разработки вне вашего дерева проектов. Этот подход помогает предотвратить случайный обмен секретами приложений в исходном коде.

- Откройте файл с расширением *CSPROJ*. Добавьте элемент `UserSecretsId`, как показано ниже, и замените его своим значением, которое обычно является идентификатором GUID. Сохраните файл.

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

## <a name="connect-to-app-configuration-store"></a>Подключение к хранилищу конфигураций приложений

1. Добавьте ссылку на пакет NuGet `Microsoft.Extensions.Configuration.AzureAppConfiguration`, выполнив следующую команду:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration

2. Выполните следующую команду, чтобы восстановить пакеты проекта.

        dotnet restore

3. Добавьте секрет с именем *ConnectionStrings:AppConfig* в диспетчер секретов.

    Этот секрет будет содержать строку подключения для получения доступа к хранилищу конфигураций приложения. Замените значение в приведенной ниже команде на строку подключения для вашего хранилища конфигураций приложения.

    Эта команда должна выполняться в том же каталоге, что и файл *CSPROJ*.

        dotnet user-secrets set ConnectionStrings:AppConfig "Endpoint=<your_endpoint>;Id=<your_id>;Secret=<your_secret>"

    Диспетчер секретов будет использоваться только для локального тестирования веб-приложения. При развертывании приложения (например, в [Службе приложений Azure](https://azure.microsoft.com/services/app-service/web)) вы будете использовать параметр приложения (например, **Строки подключения** в Службе приложений) вместо хранения строки подключения с помощью диспетчера секретов.

    Доступ к этому секрету осуществляется с помощью API конфигурации. Двоеточие (:) используется в имени конфигурации с API конфигурации на всех поддерживаемых платформах (см. [описание конфигурации для разных сред](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0#configuration-by-environment)).

4. Откройте файл *Program.cs* и обновите метод `CreateWebHostBuilder` для использования службы "Конфигурация приложения", вызвав метод `config.AddAzureAppConfiguration()`.

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

5. Откройте *Index.cshtml* в каталоге *Представления* > *Главная* и замените его содержимое следующим кодом.

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

6. Откройте *_Layout.cshtml* в каталоге *Представления* > *Общие* и замените его содержимое следующим кодом.

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

2. После того как создание завершится, чтобы запустить веб-приложение локально, выполните следующую команду:

        dotnet run

3. Запустите окно браузера и перейдите по адресу `http://localhost:5000`, который является URL-адресом по умолчанию для размещенного локально веб-приложения.

    ![Краткое руководство. Запуск приложения, размещенного локально](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы создали новое хранилище конфигураций приложения и использовали его с веб-приложением ASP.NET Core. См. дополнительные сведения об использовании службы конфигурации приложений в следующем руководстве, посвященном проверке подлинности.

> [!div class="nextstepaction"]
> [Управляемые удостоверения для интеграции с ресурсами Azure](./integrate-azure-managed-service-identity.md)
