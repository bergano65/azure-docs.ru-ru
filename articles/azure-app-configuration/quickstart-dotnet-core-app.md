---
title: Краткое руководство по использованию службы "Конфигурация приложений Azure" с .NET Core | Документация Майкрософт
description: Краткое руководство по использованию службы "Конфигурация приложений Azure" с приложениями .NET Core
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 1/9/2019
ms.author: lcozzens
ms.openlocfilehash: 7cabe5b0564ec63335800a999bebec67ec970587
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85856782"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Краткое руководство. Создание приложения .NET Core с помощью службы "Конфигурация приложений"

В этом кратком руководстве описано, как добавить службу "Конфигурация приложений Azure" в консольное приложение .NET Core, чтобы обеспечить централизованное хранение параметров приложения и управление ими отдельно от кода.

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
- [Пакет SDK для .NET Core](https://dotnet.microsoft.com/download) также доступен в [Azure Cloud Shell](https://shell.azure.com).

## <a name="create-an-app-configuration-store"></a>Создание хранилища Конфигурации приложений

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Выберите **Обозреватель конфигураций** > **Создать** > **Ключ-значение**, чтобы добавить следующие пары "ключ-значение":

    | Клавиши | Значение |
    |---|---|
    | TestApp:Settings:FontSize | Данные из конфигурации приложения Azure |

    Поля **Метка** и **Тип контента** пока заполнять не нужно.

7. Нажмите кнопку **Применить**.

## <a name="create-a-net-core-console-app"></a>Создание консольного приложения .NET Core

Чтобы создать проект консольного приложения .NET Core, вы будете использовать [интерфейс командной строки .NET Core](https://docs.microsoft.com/dotnet/core/tools/). Преимущество использования .NET Core CLI по сравнению с Visual Studio заключается в том, что он доступен на платформах Windows, macOS и Linux.  Кроме того, можно использовать предварительно установленные средства, доступные в [Azure Cloud Shell](https://shell.azure.com).

1. Создайте новый каталог для своего проекта

2. В новой папке выполните следующую команду, чтобы создать проект консольного приложения для ASP.NET Core:

    ```dotnetcli
    dotnet new console
    ```

## <a name="connect-to-an-app-configuration-store"></a>Подключение к хранилищу Конфигурации приложений

1. Добавьте ссылку на пакет NuGet `Microsoft.Extensions.Configuration.AzureAppConfiguration`, выполнив следующую команду:

    ```dotnetcli
    dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration
    ```

2. Выполните следующую команду, чтобы восстановить пакеты проекта:

    ```dotnetcli
    dotnet restore
    ```

3. Откройте файл *Program.cs* и добавьте ссылку на поставщик конфигурации приложений .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. Обновите метод `Main`, чтобы использовать службу "Конфигурация приложений", путем вызова метода `builder.AddAzureAppConfiguration()`.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));

        var config = builder.Build();
        Console.WriteLine(config["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Создание и запуск приложения локально

1. Задайте переменную среды с именем **ConnectionString** и укажите для нее ключ доступа к хранилищу службы "Конфигурация приложений". В командной строке выполните следующую команду:

    ```cmd
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Если вы используете Windows PowerShell, выполните следующую команду:

    ```azurepowershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Если вы используете macOS или Linux, выполните следующую команду:

    ```console
    export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

    Перезапустите командную строку, чтобы изменение вступило в силу. Выведите значение переменной среды, чтобы проверить правильность ее настройки.

2. Чтобы создать консольное приложение, выполните следующую команду:

    ```dotnetcli
    dotnet build
    ```

3. Когда создание завершится, запустите приложение локально с помощью следующей команды:

    ```dotnetcli
    dotnet run
    ```

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Дальнейшие действия

В рамках этого краткого руководства вы создали хранилище Конфигурации приложений и использовали его с консольным приложением .NET Core с помощью [поставщика Конфигурации приложений](https://go.microsoft.com/fwlink/?linkid=2074664). Чтобы узнать, как настроить приложение .NET Core для динамического обновления параметров конфигурации, перейдите к следующему учебнику.

> [!div class="nextstepaction"]
> [Включение динамической конфигурации](./enable-dynamic-configuration-dotnet-core.md)
