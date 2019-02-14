---
title: Иерархическая сущность
titleSuffix: Azure Cognitive Services
description: Поиск связанных частей данных на основе контекста. Например, места исходного расположения и назначения при перемещении из одного здания и офиса в другое связаны между собой.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: dea623684454d4b14a370251df10599eb03bbb34
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55856926"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Руководство. Извлечение из речевого фрагмента данных, связанных с контекстом

Из данного руководства можно узнать, как выполнять поиск связанных частей данных на основе контекста. Например, исходное и конечное расположения для перемещения из одного города в другой. Могут потребоваться фрагменты данных, которые связаны друг с другом.  

**В этом руководстве рассмотрено, как выполнять следующие задачи.**

> [!div class="checklist"]
> * Создание приложения
> * Добавление намерения 
> * Добавление иерархической сущности расположения с дочерними элементами исходного и целевого расположений.
> * Train
> * Опубликовать
> * Получать намерения и сущности из конечной точки.

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="hierarchical-data"></a>Иерархические данные

Это приложение определяет, в каких случаях сотрудник должен перемещаться из исходного города в целевой. Для определения расположения в пределах фразы в этом приложении используется иерархическая сущность. 

Иерархическая сущность является оптимальным выбором для этого типа данных из-за двух блоков данных и дочерних расположений.

* Являются простыми сущностями.
* Связаны между собой в контексте фразы.
* В них использовано конкретное слово, обозначающее каждую сущность. Примеры этих слов включают в себя: из/в, выход/переход, от/к.
* Обе дочерние сущности часто содержатся в одном речевом фрагменте. 
* Должны быть сгруппированы и обработаны клиентским приложением в качестве единицы информации.

## <a name="create-a-new-app"></a>Создание нового приложения

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-move-employees-between-cities"></a>Создание намерения для перемещения сотрудников между городами

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Выберите **Create new intent**. (Создать намерение). 

1. Введите `MoveEmployeeToCity` во всплывающем диалоговом окне и нажмите кнопку **Done** (Готово). 

    ![Снимок экрана диалогового окна создания намерения с](./media/luis-quickstart-intent-and-hier-entity/create-new-intent-move-employee-to-city.png)

