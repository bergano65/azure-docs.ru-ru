---
title: Точное текстовое совпадение
titleSuffix: Azure Cognitive Services
description: Получите данные, соответствующие определенному списку элементов. У каждого элемента в списке могут быть синонимы, для которых существует точное совпадение.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: bf4fd5d2a3a9bb06882dcd1b4674ccdf8ad894ee
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53971415"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance"></a>Руководство. Получение точных данных, сравниваемых с текстом, на основе речевого фрагмента

В этом руководстве вы узнаете, как получить данные сущности, соответствующие предварительно определенному списку элементов. 

**В этом руководстве рассмотрено, как выполнять следующие задачи.**

<!-- green checkmark -->
> [!div class="checklist"]
> * Создание приложения
> * Добавление намерения
> * Добавление сущности списка 
> * Train 
> * Опубликовать
> * Получать намерения и сущности из конечной точки.

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>Что такое сущность списка?

Сущность списка представляет собой точное текстовое совпадение со словами в речевом фрагменте. 

Каждый элемент в списке может содержать список синонимов. Для приложения по управлению персоналом отдел компании может определяться по нескольким ключевым компонентам, таким как официальное название, общеупотребительные сокращения и коды отделов выставления счетов. 

Приложение по управлению персоналом должно определить отдел, в который переводится сотрудник. 

Сущность списка оптимальна для данных такого типа в таких случаях:

* Значения данных являются известным набором.
* Набор не превышает максимальные [ограничения](luis-boundaries.md) LUIS для этого типа сущности.
* Текст в высказывании в точности совпадает с синонимом или каноническим именем. LUIS не использует список вне точных текстовых совпадений. Сущность списка не используется для разрешения выделения корней, множественных форм и других вариантов. Для управления вариантами рекомендуется применять [шаблон](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance) с синтаксисом, имеющим необязательный текст. 

## <a name="create-a-new-app"></a>Создание нового приложения

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-transfer-employees-to-a-different-department"></a>Создание намерения для перевода сотрудников в другой отдел

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Выберите **Create new intent**. (Создать намерение). 

3. Введите `TransferEmployeeToDepartment` во всплывающем диалоговом окне и нажмите кнопку **Done** (Готово). 

    ![Снимок экрана диалогового окна создания намерения с](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. Добавьте примеры фраз в намерение.

    |Примеры высказываний|
    |--|
    |Перевести Джона Смита в бухгалтерию.|
    |Перевести Джил Джонс в отдел научных исследований и разработки.|
    |В отделе 1234 появился новый сотрудник Билл Брадстрит.|
    |Перевести Джона Джексона в инженерный отдел. |
    |Перевести Дебру Дотери в отдел продаж.|
    |Направить Джил Джонс в IT-отдел.|
    |Перевести Елис Андерсон в отдел разработки и эксплуатации.|
    |Направить Карла Чамерлина в отдел финансов.|
    |Стив Стандиш — в отдел 1234.|
    |Таннер Томпсон — в отдел 3456.|

    [![Снимок экрана намерения с примерами речевых фрагментов](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png "Screenshot of intent with example utterances")](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="department-list-entity"></a>Сущности списка отделов

Теперь, когда намерение **TransferEmployeeToDepartment** содержит высказывания, LUIS должна распознавать, что такое отдел. 

Первичное _каноническое_ имя для каждого элемента является названием отдела. Примеры синонимов для этой тематики. 

|Каноническое имя|синонимы;|
|--|--|
|Учет|Бухг.<br>Бухгалт.<br>3456|
|Разработка и эксплуатация|Разработка и операции<br>4949|
|Engineering|Инж.<br>Инженер.<br>4567|
|Финансы|Фин.<br>2020|
|Информационные технологии|IT<br>2323|
|Продажи|Прод.<br>Продаж.<br>1414|
|Научные исследования и разработки|НИИР<br>1234|

1. В области слева выберите **Entities** (Сущности).

1. Нажмите кнопку **Создать сущность**.

1. Во всплывающем диалоговом окне сущности введите имя сущности `Department` и **Список** для типа сущности. Нажмите кнопку **Готово**.  

    [![Снимок экрана со всплывающим диалоговым окном для создания сущности](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png "Screenshot of creating new entity pop-up dialog")](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png#lightbox)

1. На странице сущности Department введите `Accounting` в качестве нового значения.

    [![Снимок экрана, на котором показан ввод значений](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png "Screenshot of entering value")](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png#lightbox)

1. В графу синонимов добавьте синонимы из приведенной выше таблицы.

    [![Снимок экрана, на котором показан ввод синонимов](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png "Screenshot of entering synonyms")](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png#lightbox)

1. Продолжайте добавлять канонические имена и синонимы. 

## <a name="add-example-utterances-to-the-none-intent"></a>Добавление примеров речевых фрагментов в намерение None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Обучение приложения для проверки изменений намерения 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Публикация приложения, чтобы в обученную модель можно было отправлять запросы из конечной точки

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Получение намерения и прогнозирование сущности из конечной точки

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

1. Перейдите в конец URL-адреса и введите `shift Joe Smith to IT`. Последний параметр строки запроса — `q`. Это **запрос** фразы. Эта фраза не совпадает ни с какими помеченными фразами, поэтому она является хорошим тестом и должна возвращать намерение `TransferEmployeeToDepartment` с извлеченным `Department`.

  ```json
    {
      "query": "shift Joe Smith to IT",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9775754
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9775754
        },
        {
          "intent": "None",
          "score": 0.0154493852
        }
      ],
      "entities": [
        {
          "entity": "it",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 20,
          "resolution": {
            "values": [
              "Information Technology"
            ]
          }
        }
      ]
    }
  ```

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Связанные сведения

* [Типы сущностей и их задачи в LUIS](luis-concept-entity-types.md#list-entity) — основные сведения
* [Обучение активной версии приложения LUIS](luis-how-to-train.md)
* [Как опубликовать предложение](luis-how-to-publish-app.md)
* [Тестирование приложения LUIS на портале LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как создать новое намерение, добавить примеры высказываний, а затем создать сущность списка для извлечения точного текстового совпадения из высказывания. После обучения и публикации приложения запрос к конечной точке идентифицировал намерение и вернул извлеченные данные.

Продолжите работу с этим приложением, [добавив составную сущность](luis-tutorial-composite-entity.md).

> [!div class="nextstepaction"]
> [Добавление иерархической сущности в приложение](luis-quickstart-intent-and-hier-entity.md)

