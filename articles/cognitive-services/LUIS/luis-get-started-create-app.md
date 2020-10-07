---
title: Краткое руководство. Создание приложения — LUIS
description: В этом кратком руководстве описано, как создать приложение LUIS, которое использует предварительно созданную предметную область `HomeAutomation` для включения и отключения освещения и устройств. Эта предварительно созданная предметная область предоставляет для использования намерения, сущности и фразы. Когда все будет готово, вы получите работающую в облаке конечную точку LUIS.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/05/2020
ms.openlocfilehash: 28bf79b61c0278a3f45820a23cd2c69f0b609700
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91316497"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Краткое руководство. Использование предварительно созданного приложения для системы домашней автоматики

В этом кратком руководстве описано, как создать приложение LUIS, которое использует предварительно созданную предметную область `HomeAutomation` для включения и отключения освещения и устройств. Эта предварительно созданная предметная область предоставляет для использования намерения, сущности и фразы. Когда все будет готово, вы получите работающую в облаке конечную точку LUIS.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>Создание нового приложения
Создавать и администрировать приложения можно на странице **Мои приложения**.

1. В списке "Мои приложения" выберите **+ Создать приложение для общения**, а затем в списке параметров выберите **+ Создать приложение для общения** еще раз.

1. В диалоговом окне введите имя приложения `Home Automation`.
1. Выберите в качестве языка и региональных параметров **Английский**.
1. При необходимости введите описание.
1. Не выбирайте ресурс прогнозирования, если он еще не создан. Чтобы использовать конечную точку прогнозирования приложения (промежуточное или рабочее использование), необходимо назначить ресурс прогнозирования.
1. Нажмите кнопку **Готово**.

    LUIS создает приложение.

    ![В диалоговом окне присвойте приложению имя Home Automation.](./media/create-new-app-details.png)

    >[!NOTE]
    >Язык и региональные параметры нельзя изменить после создания приложения.

## <a name="add-prebuilt-domain"></a>Добавление готовой предметной области

1. В области навигации слева выберите **Предварительно созданные предметные области**.
1. Выполните поиск по фразе **HomeAutomation**.
1. Нажмите кнопку **Add domain** (Добавить предметную область) на карточке HomeAutomation.

    > [!div class="mx-imgBorder"]
    > ![Выберите Prebuilt domains (Предварительно созданные предметные области) и найдите HomeAutomation. Нажмите кнопку Add domain (Добавить предметную область) на карточке HomeAutomation.](media/luis-quickstart-new-app/home-automation.png)

    Когда предметная область будет успешно добавлена, в области предварительно созданных предметных областей отобразится кнопка **Remove domain** (Удалить предметную область).

## <a name="intents-and-entities"></a>Намерения и сущности

1. Выберите **Intents** (Намерения) для просмотра намерений предметной области HomeAutomation. Для намерений предварительно созданных предметных областей есть примеры речевых фрагментов.

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана со списком намерений HomeAutomation](media/luis-quickstart-new-app/home-automation-intents.png "Снимок экрана со списком намерений HomeAutomation")

    > [!NOTE]
    > Намерение **None** (Нет) стандартно предоставляется всеми приложениями LUIS. Оно используется для обработки фраз, которые не соответствуют функциям приложения.

1. Выберите намерение **HomeAutomation.TurnOff**. Намерение содержит список речевых фрагментов с обозначенными сущностями.

    > [!div class="mx-imgBorder"]
    > [![Снимок экрана с намерением HomeAutomation.TurnOff](media/luis-quickstart-new-app/home-automation-turnoff.png "Снимок экрана с намерением HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>Обучение приложения LUIS

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>Тестирование приложения
После обучения можно выполнить тестирование приложения.

1. Выберите **Тест** в правом верхнем меню навигации.

1. Введите тестовый речевой фрагмент, например `Turn off the lights`, в области интерактивного тестирования и нажмите клавишу ВВОД.

    ```
    Turn off the lights
    ```

    В нашем примере `Turn off the lights` правильно распознано как намерение с высокой оценкой **HomeAutomation.TurnOff**.

    ![Снимок экрана с панелью тестирования, на которой выделено намерение](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

1. Выберите **Проверить**, чтобы просмотреть дополнительные сведения о прогнозировании.

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана: панель тестирования со сведениями о проверке](media/luis-quickstart-new-app/test.png)

1. Закройте область тестирования.

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Публикация приложения для получения URL-адреса конечной точки

[!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)]

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>Запрос конечной точки прогнозирования API V3

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

2. В адресной строке браузера для строки запроса проверьте, указаны ли в URL-адресе следующие строки имени и значения. Если они отсутствуют в строке запроса, добавьте их:

    |Пара "имя — значение"|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

3. В адресной строке браузера перейдите в конец URL-адреса и введите `turn off the living room light` для значения _запроса_, а затем нажмите клавишу ВВОД.

    ```json
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.969448864
                },
                "HomeAutomation.QueryState": {
                    "score": 0.0122336326
                },
                "HomeAutomation.TurnUp": {
                    "score": 0.006547436
                },
                "HomeAutomation.TurnDown": {
                    "score": 0.0050634006
                },
                "HomeAutomation.SetDevice": {
                    "score": 0.004951761
                },
                "HomeAutomation.TurnOn": {
                    "score": 0.00312553928
                },
                "None": {
                    "score": 0.000552945654
                }
            },
            "entities": {
                "HomeAutomation.Location": [
                    "living room"
                ],
                "HomeAutomation.DeviceName": [
                    [
                        "living room light"
                    ]
                ],
                "HomeAutomation.DeviceType": [
                    [
                        "light"
                    ]
                ],
                "$instance": {
                    "HomeAutomation.Location": [
                        {
                            "type": "HomeAutomation.Location",
                            "text": "living room",
                            "startIndex": 13,
                            "length": 11,
                            "score": 0.902181149,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceName": [
                        {
                            "type": "HomeAutomation.DeviceName",
                            "text": "living room light",
                            "startIndex": 13,
                            "length": 17,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceType": [
                        {
                            "type": "HomeAutomation.DeviceType",
                            "text": "light",
                            "startIndex": 25,
                            "length": 5,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
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

    Дополнительные сведения о [конечной точке прогнозирования V3](luis-migration-api-v3.md).


## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Дальнейшие действия

Эту конечную точку можно вызвать из кода:

> [!div class="nextstepaction"]
> [Руководство по вызову конечной точки LUIS с помощью кода](luis-get-started-cs-get-intent.md).
