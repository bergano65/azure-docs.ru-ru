---
title: Создание определения OpenAPI для бессерверного API с помощью Управления API
description: Создание определения OpenAPI, которое позволяет другим приложениям и службам вызывать функцию в Azure.
ms.topic: tutorial
ms.date: 05/08/2019
ms.reviewer: sunayv
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 659c05b3d31f5673e95cb27f10eaa8bd872e4be6
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226820"
---
# <a name="create-an-openapi-definition-for-a-serverless-api-using-azure-api-management"></a>Создание определения OpenAPI для бессерверного API с помощью Управления API

Интерфейсы REST API часто описываются с использованием определения OpenAPI. Это определение содержит сведения о доступных операция в API и о том, как необходимо структурировать данные запросов и ответов для API.

В этом руководстве вы создадите функцию, определяющую экономичность аварийного ремонта ветроэлектрической установки. Затем создадите определение OpenAPI для приложения-функции с помощью [Управления API Azure](../api-management/api-management-key-concepts.md), чтобы иметь возможность вызвать функцию из приложений и служб.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * создавать функции в Azure;
> * создавать определения OpenAPI с помощью Управления API Azure;
> * проверять определения путем вызова функции.
> * скачивать определение OpenAPI.

## <a name="create-a-function-app"></a>Создание приложения-функции

Для выполнения функций вам понадобится приложение-функция, позволяющее группировать функции в логические единицы и упростить развертывание, масштабирование и совместное использование ресурсов, а также управление ими.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-the-function"></a>Создание функции

В этом руководстве используется функция, активируемая HTTP, которая принимает два параметра:

* предполагаемое время ремонта турбин (в часах);
* производительность турбин (в киловаттах). 

