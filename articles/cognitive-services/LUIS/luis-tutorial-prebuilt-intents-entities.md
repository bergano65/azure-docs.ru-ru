---
title: Руководство по Предварительно созданные намерения и сущности — LUIS
titleSuffix: Azure Cognitive Services
description: В этом руководстве в приложение добавляются предварительно созданные намерения и сущности, чтобы быстро осуществлять прогнозирование намерений и извлекать данные. Не нужно помечать высказывания с использованием предварительно созданных сущностей. Сущность определяется автоматически.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: diberry
ms.openlocfilehash: cf0ef1095946b1c8e9479b3cd47fe403baeed7d1
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757129"
---
# <a name="tutorial-identify-common-intents-and-entities"></a>Руководство по Определение общих намерений и сущностей

В этом руководстве добавляются предварительно созданные намерения и сущности в учебное приложение Human Resources, чтобы быстро осуществлять прогнозирование намерений и извлекать данные. Вам не нужно указывать какие-либо высказывания с заранее созданными сущностями, потому что они обнаруживаются автоматически.

Предварительно созданные модели (предметные области, намерения и сущности) позволяют быстро создавать свои модели.

**В этом руководстве рассмотрено, как выполнять следующие задачи.**

> [!div class="checklist"]
> * Создание приложения
> * Добавление предварительно созданных намерений 
> * Добавление предварительно созданных сущностей 
> * Обучение 
> * Публикация 
> * Получать намерения и сущности из конечной точки.

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="create-a-new-app"></a>Создание нового приложения

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]


## <a name="add-prebuilt-intents-to-help-with-common-user-intentions"></a>Добавление предварительно созданных намерений с распространенными намерениями пользователей

В службе LUIS доступно несколько предварительно созданных намерений, что упрощает процедуру выбора.  

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Щелкните **Add prebuilt domain intent** (Добавить предварительно созданное намерение предметной области). 

1. Выполните поиск `Utilities`. 

1. Выберите все намерения и нажмите кнопку **Готово**. Эти намерения могут быть полезны при определении того, где находится пользователь в диалоге и что он просит выполнить. 

## <a name="add-prebuilt-entities-to-help-with-common-data-type-extraction"></a>Добавление предварительно созданных сущностей для помощи в извлечении распространенных типов данных

LUIS предоставляет несколько предварительно созданных сущностей для извлечения общих данных. 

1. Выберите **Сущности** в меню навигации слева.

1. Нажмите кнопку **Add prebuilt entity** (Добавить предварительно созданную сущность).

1. Выберите следующие сущности из списка предварительно созданных сущностей, а затем нажмите кнопку **Готово**:

   * **[GeographyV2](luis-reference-prebuilt-geographyV2.md)**

     Эта сущность поможет добавить функцию распознавания места в клиентское приложение.

## <a name="add-example-utterances-to-the-none-intent"></a>Добавление примеров речевых фрагментов в намерение None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Обучение приложения для проверки изменений намерения 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Публикация приложения, чтобы в обученную модель можно было отправлять запросы из конечной точки

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Получение намерения и прогнозирование сущности из конечной точки

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Перейдите в конец URL-адреса в адресной строке браузера и введите `I want to cancel my trip to Seattle`. Последний параметр строки запроса — `q`. Это **запрос** фразы. 

    ```json
    {
      "query": "I want to cancel my trip to Seattle",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.1055009
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.1055009
        },
        {
          "intent": "Utilities.SelectItem",
          "score": 0.02659072
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.0253379084
        },
        {
          "intent": "Utilities.ReadAloud",
          "score": 0.02528683
        },
        {
          "intent": "Utilities.SelectNone",
          "score": 0.02434013
        },
        {
          "intent": "Utilities.Escalate",
          "score": 0.009161292
        },
        {
          "intent": "Utilities.Help",
          "score": 0.006861785
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00633448
        },
        {
          "intent": "Utilities.ShowNext",
          "score": 0.0053827134
        },
        {
          "intent": "None",
          "score": 0.002602003
        },
        {
          "intent": "Utilities.ShowPrevious",
          "score": 0.001797354
        },
        {
          "intent": "Utilities.SelectAny",
          "score": 0.000831930141
        },
        {
          "intent": "Utilities.Repeat",
          "score": 0.0006924066
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.000606057351
        },
        {
          "intent": "Utilities.GoBack",
          "score": 0.000276725681
        },
        {
          "intent": "Utilities.FinishTask",
          "score": 0.000267822179
        },
        {
          "intent": "Utilities.Reject",
          "score": 3.21784828E-05
        }
      ],
      "entities": [
        {
          "entity": "seattle",
          "type": "builtin.geographyV2.city",
          "startIndex": 28,
          "endIndex": 34
        }
      ]
    }
    ```

    В результате спрогнозировано намерение Utilities.Cancel с точностью 80 % и извлечены данные о городе. 


## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Связанные сведения

Дополнительные сведения о предварительно созданных моделях:

* [Предварительно созданные предметные области](luis-reference-prebuilt-domains.md). Это общие предметные области, которые сокращают время, затрачиваемое на разработку приложения LUIS.
* Предварительно созданные намерения. Это отдельные намерения в общих предметных областях. Вы можете добавить намерения по отдельности, вместо того чтобы добавлять всю предметную область.
* [Предварительно созданные сущности](luis-prebuilt-entities.md). Это общие типы данных, полезные для большинства приложений LUIS.

Дополнительные сведения о работе приложения LUIS:

* [Обучение активной версии приложения LUIS](luis-how-to-train.md)
* [Как опубликовать предложение](luis-how-to-publish-app.md)
* [Тестирование приложения LUIS на портале LUIS](luis-interactive-test.md)

## <a name="next-steps"></a>Дополнительная информация

Добавляя предварительно созданные намерения и сущности, клиентское приложение может определить общие намерения пользователя и извлекать общие типы данных.  

> [!div class="nextstepaction"]
> [Добавление сущности регулярного выражения в приложение](luis-quickstart-intents-regex-entity.md)

