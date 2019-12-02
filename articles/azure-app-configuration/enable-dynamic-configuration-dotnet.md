---
title: Руководство по использованию динамической конфигурации службы Конфигурация приложений Azure в приложении .NET Framework | Документация Майкрософт
description: Из этого руководства вы узнаете, как динамически обновлять данные конфигурации для приложений .NET Framework.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: 7cb76d5836055ce352373fa13449e27d81e84022
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185240"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>Руководство по использованию динамической конфигурации в приложении .NET Framework

Клиентская библиотека .NET для Конфигурации приложений поддерживает обновление набора параметров конфигурации по запросу без перезапуска приложения. Это можно реализовать, сначала получив экземпляр `IConfigurationRefresher` из параметров для поставщика конфигурации, а затем вызвав `Refresh` на этом экземпляре, где угодно в коде.

Чтобы поддерживать параметры в актуальном состоянии и избегать слишком большого количества обращений к хранилищу конфигураций, для каждого параметра используется кэш. До истечения срока действия кэшированного значения, операция обновления не выполняет обновление значения, даже если значение изменилось в хранилище конфигураций. Стандартное время истечения для каждого запроса составляет 30 секунд, но при необходимости его можно переопределить.

Из этого руководства вы узнаете, как реализовать динамические обновления конфигурации в коде. При этом в качестве основы используется код приложения, представленный в кратких руководствах. Прежде чем продолжить, ознакомьтесь со статьей [Краткое руководство. Создание приложения на .NET Framework с помощью службы конфигурации приложений Azure](./quickstart-dotnet-app.md).

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * настройка приложения .NET Framework на обновление конфигурации при изменении данных в хранилище службы "Конфигурация приложений";
> * внедрение последней конфигурации в приложение.
## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.1 или более поздней версии](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Создание хранилища Конфигурации приложений

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Выберите **Configuration Explorer** (Обозреватель конфигураций)  >  **+ Создать**, чтобы добавить следующие пары "ключ-значение".

    | Ключ | Значение |
    |---|---|
    | TestApp:Settings:FontSize | Данные из конфигурации приложения Azure |

    Поля **Метка** и **Тип контента** пока заполнять не нужно.

## <a name="create-a-net-framework-console-app"></a>Создание консольного приложения .NET Framework

1. Запустите Visual Studio и выберите **Файл** > **Создать** > **Проект**.

1. В разделе **Создание проекта** с помощью фильтра найдите тип проекта **Консоль** и щелкните **Консольное приложение (.NET Framework)** . Щелкните **Далее**.

1. В окне **Настроить новый проект** введите имя проекта. В разделе **Платформа** выберите **.NET Framework 4.7.1** или более поздней версии. Нажмите кнопку **Создать**.

## <a name="reload-data-from-app-configuration"></a>Перезагрузка данных из App Configuration
1. Щелкните проект правой кнопкой мыши и выберите **Управление пакетами NuGet**. На вкладке **Обзор** найдите и добавьте пакет NuGet *Microsoft.Extensions.Configuration.AzureAppConfiguration* в свой проект. Если найти не удается, установите флажок **Включить предварительные выпуски**.

1. Откройте файл *Program.cs* и добавьте ссылку на поставщик конфигурации приложений .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Добавьте две переменные для хранения объектов, связанных с конфигурацией.

    ```csharp
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;
    ```

1. Обновите метод `Main`, чтобы подключиться к Конфигурации приложений с помощью указанных параметров обновления.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                    .ConfigureRefresh(refresh =>
                    {
                        refresh.Register("TestApp:Settings:Message")
                            .SetCacheExpiration(TimeSpan.FromSeconds(10));
                    });

            _refresher = options.GetRefresher();
        });

        _configuration = builder.Build();
        PrintMessage().Wait();
    }
    ```
    Метод `ConfigureRefresh` используется, чтобы указать параметры, используемые для обновления данных конфигурации в хранилище службы "Конфигурация приложений" при запуске операции обновления. Экземпляр `IConfigurationRefresher` можно получить, вызвав метод `GetRefresher` в параметрах, предоставленных методу `AddAzureAppConfiguration`, а метод `Refresh` в этом экземпляре можно использовать для запуска операции обновления в любом месте вашего кода.

    > [!NOTE]
    > Стандартное время истечения срока действия кэша для параметра конфигурации составляет 30 секунд, но его можно переопределить, вызвав метод `SetCacheExpiration` в инициализаторе параметров, переданном в качестве аргумента методу `ConfigureRefresh`.

1. Добавьте метод с именем `PrintMessage()`, который запускает ручное обновление данных конфигурации из Конфигурации приложений.

    ```csharp
    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.Refresh();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Создание и запуск приложения локально

1. Задайте переменную среды с именем **ConnectionString** и укажите для нее ключ доступа к хранилищу службы "Конфигурация приложений". Если вы используете командную строку Windows, выполните следующую команду и перезапустите командную строку, чтобы изменения вступили в силу:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Если вы используете Windows PowerShell, выполните следующую команду:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. Перезапустите Visual Studio, чтобы изменение вступило в силу. 

1. Нажмите клавиши CTRL+F5, чтобы скомпилировать и запустить консольное приложение.

    ![Локальный запуск приложения](./media/dotnet-app-run.png)

1. Войдите на [портале Azure](https://portal.azure.com). Щелкните **Все ресурсы** и выберите экземпляр хранилища Конфигурации приложений, который вы создали по инструкциям из краткого руководства.

1. Выберите **Configuration Explorer** (Обозреватель конфигураций) и измените значения следующих ключей.

    | Ключ | Значение |
    |---|---|
    | TestApp:Settings:FontSize | Данные из конфигурации приложений Azure. Обновлено |

1. Вернувшись в запущенное приложение, нажмите клавишу Enter, чтобы запустить обновление и вывести обновленное значение в окне командной строки или PowerShell.

    ![Локальное обновление приложения](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > Поскольку время истечения срока действия кэша было установлено равным 10 секундам с использованием метода `SetCacheExpiration` при указании конфигурации для операции обновления, то значение параметра конфигурации будет обновляться только в том случае, если с момента последнего обновления этого параметра прошло не менее 10 секунд.

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Дополнительная информация

В рамках этого учебника вы включили в приложении .NET Framework динамическое обновление параметров конфигурации из службы "Конфигурация приложения". Чтобы узнать, как с помощью удостоверения, управляемого Azure, упростить доступ к службе "Конфигурация приложений Azure", перейдите к следующему учебнику.

> [!div class="nextstepaction"]
> [Руководство по интеграции с управляемыми удостоверениями Azure](./howto-integrate-azure-managed-service-identity.md)
