---
title: Интеграция с конвейера непрерывной интеграции и доставки с помощью конфигурации приложения Azure | Документация Майкрософт
description: Узнайте, как создать файл конфигурации, используя данные в конфигурации приложения Azure во время непрерывной интеграции и доставки
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 7b2e919bc46810e8478956675ffeb1cb0542da85
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957374"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Интеграция с конвейером CI/CD

Чтобы повысить устойчивость приложения удаленного возможной не смогла перейдете к настройке приложения Azure, должны упаковать текущие данные конфигурации в файл, который развертывается с приложением и локально загружается во время его запуска . Такой подход гарантирует, что приложение будет иметь значения параметров по умолчанию по крайней мере. Эти значения будут перезаписаны, новые изменения в конфигурации магазина, когда она станет доступна.

С помощью [ **Экспорт** ](./howto-import-export-data.md#export-data) функция конфигурации приложения Azure, можно автоматизировать процесс получения данных конфигурации в качестве отдельного файла. Затем этот файл можно внедрить на этапе сборки или развертывания в процессе непрерывной интеграции и непрерывного развертывания конвейера.

Приведенный ниже показано, как включить конфигурацию приложения данные в виде сборки шаг для веб-приложения, представленные в кратких руководствах. Прежде чем продолжить, прочитайте краткое руководство [Создание приложения ASP.NET Core с помощью службы "Конфигурация приложений Azure"](./quickstart-aspnet-core-app.md).

Вы можете использовать любой редактор кода для выполнения шагов в этом кратком руководстве. Однако [Visual Studio Code](https://code.visualstudio.com/) является отличным вариантом, доступным на платформах Windows, MacOS и Linux.

## <a name="prerequisites"></a>Технические условия

При создании локально, скачайте и установите [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Если у вас еще нет.

Если вы хотите выполнить построение облака, с помощью DevOps в Azure, убедитесь, что [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) установлен в системе сборки.

## <a name="export-app-configuration-store"></a>Экспорт хранилища конфигурации приложения

1. Откройте ваш *.csproj* файл и добавьте следующий код:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -f $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    *ConnectionString* связанные с конфигурацией приложения необходимо добавить хранилище в качестве переменной среды.

2. Откройте *Program.cs* и обновить `CreateWebHostBuilder` метод, чтобы использовать экспортированный json-файл путем вызова `config.AddJsonFile()` метод.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var directory = System.IO.Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location);
                var settings = config.Build();

                config.AddJsonFile(Path.Combine(directory, "azureappconfig.json"));
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

## <a name="build-and-run-the-app-locally"></a>Создание и запуск приложения локально

1. Задайте переменную среды с именем **ConnectionString** и укажите для нее ключ доступа к хранилищу конфигураций приложений. Если вы используете командную строку Windows, выполните следующую команду и перезапустите командную строку, чтобы изменения вступили в силу:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    При использовании Windows PowerShell выполните следующую команду:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    При использовании macOS или Linux воспользуйтесь приведенной ниже командой:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Чтобы создать приложение с помощью .NET Core CLI, выполните следующую команду в оболочке командной строки:

        dotnet build

3. После того как создание завершится, чтобы запустить веб-приложение локально, выполните следующую команду:

        dotnet run

4. Запустите окно браузера и перейдите по адресу `http://localhost:5000`, который является URL-адресом по умолчанию для размещенного локально веб-приложения.

    ![Краткое руководство. Запуск приложения, размещенного локально](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Управляемые удостоверения для интеграции с ресурсами Azure](./integrate-azure-managed-service-identity.md)
