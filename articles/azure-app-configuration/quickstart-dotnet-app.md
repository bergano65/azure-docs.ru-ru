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
ms.openlocfilehash: 33deea0805ffa89bcc6a64f34a97a4e080690da9
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60008823"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Краткое руководство. Создание приложения на .NET Framework с помощью службы конфигурации приложений Azure

Конфигурация приложений Azure — это служба для управления конфигурациями в Azure. С ее помощью вы можете централизовано хранить и администрировать все параметры приложения отдельно от кода. В этом кратком руководстве показано, как внедрить службу в классическое консольное приложении Windows на основе .NET Framework.

![Полное руководство для локальной среды](./media/quickstarts/dotnet-fx-app-run.png)

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством установите [Visual Studio 2017](https://visualstudio.microsoft.com/vs) и [.NET Framework 4.7.1](https://dotnet.microsoft.com/download) или более поздней версии, если это еще не сделано.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Создание хранилища конфигураций приложений

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Выберите **Обозреватель ключей и значений** > **+ Создать**, чтобы добавить следующие пары "ключ-значение":

    | Ключ | Значение |
    |---|---|
    | TestApp:Settings:FontSize | Данные из конфигурации приложения Azure |

    Поля **Метка** и **Тип контента** пока заполнять не нужно.

## <a name="create-a-net-console-app"></a>Создайте консольное приложение .NET

1. Запустите Visual Studio и выберите **Файл** > **Создать** > **Проект**.

2. В разделе **Создание проекта** выберите **Установлено** > **Visual C#** > **Классическое приложение для Windows**. Выберите **Консольное приложение (.NET Framework)** и введите имя для проекта. Выберите **.NET Framework 4.7.1** или более поздней версии, а затем нажмите кнопку **ОК**.

## <a name="connect-to-an-app-configuration-store"></a>Подключение к хранилищу конфигураций приложений

1. Щелкните проект правой кнопкой мыши и выберите **Управление пакетами NuGet**. На вкладке **Обзор** найдите и добавьте в проект следующие пакеты NuGet. Если вы не можете их найти, установите флажок **Включить предварительные выпуски**.

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

   Строка подключения к хранилищу конфигураций приложений считывается из переменной среды `ConnectionString`. Добавьте построитель конфигураций `Environment` перед `MyConfigStore` в свойстве `configBuilders` раздела `appSettings`.

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

    Если вы используете Windows PowerShell, выполните следующую команду:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

2. Перезапустите Visual Studio, чтобы изменение вступило в силу. Нажмите клавиши CTRL+F5, чтобы скомпилировать и запустить консольное приложение.

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве описано, как создать хранилище конфигурации приложения и использовать его с консольным приложением на основе .NET Framework. Ознакомьтесь с дополнительными сведениями об использовании службы "Конфигурация приложений" в следующем учебнике, посвященном проверке подлинности.

> [!div class="nextstepaction"]
> [Руководство по интеграции с управляемыми удостоверениями Azure](./howto-integrate-azure-managed-service-identity.md)
