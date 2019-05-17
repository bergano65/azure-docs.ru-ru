---
title: Анализ мнений
titleSuffix: Azure Cognitive Services
description: В этом руководстве создается приложение, демонстрирующее, как получить позитивные, негативные и нейтральные тональности из речевых фрагментов. Тональность определяется на основе всего высказывания.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: diberry
ms.openlocfilehash: 3315af0898cb3b18af0334a433a94242b056a8bd
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236207"
---
# <a name="tutorial--get-sentiment-of-utterance"></a>Руководство по  Получение данных о тональности речевого фрагмента

В этом руководстве создается приложение, демонстрирующее, как определить позитивные, негативные и нейтральные тональности из речевых фрагментов. Тональность определяется на основе всего высказывания.

**В этом руководстве рассмотрено, как выполнять следующие задачи.**

<!-- green checkmark -->
> [!div class="checklist"]
> * Создание нового приложения
> * Добавление анализа тональности как параметра публикации.
> * Обучать приложения
> * Публикация приложения
> * Получение данных о тональности высказывания из конечной точки

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>Анализ тональности — параметр публикации

Ниже приведены примеры фраз, передающие тональность.

|Мнение|Оценка|Фраза|
|:--|:--|:--|
|Позитивная|0,91 |Тарас Хмелев проделал отличную работу на презентации в Париже.|
|Позитивная|0,84 |Инженеры Сиэтла отлично поработали над презентацией Parker.|

Анализ тональности — это параметр публикации, который применяется к каждому высказыванию. Вам не нужно находить слова, указывающие на тональность в речевом фрагменте, и отмечать их. 

Так как это параметр публикации, вы не увидите его на страницах намерений или сущностей. Он будет отображаться в области [интерактивного тестирования](luis-interactive-test.md#view-sentiment-results) или при тестировании на странице по URL-адресу конечной точки. 


## <a name="create-a-new-app"></a>Создание нового приложения

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="add-personname-prebuilt-entity"></a>Добавление предварительно созданной сущности PersonName 


1. Выберите **Сущности** в меню навигации слева.

1. Нажмите кнопку **Add prebuilt entity** (Добавить предварительно созданную сущность).

1. Выберите следующую сущность из списка предварительно созданных сущностей, а затем выберите **Готово**:

   * **[PersonName](luis-reference-prebuilt-person.md)** 

     ![Снимок экрана выбора сущности number в диалоговом окне предварительно созданных сущностей](./media/luis-quickstart-intent-and-sentiment-analysis/add-personname-prebuilt-entity.png)

## <a name="create-an-intent-to-determine-employee-feedback"></a>Создание намерения для определения отзывов о работе сотрудников

Добавьте новое намерение, чтобы получить отзывы о сотрудниках от других работников компании. 

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Выберите **Create new intent**. (Создать намерение).

3. В качестве имени нового намерения введите `EmployeeFeedback`.

    ![Диалоговое окно для создания намерения с именем EmployeeFeedback](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. Добавьте несколько выражений, чтобы указать на хорошую работу сотрудника или область, в которой требуются улучшения:

    |Высказывания|
    |--|
    |Джон Смит хорошо проявил себя при организации встречи для коллеги, вернувшейся из декретного отпуска.|
    |Джилл Джонс хорошо проявила себя, оказав поддержку коллеге во время траура.|
    |Боб Барнс не располагал всеми необходимыми счетами для оформления документов.|
    |Тодд Томас вернул необходимые бланки с опозданием на месяц и без подписей.|
    |Кэтрин Келли не явилась на важную выездную встречу маркетологов.|
    |Денис Диллард пропустил собрание для обсуждения показателей за июнь.|
    |Марк Мэтьюз подготовил прекрасное коммерческое предложение для встречи в Гарварде.|
    |Волтер Вильямс проделал отличную работу на презентации в Стэнфорде.|

    Выберите **View options** (Параметры просмотра) и выберите **Show entity values** (Показать значения сущностей), чтобы увидеть имена.

    [![Снимок экрана с приложением LUIS и примерами фраз в намерении EmployeeFeedback](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="add-example-utterances-to-the-none-intent"></a>Добавление примеров речевых фрагментов в намерение None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Обучение приложения для проверки изменений намерения 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Настройка приложения для включения анализа тональности

1. На правой верхней панели навигации выберите **Управление** и щелкните **Параметры публикации** в меню слева.

1. Выберите параметр **Анализ тональности**, чтобы включить его. 

    ![Добавление анализа тональности как параметра публикации](./media/luis-quickstart-intent-and-sentiment-analysis/turn-on-sentiment-analysis-as-publish-setting.png)

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Публикация приложения, чтобы в обученную модель можно было отправлять запросы из конечной точки

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>Получение тональности речевого фрагмента из конечной точки

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Перейдите в конец URL-адреса и введите `Jill Jones work with the media team on the public portal was amazing`. Последний параметр строки запроса — `q`. Это **запрос** фразы. Эта фраза не совпадает ни с какими помеченными фразами, поэтому она является хорошим тестом. В результате должно быть возвращено намерение `EmployeeFeedback` с извлечением анализа тональности.
    
    ```json
    {
      "query": "Jill Jones work with the media team on the public portal was amazing",
      "topScoringIntent": {
        "intent": "EmployeeFeedback",
        "score": 0.9616192
      },
      "intents": [
        {
          "intent": "EmployeeFeedback",
          "score": 0.9616192
        },
        {
          "intent": "None",
          "score": 0.09347677
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
          "startIndex": 0,
          "endIndex": 9
        }
      ],
      "sentimentAnalysis": {
        "label": "positive",
        "score": 0.8694164
      }
    }
    ```

    Анализ тональности является положительным с оценкой 86 %. 

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Связанные сведения

* Анализ тональности предоставляется службой [Анализ текста](../Text-Analytics/index.yml) Cognitive Services. Эта функция ограничивается [поддерживаемыми языками](luis-language-support.md##languages-supported) Анализа текста.
* [Обучение активной версии приложения LUIS](luis-how-to-train.md)
* [Как опубликовать предложение](luis-how-to-publish-app.md)
* [Тестирование приложения LUIS на портале LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Дополнительная информация
В рамках этого руководства вы добавили анализ тональности как параметр публикации, чтобы извлечь значения тональности из всего высказывания.

> [!div class="nextstepaction"] 
> [Проверка фрагментов речи конечной точки в приложении для управления персоналом](luis-tutorial-review-endpoint-utterances.md) 

