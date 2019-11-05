---
title: Руководство по Сущность регулярного выражения — LUIS
titleSuffix: Azure Cognitive Services
description: Извлечение согласованно отформатированных данных из высказывания с помощью сущности регулярного выражения.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 0f24721272c85d658a46ab1319e6f4af79b29364
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465183"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>Руководство по Получение данных правильного формата из высказывания
В этом руководстве создается приложение для извлечения согласованно отформатированных данных из фразы с помощью сущности **регулярного выражения**.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**В этом руководстве рассмотрено, как выполнять следующие задачи.**

<!-- green checkmark -->
> [!div class="checklist"]
> * Создание нового приложения 
> * Добавление намерения
> * Добавление сущности регулярного выражения 
> * Обучение
> * Публикация
> * Получать намерения и сущности из конечной точки.

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>Сущности регулярного выражения

Приложение использует сущность регулярного выражения для извлечения отформатированных номеров формы в системе управления персоналом из высказывания. Хотя намерение высказывания всегда определяется машинным обучением, этот тип сущности к машинному обучению не относится. 

**Примеры высказывания включают следующее.**

|Примеры высказываний|
|--|
|Где находится HRF-123456?|
|Кто написал HRF-123234?|
|Было ли HRF-456098 опубликовано на французском языке?|
|HRF 456098|
|Дата HRF 456098?|
 
Регулярное выражение оптимально для данных такого типа, когда:

* данные имеют правильный формат.


## <a name="create-a-new-app"></a>Создание нового приложения

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-finding-form"></a>Создание намерения для поиска формы

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Выберите **Create new intent**. (Создать намерение). 

1. Введите `FindForm` во всплывающем диалоговом окне и нажмите кнопку **Done** (Готово). 

    ![Снимок экрана диалогового окна создания намерения со служебными программами в поле поиска](./media/luis-quickstart-intents-regex-entity/create-new-intent-ddl.png)

1. Добавьте примеры фраз в намерение.

    |Примеры фраз|
    |--|
    |Каков URL-адрес для hrf-123456?|
    |Где находится hrf-345678?|
    |Когда hrf-456098 был обновлен?|
    |Обновлял ли Артем Кузнецов hrf-234639 на прошлой неделе?|
    |Сколько существует версий hrf-345123?|
    |Кому необходимо авторизовать форму hrf-123456?|
    |Скольким сотрудникам необходимо подтвердить hrf-345678?|
    |Дата hrf-234123?|
    |Автор hrf-546234?|
    |Заголовок hrf-456234?|

    [![Снимок экрана страницы намерения с выделенными фразами](./media/luis-quickstart-intents-regex-entity/findform-intent.png)](./media/luis-quickstart-intents-regex-entity/findform-intent.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>Использование сущности регулярного выражения для отформатированных данных
Сущность регулярного выражения для поиска номера формы — `hrf-[0-9]{6}`. Это регулярное выражение сопоставляет символьные литералы `hrf-`, но не учитывает регистр и варианты языка и региональных параметров. Оно ищет цифры 0–9 (всего 6 цифр).

HRF означает `human resources form`.

LUIS помечает высказывания, добавляемые в намерение. При разметке этих фраз добавляются пробелы до и после дефиса (`Where is HRF - 123456?`). Регулярное выражение применяется к фразам в необработанной форме до выполнения разметки. Так как он применяется к _необработанные_ формы, регулярное выражение не имеет дело с границами слов. 

Создайте сущность регулярного выражения, чтобы сообщить LUIS формат номера HRF, выполнив следующие шаги:

1. В области слева выберите **Entities** (Сущности).

1. Нажмите кнопку **Create new entity** (Создать сущность) на странице "Сущности". 

1. Во всплывающем диалоговом окне введите имя новой сущности `HRF-number` и выберите **RegEx** как тип сущности, а затем введите `hrf-[0-9]{6}` в качестве значения **RegEx** и нажмите кнопку **Готово**.

    ![Снимок экрана всплывающего диалогового окна, где задаются свойства новой сущности](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

1. Выберите **Intents** (Намерения) в меню слева, а затем намерение **FindForm**, чтобы увидеть регулярное выражение, помеченное в высказывании. 

    [![Снимок экрана помеченной фразы с имеющейся сущностью и шаблоном регулярного выражения](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    Так как сущность не обучена с помощью машинного обучения, сущность применяется к фразам и отображается на веб-сайте LUIS сразу же после создания.

## <a name="add-example-utterances-to-the-none-intent"></a>Добавление примеров речевых фрагментов в намерение None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>Обучение приложения перед тестированием и публикацией

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Публикация приложения для получения запроса из конечной точки

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Получение намерения и прогнозирование сущности из конечной точки

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. В конце URL-адреса введите такое высказывание:

    `When were HRF-123456 and hrf-234567 published in the last year?`

    Последний параметр строки запроса — `q`. Это **запрос** фразы. Эта фраза не совпадает ни с какими помеченными фразами, поэтому она является хорошим тестом. В результате должно быть возвращено намерение `FindForm` с двумя номерами форм: `HRF-123456` и `hrf-234567`.

    ```json
    {
      "query": "When were HRF-123456 and hrf-234567 published in the last year?",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9988884
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9988884
        },
        {
          "intent": "None",
          "score": 0.00204812363
        }
      ],
      "entities": [
        {
          "entity": "hrf-123456",
          "type": "HRF-number",
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "hrf-234567",
          "type": "HRF-number",
          "startIndex": 25,
          "endIndex": 34
        }
      ]
    }
    ```

    С помощью сущности регулярного выражения LUIS извлекает именованные данные, что программно более полезно для клиентского приложения, получающего ответ JSON.


## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Связанные сведения

* Концепции сущности [регулярного выражения](luis-concept-entity-types.md#regular-expression-entity)
* [Обучение активной версии приложения LUIS](luis-how-to-train.md)
* [Как опубликовать предложение](luis-how-to-publish-app.md)
* [Тестирование приложения LUIS на портале LUIS](luis-interactive-test.md)

## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как создать новое намерение, добавить примеры высказываний, а затем создать сущность регулярного выражения для извлечения данных в правильном формате из высказывания. После обучения и публикации приложения запрос к конечной точке идентифицировал намерение и вернул извлеченные данные.

> [!div class="nextstepaction"]
> [Дополнительные сведения о списке сущности](luis-quickstart-intent-and-list-entity.md)

