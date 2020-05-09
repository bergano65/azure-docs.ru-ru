---
title: Краткое руководство по использованию службы конфигурации приложений Azure с .NET Framework | Документация Майкрософт
description: Краткое руководство по использованию службы конфигурации приложений Azure с .NET Framework
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: lcozzens
ms.openlocfilehash: cfa89a7921751541d1044d697237946cd63cbfd8
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2020
ms.locfileid: "82732059"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Краткое руководство. Создание приложения на .NET Framework с помощью службы конфигурации приложений Azure

В этом кратком руководстве описано, как добавить службу конфигурации приложений Azure в консольное приложение .NET Framework, чтобы обеспечить централизованное хранение параметров приложения и управление ими отдельно от кода.

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.2](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Создание хранилища Конфигурации приложений

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Выберите **Обозреватель конфигураций** > **Создать** > **Ключ-значение**, чтобы добавить следующие пары "ключ-значение":

    | Клавиши | Значение |
    |---|---|
    | TestApp:Settings:FontSize | Данные из конфигурации приложения Azure |

    Поля **Метка** и **Тип контента** пока заполнять не нужно.

7. Нажмите кнопку **Применить**.

## <a name="create-a-net-console-app"></a>Создайте консольное приложение .NET

1. Запустите Visual Studio и выберите **Файл** > **Создать** > **Проект**.

1. В разделе **Создание проекта** с помощью фильтра найдите тип проекта **Консоль** и щелкните **Консольное приложение (.NET Framework)** . Выберите **Далее**.

1. В окне **Настроить новый проект** введите имя проекта. В разделе **Платформа** выберите **.NET Framework 4.7.1** или более поздней версии. Нажмите кнопку **создания**.

## <a name="connect-to-an-app-configuration-store"></a>Подключение к хранилищу Конфигурации приложений

1. Щелкните проект правой кнопкой мыши и выберите **Управление пакетами NuGet**. На вкладке **Обзор** найдите и добавьте в проект следующие пакеты NuGet.

    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 or later
    System.Configuration.ConfigurationManager version 4.6.0 or later
    ```

1. Обновите файл проекта *App.config*:

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

   Строка подключения к хранилищу службы "Конфигурация приложений" считывается из переменной среды `ConnectionString`. Добавьте построитель конфигураций `Environment` перед `MyConfigStore` в свойстве `configBuilders` раздела `appSettings`.

1. Откройте файл *Program.cs* и обновите метод `Main` для использования службы "Конфигурация приложений", вызвав метод `ConfigurationManager`.

    ```csharp
    static void Main(string[] args)
    {
        string message = System.Configuration.ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Создание и запуск приложения локально

1. Задайте переменную среды с именем **ConnectionString** для строки подключения к хранилищу службы "Конфигурация приложений". При использовании командной строки Windows выполните следующую команду:

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Если вы используете Windows PowerShell, выполните следующую команду:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```
1. Перезапустите Visual Studio, чтобы изменение вступило в силу. Нажмите клавиши CTRL+F5, чтобы скомпилировать и запустить консольное приложение.

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве описано, как создать хранилище службы "Конфигурация приложений" и использовать его с консольным приложением на основе .NET Framework. После запуска приложения значение `AppSettings` параметра `ConfigurationManager` не изменится. Однако в приложении .NET Framework также может использоваться библиотека поставщика конфигурации .NET Standard службы "Конфигурация приложений". Чтобы узнать, как включить в приложении .NET Framework динамическое обновление параметров конфигурации, перейдите к следующему учебнику.

> [!div class="nextstepaction"]
> [Включение динамической конфигурации](./enable-dynamic-configuration-dotnet.md)
