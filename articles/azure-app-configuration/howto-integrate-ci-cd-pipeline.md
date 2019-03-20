---
title: Интеграция с конвейера непрерывной интеграции и доставки с помощью конфигурации приложения Azure | Документация Майкрософт
description: Узнайте, как создать файл конфигурации с помощью данных в конфигурации приложения Azure во время непрерывной интеграции и доставки
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
ms.openlocfilehash: cb9fe6dc234c317daa5eabec01812324e7c81663
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224334"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Интеграция с конвейером CI/CD

Вы можете повысить устойчивость приложения удаленного возможной не смогла перейдете к настройке приложения Azure. Для этого пакета текущие данные конфигурации в файл, который развертывается с приложением и загружен локально во время его запуска. Такой подход гарантирует, что приложение по крайней мере получит значения параметров по умолчанию. Эти значения будут перезаписаны, более новые изменения в хранилище конфигурации приложения, когда она станет доступна.

С помощью [Экспорт](./howto-import-export-data.md#export-data) функция конфигурации приложения Azure, можно автоматизировать процесс получения данных конфигурации в качестве отдельного файла. Затем можно внедрите этот файл на этапе сборки или развертывания в процессе непрерывной интеграции и конвейера непрерывного развертывания (CI/CD).

Приведенный ниже показано, как включить конфигурацию приложения данные в виде сборки шаг для веб-приложения, представленные в кратких руководствах. Прежде чем продолжить, необходимо завершить [создать приложение ASP.NET Core с помощью конфигурации приложения](./quickstart-aspnet-core-app.md) первого.

Можно использовать любой редактор кода, описанная в этом кратком руководстве. [Visual Studio Code](https://code.visualstudio.com/) является отличным вариантом доступна на платформах Linux, macOS и Windows.

## <a name="prerequisites"></a>Технические условия

При создании локально, скачайте и установите [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Если у вас еще нет.

Чтобы выполнить построение облака, с DevOps в Azure, убедитесь, что [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) установлен в системе сборки.

## <a name="export-an-app-configuration-store"></a>Экспорт конфигурации магазина

1. Откройте ваш *.csproj* файла и добавьте следующий скрипт:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -f $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    Добавить *ConnectionString* связанные с хранилищем конфигурации приложения как переменную среды.

2. Откройте файл Program.cs и обновить `CreateWebHostBuilder` экспортированный JSON-файл путем вызова метода `config.AddJsonFile()` метод.

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

1. Задайте переменную среды с именем **ConnectionString**и задайте для него ключ доступа к хранилищу конфигурации приложения. Если вы используете командную строку Windows, выполните следующую команду и перезапустите командной строке, чтобы разрешить внесение изменений в силу:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Если вы используете Windows PowerShell, выполните следующую команду:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Если вы используете macOS или Linux, выполните следующую команду:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Чтобы построить приложение с помощью интерфейса командной строки .NET Core, выполните следующую команду в командной строке:

        dotnet build

3. После успешного завершения сборки, выполните следующую команду, чтобы запустить веб-приложение локально:

        dotnet run

4. Откройте окно браузера и перейдите к `http://localhost:5000`, который является URL-адрес по умолчанию для веб-приложения, размещенные локально.

    ![Краткое руководство. Запуск приложения, размещенного локально](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Управляемые удостоверения для интеграции с ресурсами Azure](./integrate-azure-managed-service-identity.md)
