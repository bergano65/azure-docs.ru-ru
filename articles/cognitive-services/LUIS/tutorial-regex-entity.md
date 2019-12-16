---
title: Руководство по Сущность регулярного выражения — LUIS
titleSuffix: Azure Cognitive Services
description: Извлечение согласованно отформатированных данных из речевого фрагмента с помощью сущности регулярного выражения.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: diberry
ms.openlocfilehash: 8da47899e2d3d2c1d04e9cf4768a968e7893ce96
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852448"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>Руководство по Получение данных правильного формата из высказывания
В этом учебнике вы создадите сущность регулярного выражения для извлечения согласованно отформатированных данных из речевого фрагмента.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**В этом руководстве рассмотрено, как выполнять следующие задачи.**

<!-- green checkmark -->
> [!div class="checklist"]
> * Импорт приложения.
> * Добавление намерения
> * Добавление сущности регулярного выражения
> * Обучение, публикация приложения и отправка к нему запросов для получения извлеченных данных.

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>Сущности регулярного выражения

Сущность регулярного выражения можно использовать для извлечения правильно отформатированного текста из речевого фрагмента. Хотя намерение высказывания всегда определяется машинным обучением, этот тип сущности к машинному обучению не относится. Хорошим примером использования сущности регулярного выражения является любой текст, который может быть постоянно представлен в виде [регулярного выражения](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference).

`Send pizza delivery time to x123432`

В этом примере используется _короткий код_ для отправки текстовых сообщений. Этот короткий код состоит из 5 или 6-значного числового кода с префиксом x и может быть описан с помощью регулярного выражения `x\d{5,6}`.

При добавлении сущности регулярного выражения в приложение LUIS вам не нужно [помечать](label-entity-example-utterance.md) текст с помощью этой сущности. Она применяется ко всем речевым фрагментам во всех намерениях.

## <a name="import-example-json-to-begin-app"></a>Импорт примера JSON-файла для создания приложения

1.  Скачайте и сохраните [JSON-файл приложения](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-intent-for-sending-confirmation-text-messages"></a>Создание намерения для отправки текстового сообщения с подтверждением

1. Выберите **+ Создать**, чтобы создать намерение, позволяющее классифицировать намерение речевого фрагмента для отправки текста с подтверждением.

1. Введите `ConfirmationText` во всплывающем диалоговом окне и нажмите кнопку **Done** (Готово).

1. Добавьте примеры фраз в намерение.

    |Примеры высказываний|
    |--|
    |Отправка времени доставки пиццы на номер x123432|
    |Написать на номер x234567 с указанием времени|
    |x23987 для уведомления|

    Чтобы извлечь сущности, прошедшие машинное обучение, вы должны предоставить примеры, которые содержат сущность в различных речевых фрагментах, но без сущностей машинного обучения (вариация не важна). Если текст соответствует регулярному выражению, он будет извлечен.

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>Использование сущности регулярного выражения для отформатированных данных
Создайте сущность регулярного выражения для поиска номера текста. Это регулярное выражение сопоставляет текст, но не учитывает регистр и варианты языка и региональных параметров.

1. В области слева выберите **Entities** (Сущности).

1. Нажмите кнопку **+ Создать** на странице "Список сущностей".

1. Во всплывающем диалоговом окне введите имя новой сущности `ConfirmationTextRegEx`, выберите **RegEx** в качестве типа сущности, а затем нажмите кнопку **Далее**.

    > [!div class="mx-imgBorder"]
    > ![Выполнение шагов по созданию сущности для сущности регулярного выражения](./media/luis-quickstart-intents-regex-entity/pizza-create-new-entity.png)

1. На вкладке **Create a regex entity** (Создание сущности регулярного выражения) введите `x\d{5,6}` в качестве значения **Regex**, а затем нажмите кнопку **Создать**.

    > [!div class="mx-imgBorder"]
    > ![Ввод регулярного выражения для извлечения данных из примера речевого фрагмента](./media/luis-quickstart-intents-regex-entity/pizza-set-regular-expression-for-new-entity.png)

1. Выберите **Intents** (Намерения) в меню слева, а затем намерение **ConfirmationText**, чтобы увидеть регулярное выражение, помеченное в речевых фрагментах.

    > [!div class="mx-imgBorder"]
    > ![Просмотр регулярного выражения с метками в примерах речевых фрагментов](./media/luis-quickstart-intents-regex-entity/pizza-reg-ex-entity-shown-example-utterances-intent.png)

    Так как сущность не обучена с помощью машинного обучения, сущность применяется к фразам и отображается на портале LUIS сразу же после создания.

## <a name="train-the-app-before-testing-or-publishing"></a>Обучение приложения перед тестированием и публикацией

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Публикация приложения для получения запроса из конечной точки

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Получение намерения и прогнозирование сущности из конечной точки

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. В конце URL-адреса введите такое высказывание:

    `Text my pizza delivery to x23456 x234567 x12345`

    Последний параметр строки запроса — `query`. Это **запрос** фразы.

    ```json
    {
        "query": "Text my pizza delivery to x23456 x234567 x12345",
        "prediction": {
            "topIntent": "ConfirmationText",
            "intents": {
                "ConfirmationText": {
                    "score": 0.7061845
                },
                "ModifyOrder": {
                    "score": 0.196021989
                },
                "None": {
                    "score": 0.02342912
                },
                "Test-Pizza": {
                    "score": 0.01213586
                },
                "CancelOrder": {
                    "score": 0.0063042324
                },
                "Confirmation": {
                    "score": 0.0058615827
                },
                "Greetings": {
                    "score": 0.00398947531
                }
            },
            "entities": {
                "ConfirmationTextRegEx": [
                    "x23456",
                    "x234567",
                    "x12345"
                ],
                "$instance": {
                    "ConfirmationTextRegEx": [
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x23456",
                            "startIndex": 26,
                            "length": 6,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x234567",
                            "startIndex": 33,
                            "length": 7,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x12345",
                            "startIndex": 41,
                            "length": 6,
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

    С помощью сущности регулярного выражения LUIS извлекает именованные данные, что программно более полезно для клиентского приложения, получающего ответ JSON.


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Связанные сведения

* [Entities and their purpose in LUIS](luis-concept-entity-types.md) (Сущности и их назначение в LUIS)
* [Regular expression entity](reference-entity-regular-expression.md?tabs=V3) (Сущность регулярного выражения)
* [Add entities to extract data](luis-how-to-add-entities.md) (Добавление сущностей для извлечения данных)

## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как создать новое намерение, добавить примеры высказываний, а затем создать сущность регулярного выражения для извлечения данных в правильном формате из высказывания. После обучения и публикации приложения запрос к конечной точке идентифицировал намерение и вернул извлеченные данные.

> [!div class="nextstepaction"]
> [Дополнительные сведения о списке сущности](tutorial-list-entity.md)

