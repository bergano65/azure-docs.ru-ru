---
title: 'Распознавание речи: контекстно-зависимые данные с ролями'
titleSuffix: Azure Cognitive Services
description: Поиск связанных данных на основе контекста. Например, места исходного расположения и назначения при перемещении из одного здания и офиса в другое связаны между собой.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: diberry
ms.openlocfilehash: 98c2ad15d836162608affbfbc89908a15e274cee
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59528777"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Руководство по Извлечение из речевого фрагмента данных, связанных с контекстом

Из данного руководства можно узнать, как выполнять поиск связанных частей данных на основе контекста. Например, исходное и конечное расположения для перемещения из одного города в другой. Могут потребоваться фрагменты данных, которые связаны друг с другом.  

Ранее в этом руководстве использовались иерархические сущности. Роли сущностей позволяют отказаться от использования иерархического типа сущности. Роли можно использовать с любым предварительно созданным или настраиваемым типом сущности. Они используются в примерах речевых фрагментов и шаблонов. 

**В этом руководстве рассмотрено, как выполнять следующие задачи.**

> [!div class="checklist"]
> * Создание приложения
> * Добавление намерения 
> * Получение сведений об источнике и назначении с помощью ролей.
> * Train
> * Опубликовать
> * Получение намерений и ролей сущностей из конечной точки.

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="related-data"></a>Связанные данные

Это приложение определяет, в каких случаях сотрудник должен перемещаться из исходного города в целевой. Оно использует предварительно созданную сущность geographyV2 для определения названий городов. Роли используются, чтобы определить тип расположения (исходное или конечное) в речевом фрагменте.

Роль должна использоваться, когда извлекаемые данные сущности:

* связаны между собой в контексте речевого фрагмента;
* содержат конкретные слова, обозначающие каждую роль. Примеры этих слов включают в себя: из/в, выход/переход, от/к.
* Обе эти роли часто находятся в одном речевом фрагменте, позволяя LUIS учиться на частом использовании в контексте.
* Должны быть сгруппированы и обработаны клиентским приложением в качестве единицы информации.

## <a name="create-a-new-app"></a>Создание нового приложения

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-move-employees-between-cities"></a>Создание намерения для перемещения сотрудников между городами

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Выберите **Create new intent**. (Создать намерение). 

1. Введите `MoveEmployeeToCity` во всплывающем диалоговом окне и нажмите кнопку **Done** (Готово). 

    ![Снимок экрана диалогового окна создания намерения с](./media/tutorial-entity-roles/create-new-intent-move-employee-to-city.png)

1. Добавьте примеры фраз в намерение.

    |Примеры высказываний|
    |--|
    |Переместить Джона В. Смита из Сиэтла в Орландо.|
    |Переместить Джил Джонс из Сиэтла в Каир.|
    |Перевести Джона Джексона из Тампа в Атланту. |
    |Перевести Дебру Дотери в Талсы из Чикаго.|
    |Перем. Джил Джонс из Каира в Тампа.|
    |Перевести Елис Андерсон в Окленд из Редмонда.|
    |Направить Карла Чамерлина из Сан-Франциско в Редмонд.|
    |Отправить Стива Стандиша из Сан-Диего в Бельвю. |
    |Перебросить Таннера Томпсона из Канзас-Сити в Чикаго.|

    [![Снимок экрана приложения LUIS с новыми фразами в намерении MoveEmployee](./media/tutorial-entity-roles/hr-enter-utterances.png)](./media/tutorial-entity-roles/hr-enter-utterances.png#lightbox)

## <a name="add-prebuilt-entity-geographyv2"></a>Добавление предварительно созданной сущности geographyV2

Предварительно созданная сущность, geographyV2, извлекает сведения о расположении, включая названия городов. Так как речевые фрагменты содержат два названия городов, связанные друг с другом в контексте, роли можно использовать для извлечения этого контекста.

1. Выберите **Entities** (Сущности) в меню навигации слева.

1. Выберите **Add prebuilt entity** (Добавить предварительно созданную сущность), а затем выберите `geo` в строке поиска, чтобы отфильтровать предварительно созданные сущности. 

    ![Добавление предварительно созданной сущности geographyV2 в приложение](media/tutorial-entity-roles/add-geographyV2-prebuilt-entity.png)
1. Установите флажок и выберите **Done** (Готово).
1. В списке **Entities** (Сущности) выберите **geographyV2**, чтобы открыть новую сущность. 
1. Добавьте две роли, `Origin` и `Destination`. 

    ![Добавление ролей в предварительно созданную сущность](media/tutorial-entity-roles/add-roles-to-prebuilt-entity.png)
1. Выберите **Intents** (Намерения) в области навигации слева, затем выберите намерение **MoveEmployeeToCity**. Обратите внимание на то, что названия городов помечены с использованием предварительно созданной сущности **geogrpahyV2**.
1. В первом речевом фрагменте списка выберите исходное расположение. Откроется раскрывающееся меню. Выберите **geographyV2** из списка, затем проведите указателем мыши по меню, чтобы выбрать **Origin** (Источник).

    [![Снимок экрана: обозначение города в качестве исходного расположения](media/tutorial-entity-roles/tag-origin-city-with-role.png "Снимок экрана: обозначение города в качестве исходного расположения")](media/tutorial-entity-roles/tag-origin-city-with-role.png#lightbox)

1. Используя метод из предыдущего шага, пометьте все роли расположений во всех речевых фрагментах. 

    [![Снимок экрана отмеченной во фразах сущности расположения](media/tutorial-entity-roles/all-locations-marked-with-roles.png "Screenshot of Locations entity labeled in utterances")](media/tutorial-entity-roles/all-locations-marked-with-roles.png#lightbox)

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
          "entity": "geographyV2",
          "role": "Destination",
          "startIndex": 41,
          "endIndex": 48,
          "score": 0.6044041
        },
        {
          "entity": "geographyV2",
          "role": "Origin",
          "startIndex": 32,
          "endIndex": 36,
          "score": 0.739491045
        }
      ]
    }
    ```
    
    При этом прогнозируется правильное намерение, а для массива сущностей в соответствующем свойстве **entities** существует как целевая, так и исходная роли.
    
## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Связанные сведения

* [Основные понятия сущностей](luis-concept-entity-types.md)
* [Основные понятия ролей](luis-concept-roles.md)
* [Список предварительно созданных сущностей](luis-reference-prebuilt-entities.md)
* [Обучение активной версии приложения LUIS](luis-how-to-train.md)
* [Как опубликовать предложение](luis-how-to-publish-app.md)
* [Тестирование приложения LUIS на портале LUIS](luis-interactive-test.md)
* [Роли](luis-concept-roles.md)

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как создать намерение и добавить пример с речевыми фрагментами для данных об исходном и конечном расположении, полученных с учетом контекста. После обучения и публикации приложения клиентское приложение может использовать данную информацию для создания и перемещения билета с соответствующей информацией.

> [!div class="nextstepaction"] 
> [Сведения о добавлении составной сущности](luis-tutorial-composite-entity.md) 
