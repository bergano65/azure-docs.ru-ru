---
title: Краткое руководство по использованию службы "Конфигурация приложений Azure" с .NET Core | Документация Майкрософт
description: Краткое руководство по использованию службы "Конфигурация приложений Azure" с приложениями .NET Core
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET Core
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 986bda4ab95985820e61c93bbe82431fb50c983b
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185014"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Краткое руководство. Создание приложения .NET Core с помощью службы "Конфигурация приложений"

В этом кратком руководстве описано, как добавить службу "Конфигурация приложений Azure" в консольное приложение .NET Core, чтобы обеспечить централизованное хранение параметров приложения и управление ими отдельно от кода.

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
- [Базовый пакет SDK для .NET](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Создание хранилища Конфигурации приложений

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Выберите **Configuration Explorer** (Обозреватель конфигураций)  >  **+ Создать**, чтобы добавить следующие пары "ключ-значение".

    | Ключ | Значение |
    |---|---|
    | TestApp:Settings:FontSize | Данные из конфигурации приложения Azure |

    Поля **Метка** и **Тип контента** пока заполнять не нужно.

## <a name="create-a-net-core-console-app"></a>Создание консольного приложения .NET Core

Чтобы создать проект консольного приложения .NET Core, вы будете использовать [интерфейс командной строки .NET Core](https://docs.microsoft.com/dotnet/core/tools/). Преимущество использования .NET Core CLI по сравнению с Visual Studio заключается в том, что он доступен на платформах Windows, macOS и Linux.

1. Создайте новый каталог для своего проекта

2. В новой папке выполните следующую команду, чтобы создать проект консольного приложения для ASP.NET Core:

        dotnet new console

## <a name="connect-to-an-app-configuration-store"></a>Подключение к хранилищу Конфигурации приложений

1. Добавьте ссылку на пакет NuGet `Microsoft.Extensions.Configuration.AzureAppConfiguration`, выполнив следующую команду:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration --version 2.0.0-preview-009470001-1371

2. Выполните следующую команду, чтобы восстановить пакеты проекта:

        dotnet restore

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

1. Задайте переменную среды с именем **ConnectionString** и укажите для нее ключ доступа к хранилищу службы "Конфигурация приложений". Если вы используете командную строку Windows, выполните следующую команду и перезапустите командную строку, чтобы изменения вступили в силу:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Если вы используете Windows PowerShell, выполните следующую команду:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Если вы используете macOS или Linux, выполните следующую команду:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Чтобы создать консольное приложение, выполните следующую команду:

        dotnet build

3. Когда создание завершится, запустите приложение локально с помощью следующей команды:

        dotnet run

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Дополнительная информация

В рамках этого краткого руководства вы создали хранилище Конфигурации приложений и использовали его с консольным приложением .NET Core с помощью [поставщика Конфигурации приложений](https://go.microsoft.com/fwlink/?linkid=2074664). Чтобы узнать, как настроить приложение .NET Core для динамического обновления параметров конфигурации, перейдите к следующему учебнику.

> [!div class="nextstepaction"]
> [Включение динамической конфигурации](./enable-dynamic-configuration-dotnet-core.md)
