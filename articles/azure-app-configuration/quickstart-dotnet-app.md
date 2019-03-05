---
title: Краткое руководство по использованию службы конфигурации приложений Azure с .NET Framework | Документация Майкрософт
description: Краткое руководство по использованию службы конфигурации приложений Azure с .NET Framework
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: b5e41b1f9ee982b8ff8c86232f715d5dab705cd6
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56962168"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Краткое руководство. Создание приложения на .NET Framework с помощью службы конфигурации приложений Azure

Конфигурация приложений Azure — это служба для управления конфигурациями в Azure. Она позволяет централизовано хранить и администрировать все конфигурации вашего приложения отдельно от кода. В этом кратком руководстве показано, как внедрить службу в классическое консольное приложении Windows на основе .NET Framework.

![Полное руководство для локальной среды](./media/quickstarts/dotnet-fx-app-run.png)

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством установите [Visual Studio 2017](https://visualstudio.microsoft.com/vs) и [.NET Framework 4.7.1](https://dotnet.microsoft.com/download) или более поздней версии, если это еще не сделано.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Создание хранилища конфигураций приложений

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-console-app"></a>Создайте консольное приложение .NET

1. Запустите Visual Studio и выберите **Файл** > **Создать** > **Проект**.

2. В диалоговом окне **Создание проекта** выберите **Установлено**, разверните **Visual C#** > **Классическое приложение для Windows**, выберите **Консольное приложение (.NET Framework)**, а затем введите **имя** проекта, выберите **.NET Framework 4.7.1** или более поздней версии и щелкните **OK**.

## <a name="connect-to-app-configuration-store"></a>Подключение к хранилищу конфигураций приложений

1. Щелкните проект правой кнопкой мыши и выберите **Управление пакетами NuGet**. На вкладке **Просмотр** найдите и добавьте в проект следующие пакеты NuGet (если их не удается найти, проверьте флажок **Включить предварительные версии**).
    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    ```

2. Обновите файл проекта *App.config*:

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" connectionString="${ConnectionString}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="ConnectionString" value ="Set via an environment variable - for example, dev, test, staging, or production connection string." />
    </appSettings>
    ```

   Обратите внимание, что строка подключения к хранилищу конфигураций приложений считывается из переменной среды `ConnectionString`, поэтому важно добавить построитель конфигурации `Environment` перед `MyConfigStore` в свойство `configBuilders` раздела `appSettings`.

3. Откройте файл *Program.cs* и обновите метод `Main` для использования службы "Конфигурация приложений", вызвав метод `ConfigurationManager`.

    ```csharp
    static void Main(string[] args)
    {
        string message = ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Создание и запуск приложения локально

1. Задайте переменную среды с именем **ConnectionString** для строки подключения к хранилищу конфигураций приложений. При использовании командной строки Windows выполните следующую команду:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    При использовании Windows PowerShell выполните следующую команду:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

2. Перезапустите Visual Studio, чтобы изменения вступили в силу, а затем нажмите клавиши **CTRL+F5**, чтобы создать и запустить консольное приложение.

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве описано, как создать хранилище конфигурации приложения и использовать его с консольным приложением на основе .NET Framework. См. дополнительные сведения об использовании службы конфигурации приложений в следующем руководстве, посвященном проверке подлинности.

> [!div class="nextstepaction"]
> [Управляемые удостоверения для интеграции с ресурсами Azure](./integrate-azure-managed-service-identity.md)
