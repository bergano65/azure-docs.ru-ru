---
title: Руководство по интеграции конвейера непрерывной интеграции и доставки с помощью Конфигурации приложений Azure | Документация Майкрософт
description: В этом руководстве вы узнаете, как создать файл конфигурации с помощью данных в Конфигурации приложений Azure во время непрерывной интеграции и доставки.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: e9b81baed14b18c6db736bd94a2aba43a4e671ad
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185106"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Интеграция с конвейером CI/CD

В этой статье описываются различные способы использования данных из Конфигурации приложений Azure в системе непрерывной интеграции и непрерывного развертывания.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>Использование Конфигурации приложений в конвейере Azure DevOps

Если у вас есть конвейер Azure DevOps, можно извлечь значения ключей из Конфигурации приложений и установить их в качестве переменных задач. [Расширение Конфигурации приложений для Azure DevOps](https://go.microsoft.com/fwlink/?linkid=2091063) — это дополнительный модуль, обеспечивающий эти функциональные возможности. Просто следуйте встроенным инструкциям по использованию расширения в последовательности задач сборки или выпуска.

## <a name="deploy-app-configuration-data-with-your-application"></a>Развертывание данных Конфигурации приложений с приложением

Ваше приложение может не запуститься, если оно зависит от Конфигурации приложений Azure и не сможет получить к ней доступ. Вы можете повысить устойчивость приложения на этот случай, хотя подобное событие маловероятно. Для этого упакуйте текущие данные конфигурации в файл, который развертывается с приложением и загружается локально во время его запуска. Такой подход гарантирует, что приложение по крайней мере получит значения параметров по умолчанию. Эти значения будут перезаписаны более новыми изменениями из хранилища Конфигурации приложений, когда оно станет доступно.

С помощью функции [Экспорт](./howto-import-export-data.md#export-data) Конфигурации приложений Azure можно автоматизировать процесс получения текущих данных конфигурации в виде отдельного файла. Затем на этапе сборки или развертывания этот файл можно внедрить в конвейер непрерывной интеграции и развертывания (CI/CD).

Ниже приведен пример того, как включить данные Конфигурации приложений в качестве этапа сборки для веб-приложения, представленного в кратких руководствах. Прежде чем продолжить, ознакомьтесь со статьей [Краткое руководство. Создание приложения ASP.NET Core с помощью службы "Конфигурация приложений Azure"](./quickstart-aspnet-core-app.md).

Вы можете выполнять шаги в этом учебнике с помощью любого редактора кода. [Visual Studio Code](https://code.visualstudio.com/) является отличным вариантом, который доступен на платформах Windows, macOS и Linux.

### <a name="prerequisites"></a>Предварительные требования

Если сборка выполняется локально, скачайте и установите [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), если этот компонент отсутствует.

Чтобы выполнить облачную сборку, например с помощью DevOps в Azure, убедитесь, что в системе сборки установлен [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="export-an-app-configuration-store"></a>Экспорт хранилища Конфигурации приложений

1. Откройте *CSPROJ*-файл и добавьте в него приведенный ниже сценарий.

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -d file --path $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    Добавьте значение *ConnectionString*, связанное с хранилищем Конфигурации приложений, как переменную среды.

2. Откройте файл *Program.cs* и обновите метод `CreateWebHostBuilder`, чтобы использовать экспортированный JSON-файл с помощью вызова метода `config.AddJsonFile()`.

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

### <a name="build-and-run-the-app-locally"></a>Создание и запуск приложения локально

1. Задайте переменную среды с именем **ConnectionString** и укажите для нее ключ доступа к хранилищу службы "Конфигурация приложений". Если вы используете командную строку Windows, выполните следующую команду и перезапустите командную строку, чтобы изменения вступили в силу:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Если вы используете Windows PowerShell, выполните следующую команду:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Если вы используете macOS или Linux, выполните следующую команду:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Чтобы создать приложение с помощью .NET Core CLI, выполните следующую команду в оболочке командной строки:

        dotnet build

3. Когда создание завершится, запустите веб-приложение локально с помощью следующей команды:

        dotnet run

4. Откройте окно браузера и перейдите по адресу `http://localhost:5000`, который является URL-адресом по умолчанию для веб-приложения, размещенного локально.

    ![Краткое руководство. Запуск приложения, размещенного локально](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы экспортировали данные Конфигурации приложений Azure для использования в конвейере развертывания. Чтобы узнать больше об использовании службы "Конфигурация приложений", перейдите к примерам скриптов Azure CLI.

> [!div class="nextstepaction"]
> [Интеграция управляемых удостоверений](./howto-integrate-azure-managed-service-identity.md)
