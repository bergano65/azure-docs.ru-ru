---
title: Краткое руководство по использованию службы "Конфигурация приложений Azure" с решением "Функции Azure"| Документация Майкрософт
description: Краткое руководство по использованию службы "Конфигурация приложений Azure" с решением "Функции Azure".
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: Azure Functions
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 5f28e213a5f824562df62a05b98f0f92f71bc591
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957442"
---
# <a name="quickstart-create-an-azure-function-with-app-configuration"></a>Краткое руководство. Создание функции Azure с использованием службы "Конфигурация приложений"

Конфигурация приложений Azure — это служба для управления конфигурациями в Azure. Она позволяет централизовано хранить и администрировать все конфигурации вашего приложения отдельно от кода. В этом кратком руководстве показано, как интегрировать эту службу в функцию Azure. 

Вы можете использовать любой редактор кода для выполнения шагов в этом кратком руководстве. Однако [Visual Studio Code](https://code.visualstudio.com/) является отличным вариантом, доступным на платформах Windows, MacOS и Linux.

![Полное руководство для локальной среды](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством установите [Visual Studio 2017](https://visualstudio.microsoft.com/vs) (и убедитесь, что рабочая нагрузка **разработки Azure** также установлена) и [последние инструменты решения "Функции Azure"](../azure-functions/functions-develop-vs.md#check-your-tools-version).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Создание хранилища конфигураций приложений

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-function-app"></a>Создание приложения-функции

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-app-configuration-store"></a>Подключение к хранилищу конфигураций приложений

1. Откройте файл *Function1.cs* и добавьте ссылку на поставщик конфигурации .NET Core для службы "Конфигурация приложений".

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

2. Измените метод `Run`, чтобы использовать службу "Конфигурация приложений", путем вызова `builder.AddAzureAppConfiguration()`.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));
        var config = builder.Build();
        string message = config["TestApp:Settings:Message"];
        message = message ?? req.Query["message"];

        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
        message = message ?? data?.message;

        return message != null
            ? (ActionResult) new OkObjectResult(message)
            : new BadRequestObjectResult("Please pass a message from a configuration store, on the query string or in the request body");
    }
    ```

## <a name="test-the-function-locally"></a>Локальное тестирование функции

1. Задайте переменную среды с именем **ConnectionString** и укажите для нее ключ доступа к хранилищу конфигураций приложений. Если вы используете командную строку Windows, выполните следующую команду и перезапустите командную строку, чтобы изменения вступили в силу:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    При использовании Windows PowerShell выполните следующую команду:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    При использовании macOS или Linux воспользуйтесь приведенной ниже командой:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Чтобы проверить работу функции, нажмите клавишу **F5**. Если будет предложено, примите запрос от Visual Studio на скачивание и установку **основных инструментов решения "Функции Azure" (CLI)**. Кроме того, вам может понадобиться включить исключение брандмауэра, чтобы инструменты могли обрабатывать HTTP-запросы.

3. Скопируйте URL-адрес функции из выходных данных среды выполнения функций Azure.

    ![Отладки рассматриваемой в этом кратком руководстве функции в VS](./media/quickstarts/function-visual-studio-debugging.png)

4. Вставьте URL-адрес запроса в адресную строку браузера. Ниже показан ответ в браузере на локальный запрос GET, возвращаемый функцией:

    ![Локальный запуск рассматриваемой в этом кратком руководстве функции](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы создали новое хранилище конфигураций приложений и использовали его с функцией Azure. См. дополнительные сведения об использовании службы конфигурации приложений в следующем руководстве, посвященном проверке подлинности.

> [!div class="nextstepaction"]
> [Управляемые удостоверения для интеграции с ресурсами Azure](./integrate-azure-managed-service-identity.md)
