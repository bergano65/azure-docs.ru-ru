---
title: Краткое руководство по добавлению флагов функций в приложения .NET Framework | Документация Майкрософт | Документация Майкрософт
description: Краткое руководство по добавлению флагов функций в приложения .NET Framework и управлению ими в Конфигурации приложений Azure
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: cba0a9a003b53eeb102e14c00cb91f344ebbe3d6
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821881"
---
# <a name="quickstart-add-feature-flags-to-a-net-framework-app"></a>Краткое руководство. Добавление флагов функций в приложение .NET Framework

В этом кратком руководстве описано, как внедрить Конфигурацию приложений Azure в приложение .NET Framework, чтобы реализовать комплексное управление функциями. Вы можете использовать службу "Конфигурация приложений" для централизованного хранения всех флагов функций и управления их состояниями. 

Библиотеки управления функциями .NET расширяют возможности платформы за счет всесторонней поддержки флагов функций. Эти библиотеки создаются на основе системы конфигурации .NET. Они легко интегрируются с Конфигурацией приложений посредством поставщика конфигураций .NET.

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.2](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Создание хранилища конфигураций приложений

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-console-app"></a>Создайте консольное приложение .NET

1. Запустите Visual Studio и выберите **Файл** > **Создать** > **Проект**.

1. В разделе **Создание проекта** с помощью фильтра найдите тип проекта **Консоль** и щелкните **Консольное приложение (.NET Framework)** . Щелкните **Далее**.

1. В окне **Настроить новый проект** введите имя проекта. В разделе **Платформа** выберите **.NET Framework 4.7.1** или более поздней версии. Нажмите кнопку **Создать**.

## <a name="connect-to-an-app-configuration-store"></a>Подключение к хранилищу конфигураций приложений

1. Щелкните проект правой кнопкой мыши и выберите **Управление пакетами NuGet**. На вкладке **Обзор** найдите и добавьте в проект следующие пакеты NuGet. Если вы не можете их найти, установите флажок **Включить предварительные выпуски**.

    ```
    Microsoft.Extensions.DependencyInjection
    Microsoft.Extensions.Configuration.AzureAppConfiguration
    Microsoft.FeatureManagement
    ```

1. Откройте файл *Program.cs* и добавьте следующие инструкции:

    ```csharp
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    using Microsoft.FeatureManagement;
    ```

1. Обновите метод `Main` для подключения к Конфигурации приложения, указав параметр `UseFeatureFlags`, чтобы получить флаги функции. Затем отобразите сообщение, если флаг функции `Beta` включен.

    ```csharp
        static void Main(string[] args)
        {
            IConfigurationRoot configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options => 
                { 
                    options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                           .UseFeatureFlags(); 
                }).Build();
            
            IServiceCollection services = new ServiceCollection(); 
            services.AddSingleton<IConfiguration>(configuration).AddFeatureManagement(); 
            IFeatureManager featureManager = services.BuildServiceProvider().GetRequiredService<IFeatureManager>(); 
            
            if (featureManager.IsEnabled("Beta")) 
            { 
                Console.WriteLine("Welcome to the beta"); 
            }

            Console.WriteLine("Hello World!");
        }
    ```

## <a name="build-and-run-the-app-locally"></a>Создание и запуск приложения локально

1. Задайте переменную среды с именем **ConnectionString** для строки подключения к хранилищу конфигураций приложений. При использовании командной строки Windows выполните следующую команду:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Если вы используете Windows PowerShell, выполните следующую команду:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. Перезапустите Visual Studio, чтобы изменение вступило в силу. 

1. Нажмите клавиши CTRL+F5, чтобы скомпилировать и запустить консольное приложение.

    ![Приложение с включенным флагом функций](./media/quickstarts/dotnet-app-feature-flag.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы создали флаг функции в Конфигурации приложений и использовали его с консольным приложением .NET Framework. Сведения о динамическом обновлении флагов функций и других значениях конфигурации без перезапуска приложения, см. в следующем руководстве.

> [!div class="nextstepaction"]
> [Включение динамической конфигурации](./enable-dynamic-configuration-dotnet.md)