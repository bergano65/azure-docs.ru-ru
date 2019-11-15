---
title: Краткое руководство. Развертывание приложения на портале LUIS
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве показано, как развернуть приложение путем создания ресурса конечной точки прогнозирования, назначения ресурса, обучения и публикации приложения.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: diberry
ms.openlocfilehash: 279ec4e8a6d9a9d473cc511b4ec690391cdbd634
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73669427"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Краткое руководство. Развертывание приложения на портале LUIS

[!INCLUDE [Uses preview portal](./includes/uses-portal-preview.md)]

Когда приложение LUIS будет готово возвращать прогнозы касательно речевых фрагментов в клиентское приложение (например, в чат-бот), его необходимо развернуть в конечной точке прогнозирования.

В рамках этого краткого руководства вы узнаете, как развернуть приложение. Вы создадите ресурс конечной точки прогнозирования, назначите его приложению, обучите и опубликуете это приложение.

## <a name="prerequisites"></a>Предварительные требования

* Оформите [подписку Azure](https://azure.microsoft.com/free).
* Выполните указания из [предыдущего краткого руководства по порталу](get-started-portal-build-app.md) или [скачайте и импортируйте приложение](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).

## <a name="create-the-endpoint-resource"></a>Создание ресурса конечной точки

Ресурс конечной точки прогнозирования создается на портале Azure. Этот ресурс следует использовать только для запросов прогнозирования конечной точки. Не используйте этот ресурс для внесения изменений в приложение.

1. Войдите на [портал Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) и создайте ресурс.

1. Настройте для подписки следующие параметры:

   |Параметр|Значение|Назначение|
   |--|--|--|
   |ИМЯ|`my-luis-resource`|Имя ресурса Azure. Это имя понадобится при назначении ресурса для приложения на портале LUIS.|
   |Subscription|Ваша подписка|Выберите одну из подписок, связанных с вашей учетной записью.|
   |группа ресурсов.|`my-resource-group`|Создайте группу ресурсов для всех ресурсов Cognitive Services. Выполнив все необходимые действия с ресурсами, вы можете удалить группу ресурсов, чтобы очистить подписку. |
   |Место разработки|**Западная часть США**|Регион Azure для разработки.|
   |Ценовая категория для разработки|**F0**|Ценовая категория по умолчанию для разработки.|
   |Расположение среды выполнения|**Западная часть США**|Регион Azure для запросов к конечной точке прогнозирования.|
   |Ценовая категория среды выполнения|**S0**|Эта ценовая категория поддерживает веб-сайты с интенсивным трафиком.|
   | | | |


   ![Выбор API Azure](./media/luis-how-to-azure-subscription/create-resource-in-azure.png)

1. Выберите **Создать**, чтобы создать ресурс Azure.

   В следующем разделе вы узнаете, как подключить этот ресурс к приложению LUIS на портале LUIS.

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Назначение ключа ресурса для приложения LUIS на портале LUIS

Каждый раз при создании ресурса для LUIS необходимо назначать его для приложения LUIS. После этого вам не нужно будет еще раз выполнять этот шаг до создания следующего ресурса. Вы можете создать ресурс для расширения регионов приложения или для поддержки большего количества запросов прогнозирования.

1. Войдите на [портал LUIS](https://www.luis.ai) и выберите приложение **myEnglishApp** из списка приложений.

1. В верхнем правом меню выберите **Manage** (Управление), **Azure Resources** (Ресурсы Azure).

1. Чтобы добавить LUIS, выберите **Add prediction resource** (Добавить ресурс прогнозирования).

    ![Чтобы добавить ресурс прогнозирования LUIS, выберите Add prediction resource (Добавить ресурс прогнозирования).](./media/get-started-portal-deploy-app/azure-resources-add-prediction-resource.png)

1. Выберите клиент, подписку и группу ресурсов. Выберите **Assign resource** (Присвоить ресурс).

   ![Присвоение ресурса приложению](./media/get-started-portal-deploy-app/assign-resource.png)

1. Выполните те же действия, чтобы добавить в приложение ключ разработки.

1. Найдите в таблице добавленную строку для нового ресурса приложения и скопируйте URL-адрес конечной точки. Он построен правильно для того, чтобы отправлять запрос `HTTP GET` в конечную точку API LUIS для прогнозирования.

## <a name="train-the-app"></a>Обучение приложения

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-the-prediction-endpoint"></a>Публикация приложения в конечной точке прогнозирования

[!INCLUDE [LUIS How to Train steps](includes/howto-publish.md)]

## <a name="prediction-endpoint-request"></a>Запрос конечной точки прогнозирования

`query=` в конце URL-адреса обозначает место добавления фрагмента речи пользователя в запрос GET. После `query=` введите речевой фрагмент пользователя, используемый в конце предыдущего краткого руководства:

```Is there a form named hrf-234098```

Убедитесь, что строка запроса содержит следующие пары:

* `show-all-intents=true`
* `verbose=true`

В браузере отобразится ответ:

```JSON
{
    "query": "Is there a form named hrf-234098",
    "prediction": {
        "topIntent": "FindForm",
        "intents": {
            "FindForm": {
                "score": 0.9768753
            },
            "None": {
                "score": 0.0216071177
            }
        },
        "entities": {
            "Human Resources Form Number": [
                "hrf-234098"
            ],
            "$instance": {
                "Human Resources Form Number": [
                    {
                        "type": "Human Resources Form Number",
                        "text": "hrf-234098",
                        "startIndex": 22,
                        "length": 10,
                        "modelTypeId": 8,
                        "modelType": "Regex Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

Чтобы просмотреть на панели проверки сведения такого же уровня, необходимо выполнить публикацию приложения. После этого на панели проверки выберите **Compare with published** (Сравнить с опубликованным). На панели проверки опубликованного приложения щелкните **Show JSON view** (Просмотреть представление JSON), чтобы просмотреть данные JSON, отображаемые при выполнении предыдущего шага. Таким образом вы сможете найти различия между текущим приложением, над которым работаете, и приложением, которое опубликовано в конечной точке.

[![Сравнение текущего приложения, в которое вносятся изменения, и опубликованной версии приложения](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Очистка ресурсов

Изучив это краткое руководство, щелкните **Мои приложения** в меню навигации вверху. Установите флажок возле имени приложения в списке, а затем щелкните **Удалить** в контекстном меню над списком.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Определение общих намерений и сущностей](luis-tutorial-prebuilt-intents-entities.md)
