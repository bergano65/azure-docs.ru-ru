---
title: Руководство по использованию динамической конфигурации службы "Конфигурация приложений Azure" в приложении Функций Azure | Документация Майкрософт
description: Из этого учебника вы узнаете, как динамически обновлять данные конфигурации для приложений Функций Azure.
services: azure-app-configuration
documentationcenter: ''
author: zhenlan
manager: qingye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/17/2019
ms.author: zhenlwa
ms.custom: azure-functions
ms.tgt_pltfrm: Azure Functions
ms.openlocfilehash: ba70d5f186c1424b2019716ab7a87aeae85f8913
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74187298"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>Руководство по использованию динамической конфигурации в приложении Функций Azure

Конфигурация .NET Standard службы "Конфигурация приложений" поддерживает кэширование и обновление, динамически управляемое активностью приложения. Из этого руководства вы узнаете, как реализовать динамические обновления конфигурации в коде. При этом в качестве основы используется код приложения Функций Azure, представленный в кратких руководствах. Прежде чем продолжить, ознакомьтесь со статьей [Краткое руководство. Создание приложения Функций Azure с помощью службы "Конфигурация приложений Azure"](./quickstart-azure-functions-csharp.md).

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * настройка в приложении Функций Azure для обновления конфигурации при изменении данных в хранилище службы "Конфигурации приложений";
> * добавление последней конфигурации в вызовы Функций Azure.

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) с рабочей нагрузкой **разработки Azure**.
- [Инструменты Функций Azure](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- Ознакомьтесь со статьей [Краткое руководство. Создание приложения Функций Azure с помощью службы "Конфигурация приложений Azure"](./quickstart-azure-functions-csharp.md).

## <a name="reload-data-from-app-configuration"></a>Перезагрузка данных из App Configuration

1. Откройте *Function1.cs*. В дополнение к свойству `static` `Configuration` добавьте новое свойство `static` `ConfigurationRefresher`, чтобы сохранить отдельный экземпляр `IConfigurationRefresher`, который будет использоваться для оповещения об обновлениях конфигурации при последующих вызовах функций.

    ```csharp
    private static IConfiguration Configuration { set; get; }
    private static IConfigurationRefresher ConfigurationRefresher { set; get; }
    ```

2. Обновите конструктор и используйте метод `ConfigureRefresh`, чтобы указать параметр, который необходимо обновить из хранилища службы "Конфигурация приложений". Экземпляр `IConfigurationRefresher` извлекается с помощью метода `GetRefresher`. При необходимости мы также можем изменить время истечения срока действия кэша конфигурации на 1 минуту с 30 секунд по умолчанию.

    ```csharp
    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   .ConfigureRefresh(refreshOptions =>
                        refreshOptions.Register("TestApp:Settings:Message")
                                      .SetCacheExpiration(TimeSpan.FromSeconds(60))
            );
            ConfigurationRefresher = options.GetRefresher();
        });
        Configuration = builder.Build();
    }
    ```

3. Обновите метод `Run` и оповещение об обновлениях конфигурации, используя метод `Refresh` в начале вызова функций. Это не будет работать, если время истечения срока действия кэша не достигнуто. Удалите оператор `await`, если вы предпочитаете, чтобы конфигурация обновлялась без блокировки.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await ConfigurationRefresher.Refresh();

        string keyName = "TestApp:Settings:Message";
        string message = Configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>Локальное тестирование функции

1. Задайте переменную среды с именем **ConnectionString** и укажите для нее ключ доступа к хранилищу конфигураций приложений. Если вы используете командную строку Windows, выполните следующую команду и перезапустите командную строку, чтобы изменения вступили в силу:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Если вы используете Windows PowerShell, выполните следующую команду:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Если вы используете macOS или Linux, выполните следующую команду:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Чтобы проверить работу функции, нажмите клавишу F5. Если будет предложено, примите запрос от Visual Studio на скачивание и установку **основных инструментов решения "Функции Azure" (CLI)** . Кроме того, возможно, вам понадобиться включить исключение брандмауэра, чтобы инструменты могли обрабатывать HTTP-запросы.

3. Скопируйте URL-адрес функции из выходных данных среды выполнения функций Azure.

    ![Отладки рассматриваемой в этом кратком руководстве функции в VS](./media/quickstarts/function-visual-studio-debugging.png)

4. Вставьте URL-адрес запроса в адресную строку браузера. На изображении ниже показан ответ в браузере на локальный запрос GET, возвращаемый функцией.

    ![Локальный запуск рассматриваемой в этом кратком руководстве функции](./media/quickstarts/dotnet-core-function-launch-local.png)

5. Войдите на [портале Azure](https://portal.azure.com). Щелкните **Все ресурсы** и выберите экземпляр хранилища Конфигурации приложений, который вы создали по инструкциям из краткого руководства.

6. Выберите **Обозреватель конфигураций** и измените значения следующего ключа.

    | Ключ | Значение |
    |---|---|
    | TestApp:Settings:FontSize | Данные из конфигурации приложений Azure. Обновлено |

7. Обновите приложение браузера несколько раз. Если истечение срока действия кэша происходит через минуту, на странице будет отображаться ответ на вызов функций с обновленным значением.

    ![Локальное обновление рассматриваемой в этом кратком руководстве функции](./media/quickstarts/dotnet-core-function-refresh-local.png)

Пример кода, используемый в этом учебнике, можно скачать в [репозитории GitHub](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction).

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Дополнительная информация

В рамках этого учебника вы включили в приложении Функций Azure динамическое обновление параметров конфигурации из службы "Конфигурация приложения". Чтобы узнать, как с помощью удостоверения, управляемого Azure, упростить доступ к службе "Конфигурация приложений Azure", перейдите к следующему учебнику.

> [!div class="nextstepaction"]
> [Руководство по интеграции с управляемыми удостоверениями Azure](./howto-integrate-azure-managed-service-identity.md)
