---
title: Руководство по сущности списка — LUIS
titleSuffix: Azure Cognitive Services
description: Получите данные, соответствующие определенному списку элементов. У каждого элемента в списке могут быть синонимы, для которых существует точное совпадение.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: diberry
ms.openlocfilehash: 83644ac7144adaa664b13b6e7d7993c50da0e779
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852472"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance-with-list-entity"></a>Руководство по получению точных данных, сравниваемых с текстом, на основе речевого фрагмента с использованием сущности списка

В этом учебнике вы узнаете, как получить данные, точно соответствующие предварительно определенному списку элементов.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**В этом руководстве рассмотрено, как выполнять следующие задачи.**

<!-- green checkmark -->
> [!div class="checklist"]
> * Импорт приложения и использование существующего намерения.
> * Добавление сущности списка
> * Обучение, публикация приложения и отправка к нему запросов для получения извлеченных данных.

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>Что такое сущность списка?

Сущность списка представляет собой точное текстовое совпадение со словами в речевом фрагменте. Каждый элемент в списке может содержать список синонимов. Используйте сущность списка, если требуется точное совпадение.

Для этого импортированного приложения для заказа пиццы создайте сущность списка для различных типов коржей пиццы.

Сущность списка оптимальна для данных такого типа в таких случаях:

* Значения данных являются известным набором.
* Набор не превышает максимальные [ограничения](luis-boundaries.md) LUIS для этого типа сущности.
* Текст в высказывании в точности совпадает с синонимом или каноническим именем. LUIS не использует список вне точных текстовых совпадений. Сущность списка не используется для разрешения выделения корней, множественных форм и других вариантов. Для управления вариантами рекомендуется применять [шаблон](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) с синтаксисом, имеющим необязательный текст.

> [!CAUTION]
> Если вы не уверены в том, что требуется — сущность списка или сущность, прошедшая машинное обучение, со списком фраз в качестве дескриптора, лучше всего использовать последнее. Этот метод позволяет LUIS изучать и расширять значения извлекаемых данных.

## <a name="import-example-json-and-add-utterances"></a>Импорт примера JSON-файла и добавление речевых фрагментов

1.  Скачайте и сохраните [JSON-файл приложения](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

    [!INCLUDE [Import app steps](includes/import-app-steps.md)]

1. Импортированное приложение содержит намерение `OrderPizza`. Выберите это намерение и добавьте несколько речевых фрагментов с новыми типами коржей:

    |Новые речевые фрагменты|
    |--|--|
    |Закажите маленькую пиццу пепперони с хрустящим коржом|
    |3 гавайские пиццы на тонком корже|
    |Доставьте 2 пиццы с коржом с начинкой и хлебными палочками|
    |Самовывоз одной пиццы на толстом корже|
    |Одна пицца пепперони на толстом корже, выпеченная в глубокой форме|

## <a name="crust-list-entity"></a>Сущность списка коржей

Теперь, когда намерение **OrderPizza** содержит примеры речевых фрагментов с типами коржей, LUIS необходимо понять, какие слова представляют типы коржей.

Примеры основного имени и синонимов:

|Каноническое имя|Синонимы|
|--|--|
|Глубокая форма|глубокая<br>корж в глубокой форме<br>толстый<br>толстый корж|
|хрустящий|обычный<br>оригинальный<br>нормальный<br>обычный корж<br>оригинальный корж<br>нормальный корж|
|с начинкой|корж с начинкой|
|тонкий|тонкий корж<br>самый тонкий<br>самый тонкий корж|

1. В области слева выберите **Entities** (Сущности).

1. Выберите **+ Создать**.

1. Во всплывающем диалоговом окне сущности введите имя сущности `CrustList` и **Список** для типа сущности. Щелкните **Далее**.

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана всплывающего диалогового окна создания сущности](media/luis-quickstart-intent-and-list-entity/create-pizza-crust-list-entity.png)

1. На странице **Create a list entity** (Создание сущности списка) введите канонические имена и синонимы для каждого канонического имени, а затем нажмите кнопку **Создать**.

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана добавления элементов в сущность списка](media/luis-quickstart-intent-and-list-entity/add-pizza-crust-items-list-entity.png)

    При добавлении сущности списка в приложение LUIS вам не нужно [помечать](label-entity-example-utterance.md) текст с помощью сущности списка. Она применяется ко всем речевым фрагментам во всех намерениях.

## <a name="train-the-app-before-testing-or-publishing"></a>Обучение приложения перед тестированием и публикацией

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Публикация приложения для получения запроса из конечной точки

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Получение намерения и прогнозирование сущности из конечной точки

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. В конце URL-адреса введите такое высказывание:

    `Deliver 2 deep dish hawaiian pizzas and a thin pepperoni`

    Последний параметр строки запроса — `query`. Это **запрос** фразы.


    ```json
    {
        "query": "Deliver 2 deep dish hawaiian pizzas and a thin pepperoni",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.9957229
                },
                "None": {
                    "score": 0.016832687
                },
                "Confirm": {
                    "score": 0.0015708931
                },
                "Greeting": {
                    "score": 0.00057060417
                }
            },
            "entities": {
                "number": [
                    2
                ],
                "CrustList": [
                    [
                        "Deep dish"
                    ],
                    [
                        "Thin"
                    ]
                ],
                "$instance": {
                    "number": [
                        {
                            "type": "builtin.number",
                            "text": "2",
                            "startIndex": 8,
                            "length": 1,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "CrustList": [
                        {
                            "type": "CrustList",
                            "text": "deep dish",
                            "startIndex": 10,
                            "length": 9,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "CrustList",
                            "text": "thin",
                            "startIndex": 42,
                            "length": 4,
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

    Типы коржей найдены как точные текстовые совпадения и возвращены в ответе JSON. Эти сведения используются клиентским приложением для обработки заказа.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Связанные сведения

* [Типы сущностей и их задачи в LUIS](luis-concept-entity-types.md#list-entity) — основные сведения
* [Обучение активной версии приложения LUIS](luis-how-to-train.md)
* [Как опубликовать предложение](luis-how-to-publish-app.md)
* [Тестирование приложения LUIS на портале LUIS](luis-interactive-test.md)
* [Entities and their purpose in LUIS](luis-concept-entity-types.md) (Сущности и их назначение в LUIS)
* [Regular expression entity](reference-entity-regular-expression.md?tabs=V3) (Сущность регулярного выражения)
* [Add entities to extract data](luis-how-to-add-entities.md) (Добавление сущностей для извлечения данных)

## <a name="next-steps"></a>Дополнительная информация
Из этого учебника вы узнали, как добавить примеры речевых фрагментов, а затем создать сущность списка для извлечения точного текстового совпадения из речевых фрагментов. После обучения и публикации приложения запрос к конечной точке идентифицировал намерение и вернул извлеченные данные.

> [!div class="nextstepaction"]
> [Учебник по извлечению из речевого фрагмента данных, связанных с контекстом](tutorial-entity-roles.md)