1. Добавьте примеры фраз в намерение.

    |Примеры высказываний|
    |--|
    |Переместить Джона В. Смита из Сиэтла в Даллас.|
    |Переместить Джил Джонс из Сиэтла в Каир.|
    |Перевести Джона Джексона из Тампа в Атланту. |
    |Перевести Дебру Дотери в Талса из Далласа.|
    |Перем. Джил Джонс из Каира в Тампа.|
    |Перевести Елис Андерсон в Окленд из Редмонда.|
    |Направить Карла Чамерлина из Сан-Франциско в Редмонд.|
    |Отправить Стива Стандиша из Сан-Диего в Бельвю. |
    |Перебросить Таннера Томпсона из Канзас-Сити в Чикаго.|

    [ ![Снимок экрана приложения LUIS с новыми фразами в намерении MoveEmployee](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)

## <a name="create-a-location-entity"></a>Создание сущности расположения
Приложению LUIS необходимо определить расположение, отмечая исходное и целевое расположения во фразах. Если необходимо просмотреть фразу в представлении токенов (необработанном представлении), выберите переключатель в строке над фразой с меткой **Entities View** (Представление сущностей). Выбрав переключатель, вы увидите, что этот элемент управления обозначен **Tokens View** (Представление токенов).

Рассмотрите следующую фразу:

```json
move John W. Smith leaving Seattle headed to Dallas
```

Во фразе указано два расположения: `Seattle` и `Dallas`. Оба эти значения сгруппированы как дочерние элементы иерархической сущности `Location`, так как для выполнения запроса в клиентском приложении их необходимо извлечь из речевого фрагмента, и они связаны между собой. 
 
Если имеется только один дочерний элемент (источник или назначение) иерархической сущности, он по-прежнему будет извлечен. Не нужно находить все дочерние элементы, чтобы извлечь один или несколько. 

1. Во фразе `move John W. Smith leaving Seattle headed to Dallas` выберите слово `Seattle`. Появится раскрывающееся меню с текстовым полем в верхней области. Введите имя сущности `Location` в текстовом поле, а затем выберите в раскрывающемся меню **Create new entity** (Создать сущность). 

    [![Снимок экрана создания новой сущности на странице намерения](media/luis-quickstart-intent-and-hier-entity/create-location-hierarchical-entity-from-example-utterance.png "Screenshot of creating new entity on intent page")](media/luis-quickstart-intent-and-hier-entity/create-location-hierarchical-entity-from-example-utterance.png#lightbox)

1. Во всплывающем окне выберите **иерархический** тип сущности с `Origin` и `Destination` в качестве дочерних сущностей. Нажмите кнопку **Готово**.

    ![Снимок экрана, на котором показано всплывающее диалоговое окно для создания сущности с новой сущностью расположения](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-2.png "Screenshot of entity creation pop-up dialog for new Location entity")

1. Метка для `Seattle` помечается как `Location`, так как LUIS не знает, это исходный город, город назначения или ни один из них. Выберите `Seattle`, затем выберите **Размещение**, а затем в меню справа выберите `Origin`.

    [![Снимок экрана всплывающего диалогового окна обозначения сущности для изменения дочернего элемента сущности расположения](media/luis-quickstart-intent-and-hier-entity/choose-hierarchical-child-entity-from-example-utterance.png "Screenshot of entity labeling pop-up dialog to change locations entity child")](media/luis-quickstart-intent-and-hier-entity/choose-hierarchical-child-entity-from-example-utterance.png#lightbox)

1. Обозначите другие расположения в оставшихся речевых фрагментах. Если все расположения помечены, речевые фрагменты выглядят как шаблон. 

    [![Снимок экрана отмеченной во фразах сущности расположения](media/luis-quickstart-intent-and-hier-entity/all-intents-marked-with-origin-and-destination-location.png "Screenshot of Locations entity labeled in utterances")](media/luis-quickstart-intent-and-hier-entity/all-intents-marked-with-origin-and-destination-location.png#lightbox)

    Красное подчеркивание указывает, что служба LUIS не уверенна насчет сущности. Эта проблема решается путем обучения. 

## <a name="add-example-utterances-to-the-none-intent"></a>Добавление примеров речевых фрагментов в намерение None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Обучение приложения для проверки изменений намерения 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Публикация приложения, чтобы в обученную модель можно было отправлять запросы из конечной точки

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Получение намерения и прогнозирование сущности из конечной точки

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


1. Перейдите в конец URL-адреса в адресной строке и введите `Please move Carl Chamerlin from Tampa to Portland`. Последний параметр строки запроса — `q`. Это **запрос** фразы. Эта фраза не совпадает ни с какими помеченными фразами, поэтому она является хорошим тестом. В результате должно быть возвращено намерение `MoveEmployee` с извлечением иерархической сущности.

    ```json
    {
      "query": "Please move Carl Chamerlin from Tampa to Portland",
      "topScoringIntent": {
        "intent": "MoveEmployeeToCity",
        "score": 0.979823351
      },
      "intents": [
        {
          "intent": "MoveEmployeeToCity",
          "score": 0.979823351
        },
        {
          "intent": "None",
          "score": 0.0156363435
        }
      ],
      "entities": [
        {
          "entity": "portland",
          "type": "Location::Destination",
          "startIndex": 41,
          "endIndex": 48,
          "score": 0.6044041
        },
        {
          "entity": "tampa",
          "type": "Location::Origin",
          "startIndex": 32,
          "endIndex": 36,
          "score": 0.739491045
        }
      ]
    }
    ```
    
    При этом прогнозируется правильное намерение, а для массива сущностей в соответствующем свойстве **entities** существует как целевое, так и исходное значение.
    
## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Связанные сведения

* [Типы сущностей и их задачи в LUIS](luis-concept-entity-types.md) — основные сведения
* [Обучение активной версии приложения LUIS](luis-how-to-train.md)
* [Как опубликовать предложение](luis-how-to-publish-app.md)
* [Тестирование приложения LUIS на портале LUIS](luis-interactive-test.md)
* [Сравнение ролей и иерархических сущностей](luis-concept-roles.md#roles-versus-hierarchical-entities)
* [Шаблоны повышают точность прогнозирования](luis-concept-patterns.md)

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как создать намерение и добавить пример с речевыми фрагментами для данных об исходном и конечном расположении, полученных с учетом контекста. После обучения и публикации приложения клиентское приложение может использовать данную информацию для создания и перемещения билета с соответствующей информацией.

> [!div class="nextstepaction"] 
> [Сведения о добавлении составной сущности](luis-tutorial-composite-entity.md) 
