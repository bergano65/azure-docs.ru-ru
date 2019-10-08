---
title: Краткое руководство. Создание приложения — LUIS
titleSuffix: Azure Cognitive Services
description: Узнайте, как создать приложение LUIS, которое использует предварительно созданную предметную область `HomeAutomation` для включения и отключения освещения и устройств. Эта предварительно созданная предметная область предоставляет для использования намерения, сущности и фразы. Когда все будет готово, вы получите работающую в облаке конечную точку LUIS.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 748c51e74db20ac101dc2dff0d924567acded114
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703242"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Краткое руководство. Использование предварительно созданного приложения для системы домашней автоматики

В этом кратком руководстве описано, как создать приложение LUIS, которое использует предварительно созданную предметную область `HomeAutomation` для включения и отключения освещения и устройств. Эта предварительно созданная предметная область предоставляет для использования намерения, сущности и фразы. Когда все будет готово, вы получите работающую в облаке конечную точку LUIS.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей требуется бесплатная учетная запись LUIS, которую можно создать на портале LUIS по адресу [https://www.luis.ai](https://www.luis.ai). 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-a-new-app"></a>Создание нового приложения
Создавать и администрировать приложения можно на странице **Мои приложения**. 

1. Выберите **Создать приложение**.

    [![Снимок экрана со списком приложений](media/luis-quickstart-new-app/app-list.png "Screenshot of app list")](media/luis-quickstart-new-app/app-list.png)

1. В диалоговом окне присвойте приложению имя Home Automation.

    [![Снимок экрана со всплывающим диалоговым окном "Создание приложения"](media/luis-quickstart-new-app/create-new-app-dialog.png "Screenshot of Create new app pop-up dialog")](media/luis-quickstart-new-app/create-new-app-dialog.png)

1. Выберите язык и региональные параметры для приложения. Для нашего примера Home Automation выберите английский язык. Затем выберите **Готово**. LUIS создаст приложение Home Automation. 

    >[!NOTE]
    >Язык и региональные параметры нельзя изменить после создания приложения. 

## <a name="add-prebuilt-domain"></a>Добавление готовой предметной области

На панели навигации слева выберите **Prebuilt domains** (Предварительно созданные предметные области). Выполните поиск по строке Home. Выберите **Add domain** (Добавить предметную область).

[![Снимок экрана с предметной областью Home Automation, вызываемой из предварительно созданного меню предметных областей](media/luis-quickstart-new-app/home-automation.png "Screenshot of Home Automation domain called out in prebuilt domain menu")](media/luis-quickstart-new-app/home-automation.png)

Когда предметная область будет успешно добавлена, в области предварительно созданных предметных областей отобразится кнопка **Remove domain** (Удалить предметную область).

[![Снимок экрана с кнопкой удаления в предметной области Home Automation](media/luis-quickstart-new-app/remove-domain.png "Screenshot of Home Automation domain with remove button")](media/luis-quickstart-new-app/remove-domain.png)

## <a name="intents-and-entities"></a>Намерения и сущности

Выберите **Intents** (Намерения) в области навигации слева, чтобы просмотреть намерения из предметной области HomeAutomation. По каждому намерению представлены примеры высказываний.

![Снимок экрана списка намерений HomeAutomation](media/luis-quickstart-new-app/home-automation-intents.png "Screenshot of HomeAutomation intents list")]

> [!NOTE]
> Намерение **None** (Нет) стандартно предоставляется всеми приложениями LUIS. Оно используется для обработки фраз, которые не соответствуют функциям приложения. 

Выберите намерение **HomeAutomation.TurnOff**. Вы увидите, что это намерение содержит список фраз с обозначенными сущностями.

[![Снимок экрана с намерением HomeAutomation.TurnOff](media/luis-quickstart-new-app/home-automation-turnoff.png "Screenshot of HomeAutomation.TurnOff intent")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>Обучение приложения LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-your-app"></a>Тестирование приложения
После обучения можно выполнить тестирование приложения. Щелкните **Test** (Тестировать) в меню навигации сверху. Введите тестовое высказывание, например Turn off the lights (Выключить свет) в области интерактивного тестирования и нажмите клавишу ВВОД. 

```
Turn off the lights
```

Убедитесь, что намерение с самой высокой оценкой для этого тестового высказывания выбрано правильно.

В нашем примере `Turn off the lights` правильно распознано как намерение с высокой оценкой **HomeAutomation.TurnOff**.

[![Снимок экрана с выделенной фразой на панели тестирования](media/luis-quickstart-new-app/test.png "Screenshot of Test panel with utterance highlighted")](media/luis-quickstart-new-app/test.png)


Выберите **Inspect** (Проверить), чтобы просмотреть дополнительные сведения о прогнозе.

![Снимок экрана с панелью тестирования, на которой выделено намерение](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

Снова щелкните **Test** (Тестировать), чтобы свернуть панель тестирования. 

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Публикация приложения для получения URL-адреса конечной точки

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-v2-api-prediction-endpoint"></a>Запрос конечной точки прогнозирования API V2

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

1. Перейдите в конец URL-адреса, введите `turn off the living room light` и нажмите клавишу ВВОД. 

    #### <a name="v2-prediction-endpointtabv2"></a>[Конечная точка прогнозирования V2](#tab/V2)

    `https://<region>.api.cognitive.microsoft.com/luis/**v2.0**/apps/<appID>?subscription-key=<YOUR_KEY>&**q=<user-utterance-text>**`

    Браузер отображает ответ JSON от **API V2** для конечной точки HTTP.

    ```json
    {
      "query": "turn off the lights",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOff",
        "score": 0.995867
      },
      "entities": [
        {
          "entity": "lights",
          "type": "HomeAutomation.DeviceType",
          "startIndex": 13,
          "endIndex": 18,
          "resolution": {
            "values": [
              "light"
            ]
          }
        }
      ]
    }
    ```
    
    #### <a name="v3-prediction-endpointtabv3"></a>[Конечная точка прогнозирования V3](#tab/V3)

    Для [запроса API V3](luis-migration-api-v3.md) в браузере измените HTTPS-запрос метода GET, изменив значения в угловых скобках для ваших значений.     

    `https://<region>.api.cognitive.microsoft.com/luis/**v3.0-preview**/apps/<appID>/**slots**/**production**/**predict**?subscription-key=<YOUR_KEY>&**query=<user-utterance-text>**`

    ```json
    {
        "query": "turn off the lights",
        "prediction": {
            "normalizedQuery": "turn off the lights",
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.99649024
                }
            },
            "entities": {
                "HomeAutomation.DeviceType": [
                    [
                        "light"
                    ]
                ]
            }
        }
    }
    ```


    Дополнительные сведения о [конечной точке прогнозирования V3](luis-migration-api-v3.md).
    
    * * * 

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Дополнительная информация

Эту конечную точку можно вызвать из кода:

> [!div class="nextstepaction"]
> [Руководство по вызову конечной точки LUIS с помощью кода](luis-get-started-cs-get-intent.md).