Затем функция вычисляет стоимость ремонта и доход от турбины за 24 часа. Чтобы создать функцию, активируемую HTTP, на [портале Azure](https://portal.azure.com) выполните следующие действия.

1. Разверните приложение-функцию и нажмите кнопку **+** рядом с элементом **Функции**. Последовательно выберите **На портале** > **Продолжить**.

1. Щелкните **Другие шаблоны…** , а затем выберите **Завершить и просмотреть шаблоны**.

1. Выберите триггер HTTP, введите `TurbineRepair` для **имени** функции, выберите `Function` в качестве **[уровня аутентификации](functions-bindings-http-webhook.md#http-auth)** , а затем нажмите кнопку **Создать**.  

    ![Создание функции HTTP для OpenAPI](media/functions-openapi-definition/select-http-trigger-openapi.png)

1. Замените содержимое файла сценария C# (run.csx) следующим кодом и выберите **Сохранить**.

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    const double revenuePerkW = 0.12;
    const double technicianCost = 250;
    const double turbineCost = 100;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        // Get query strings if they exist
        int tempVal;
        int? hours = Int32.TryParse(req.Query["hours"], out tempVal) ? tempVal : (int?)null;
        int? capacity = Int32.TryParse(req.Query["capacity"], out tempVal) ? tempVal : (int?)null;
    
        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
    
        // Use request body if a query was not sent
        capacity = capacity ?? data?.capacity;
        hours = hours ?? data?.hours;
    
        // Return bad request if capacity or hours are not passed in
        if (capacity == null || hours == null){
            return new BadRequestObjectResult("Please pass capacity and hours on the query string or in the request body");
        }
        // Formulas to calculate revenue and cost
        double? revenueOpportunity = capacity * revenuePerkW * 24;  
        double? costToFix = (hours * technicianCost) +  turbineCost;
        string repairTurbine;
    
        if (revenueOpportunity > costToFix){
            repairTurbine = "Yes";
        }
        else {
            repairTurbine = "No";
        };
    
        return (ActionResult)new OkObjectResult(new{
            message = repairTurbine,
            revenueOpportunity = "$"+ revenueOpportunity,
            costToFix = "$"+ costToFix
        });
    }
    ```

    Этот код функции возвращает сообщение `Yes` или `No`, чтобы указать, является ли аварийный ремонт экономично выгодным, а также возможности получения дохода, которые предоставляет турбина, и стоимость ремонта турбины.

1. Чтобы проверить функцию, щелкните **Тест** в правой области сверху. После этого откроется вкладка "Тест". В разделе **Текст запроса** введите указанное ниже значение, а затем нажмите кнопку **Запуск**.

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    ![Проверка функции на портале Azure](media/functions-openapi-definition/test-function.png)

    В тексте ответа возвратится следующее значение.

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

Теперь у вас есть функция, определяющая экономичность аварийного ремонта. Далее необходимо создать определение OpenAPI для приложения-функции.

## <a name="generate-the-openapi-definition"></a>Создание определения OpenAPI

Теперь можно приступить к созданию определения OpenAPI.

1. Выберите приложение-функцию, после чего в разделе **Функции платформы** выберите **Управления API** и щелкните **Создать** в разделе **Управление API**.

    ![Пункт меню "Управление API" в разделе "Функции платформы"](media/functions-openapi-definition/select-all-settings-openapi.png)

1. Затем используйте параметры Управления API, указанные в таблице под изображением.

    ![Создание службы "Управление API"](media/functions-openapi-definition/new-apim-service-openapi.png)

    | Параметр      | Рекомендуемое значение  | Описание                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Имя** | Глобально уникальное имя | Имя создается на основе имени приложения-функции. |
    | **подписка** | Ваша подписка | Подписка, в которой создан ресурс. |  
    | **[Группа ресурсов](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Та же группа, в которую входит ваше приложение-функция. Значение должно быть установлено. |
    | **Местоположение.** | Запад США | Для расположения выберите западную часть США. |
    | **Название организации** | Contoso | Название организации, используемое на портале разработчика и для уведомлений по электронной почте. |
    | **Адрес электронной почты администратора** | Ваш адрес электронной почты | Адрес электронной почты, на которую отправляются системные уведомления от службы "Управление API". |
    | **Ценовая категория** | Потребление (предварительная версия) | Уровень потребления предлагается в предварительной версии и доступен не во всех регионах. Подробные сведения о ценах на Управление API см. на [этой странице](https://azure.microsoft.com/pricing/details/api-management/). |

1. Щелкните **Создать**, чтобы создать экземпляр Управления API. Этот процесс может занять несколько минут.

1. Выберите **Включить Application Insights**, чтобы отправлять журналы в то же расположение, где находится приложение-функция, а затем примите оставшиеся значения по умолчанию и выберите **Привязать API**.

1. Откроется страница **Импорт функций Azure** с выделенной функцией **TurbineRepair**. Нажмите кнопку **Выбрать**, чтобы продолжить.

    ![Импорт Функций Azure в Управление API](media/functions-openapi-definition/import-function-openapi.png)

1. На странице **Создание из приложения-функции** примите значения по умолчанию и щелкните **Создать**.

    ![Создание из приложения-функции](media/functions-openapi-definition/create-function-openapi.png)

Теперь для функции создан API.

## <a name="test-the-api"></a>Проверка API

Прежде чем использовать определение OpenAPI, нужно убедиться в том, что API функционирует.

1. На вкладке **Проверка** своей функции выберите операцию **POST**.

1. Введите значения параметров **hours** и **capacity**.

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

1. Щелкните **Отправить**, а затем просмотрите ответ HTTP.

    ![Проверка API функции](media/functions-openapi-definition/test-function-api-openapi.png)

## <a name="download-the-openapi-definition"></a>Скачивание определения OpenAPI

Если ваш API работает правильно, можно скачать определение OpenAPI.

1. Выберите **Скачать определение OpenAPI** в верхней части страницы.
   
   ![Скачивание определения OpenAPI](media/functions-openapi-definition/download-definition.png)

2. Откройте скачанный JSON-файл и просмотрите определение.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Дополнительная информация

Вы использовали интеграцию со службой "Управление API", чтобы создать определение OpenAPI для функций. Теперь вы можете изменить определение в службе "Управление API" на портале. См. [дополнительные сведения о службе "Управление API"](../api-management/api-management-key-concepts.md).

> [!div class="nextstepaction"]
> [Изменение определения OpenAPI в службе "Управление API"](../api-management/edit-api.md)
