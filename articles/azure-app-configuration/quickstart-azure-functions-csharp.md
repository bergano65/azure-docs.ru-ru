---
title: Краткое руководство по использованию службы "Конфигурация приложений Azure" с решением "Функции Azure"| Документация Майкрософт
description: В этом кратком руководстве показано, как создать приложение Функций Azure с помощью службы "Конфигурация приложений Azure"и языка C#. Создание и подключение к хранилищу Конфигурации приложений. Локальное тестирование функции.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: lcozzens
ms.openlocfilehash: 7d4fe0f54ecd11c197f258d190e9eaef14bf2bb7
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91459929"
---
# <a name="quickstart-create-an-azure-functions-app-with-azure-app-configuration"></a>Краткое руководство. Создание приложения Функций Azure с использованием службы "Конфигурация приложений Azure"

В этом кратком руководстве описано, как добавить службу "Конфигурация приложений Azure" в приложение Функций Azure, чтобы обеспечить централизованное хранение всех параметров приложения и управление ими отдельно от кода.

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/dotnet).
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) с рабочей нагрузкой **разработки Azure**.
- [Средства функций Azure](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Создание хранилища Конфигурации приложений

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Выберите **Обозреватель конфигураций** >  **+ Создать** > **Ключ-значение**, чтобы добавить следующие пары "ключ-значение":

    | Клавиши | Значение |
    |---|---|
    | TestApp:Settings:FontSize | Данные из конфигурации приложения Azure |

    Поля **Метка** и **Тип контента** пока заполнять не нужно.

7. Нажмите кнопку **Применить**.

## <a name="create-a-functions-app"></a>Создание приложения-функции

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Подключение к хранилищу Конфигурации приложений

1. Щелкните проект правой кнопкой мыши и выберите **Управление пакетами NuGet**. На вкладке **Обзор** найдите и добавьте в проект пакет NuGet `Microsoft.Extensions.Configuration.AzureAppConfiguration`. Если найти не удается, установите флажок **Включить предварительные выпуски**.

2. Откройте *Function1.cs* и добавьте пространства имен поставщика конфигурации .NET Core и конфигурации службы "Конфигурация приложений".

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

3. Добавьте свойство `static` с именем `Configuration`, чтобы создать отдельный экземпляр `IConfiguration`. Затем добавьте конструктор `static` для подключения к службе "Конфигурация приложения", вызвав `AddAzureAppConfiguration()`. Конфигурация будет загружена один раз при запуске приложения. Позже один и тот же экземпляр конфигурации будет использоваться для всех вызовов функций.

    ```csharp
    private static IConfiguration Configuration { set; get; }

    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));
        Configuration = builder.Build();
    }
    ```

4. Обновите метод `Run`, чтобы считать значения из конфигурации.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        string keyName = "TestApp:Settings:Message";
        string message = Configuration[keyName];

        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>Локальное тестирование функции

1. Задайте переменную среды с именем **ConnectionString** и укажите для нее ключ доступа к хранилищу службы "Конфигурация приложений". Если вы используете командную строку Windows, выполните следующую команду и перезапустите командную строку, чтобы изменения вступили в силу:

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Если вы используете Windows PowerShell, выполните следующую команду:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Если вы используете macOS или Linux, выполните следующую команду:

    ```bash
        export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. Чтобы проверить работу функции, нажмите клавишу F5. Если будет предложено, примите запрос от Visual Studio на скачивание и установку **основных инструментов решения "Функции Azure" (CLI)**. Кроме того, возможно, вам понадобиться включить исключение брандмауэра, чтобы инструменты могли обрабатывать HTTP-запросы.

3. Скопируйте URL-адрес функции из выходных данных среды выполнения функций Azure.

    ![Отладки рассматриваемой в этом кратком руководстве функции в VS](./media/quickstarts/function-visual-studio-debugging.png)

4. Вставьте URL-адрес для HTTP-запроса в адресной строке браузера. На изображении ниже показан ответ в браузере на локальный запрос GET, возвращаемый функцией.

    ![Локальный запуск рассматриваемой в этом кратком руководстве функции](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве вы создали хранилище службы "Конфигурация приложений" и использовали его с приложением Функций Azure с помощью [поставщика Конфигурации приложений](https://go.microsoft.com/fwlink/?linkid=2074664). Чтобы узнать, как настроить приложение Функций Azure для динамического обновления параметров конфигурации, перейдите к следующему учебнику.

> [!div class="nextstepaction"]
> [Включение динамической конфигурации](./enable-dynamic-configuration-azure-functions-csharp.md)
