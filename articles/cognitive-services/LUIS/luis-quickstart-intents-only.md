---
title: Руководство по Прогнозирование намерений — LUIS
titleSuffix: Azure Cognitive Services
description: В этом руководстве создается пользовательское приложение, которое прогнозирует намерение пользователя. Это приложение является простейшим типом приложения LUIS, так как оно не извлекает различные элементы данных из текста высказывания, такие как адреса электронной почты или даты.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: 7139876f64841a877e688ec6faf03597c527d1f2
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375822"
---
# <a name="tutorial-build-luis-app-to-determine-user-intentions"></a>Руководство по Создание приложения LUIS для определения намерений пользователя

В этом руководстве вы создадите пользовательское приложение "Управление персоналом", которое прогнозирует пользовательские намерения на основе высказываний (текста). 

**В этом руководстве рассмотрено, как выполнять следующие задачи.**

> [!div class="checklist"]
> * Создание нового приложения 
> * Создание намерений
> * Добавление примеров высказываний
> * Обучать приложения
> * Публикация приложения
> * Получение намерения из конечной точки


[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="user-intentions-as-intents"></a>Намерения пользователей

Приложение предназначено для определения намерения беседы на естественном языке. 

`Are there any new positions in the Seattle office?`

Эти **Намерения** подразделяются на категории. 

Это приложение имеет несколько намерений. 

|Намерение|Назначение|
|--|--|
|ApplyForJob|Определяет, подает ли пользователь заявку на работу.|
|GetJobInformation|Определяет, ищет ли пользователь информацию о работе в целом или о конкретной вакансии.|
|Нет|Определяет, спрашивает ли пользователь что-либо, на что приложение не должно отвечать. Это намерение предоставляется как часть создания приложения и не может быть удалено. |

## <a name="create-a-new-app"></a>Создание нового приложения

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-job-information"></a>Создание намерения для получения информации о работе

1. Выберите **Create new intent**. (Создать намерение). Введите имя нового намерения `GetJobInformation`. Это намерение предсказывается, когда пользователь хочет получить сведения об открытых вакансиях в компании. 

    ![Снимок экрана с диалоговым окном New intent (Новое намерение) Интеллектуальной службы распознавания речи (LUIS)](media/luis-quickstart-intents-only/create-intent.png "Screenshot of Language Understanding (LUIS) New intent dialog")

1. Нажмите кнопку **Готово**.

2. Добавьте в намерение несколько примеров высказываний, которые ожидаются от пользователя.

    | Примеры высказываний|
    |--|
    |Сегодня опубликованы новые вакансии?|
    |Есть вакансии в офисе в Сиэтле?|
    |Есть ли какие-либо вакансии для удаленной работы для инженеров?|
    |Есть ли должность для работы с базами данных?|
    |Я ищу коворкинг в офисе в Тампе.|
    |Есть ли стажировка в офисе в Сан-Франциско?|
    |Если ли какая-либо работа на условиях частичной занятости для студентов?|
    |Ищу новую работу с обязанностями в бухгалтерском учете|
    |Ищу работу для человека, говорящего на двух языках, в Нью-Йорке.|
    |Ищу новую работу с обязанностями в бухгалтерском учете.|
    |Новые вакансии?|
    |Показать все вакансии для инженеров, которые были добавлены за последние 2 дня.|
    |Сегодняшние объявления о работе?|
    |Какие вакансии для бухгалтеров доступны в лондонском офисе?|
    |Какие должности доступны для старших инженеров?|
    |Где находится список вакансий|

    [![Снимок экрана: ввод новых высказываний для намерения MyStore](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "Screenshot of entering new utterances for MyStore intent")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

    Предоставляя _примеры высказываний_, вы обучаете службу LUIS прогнозированию определенного типа высказываний для этого намерения. 

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]    

## <a name="add-example-utterances-to-the-none-intent"></a>Добавление примеров речевых фрагментов в намерение None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>Обучение приложения перед тестированием и публикацией

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Публикация приложения для получения запроса из конечной точки

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-from-the-endpoint"></a>Получение прогноза намерений из конечной точки

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Перейдите в конец URL-адреса в адресной строке и введите `I'm looking for a job with Natural Language Processing`. Последний параметр строки запроса — `q`. Это **запрос** фразы. Это высказывание не похоже ни на один из примеров высказываний. Это необходимая проверка, которая должна вернуть намерение `GetJobInformation` в качестве намерения с наибольшим уровнем совпадения. 

    ```JSON
    {
      "query": "I'm looking for a job with Natural Language Processing",
      "topScoringIntent": {
        "intent": "GetJobInformation",
        "score": 0.9923871
      },
      "intents": [
        {
          "intent": "GetJobInformation",
          "score": 0.9923871
        },
        {
          "intent": "None",
          "score": 0.007810574
        }
      ],
      "entities": []
    }
    ```

    Параметр строки запроса `verbose=true` означает включение **всех намерений** в результаты запроса приложения. Массив сущностей пуст, потому что в настоящее время у этого приложения нет никаких сущностей. 

    Результат JSON определяет намерение с наивысшим показателем как свойство **`topScoringIntent`** . Все оценки находятся в диапазоне 0–1, наилучшие оценки — те, которые ближе к 1. 

## <a name="create-intent-for-job-applications"></a>Создание намерения для заявок на работу

Вернитесь на портал LUIS и создайте новое намерение, чтобы определить, является ли пользовательское высказывание заявкой на работу.

1. Выберите **Создать** в меню в правом верхнем углу, чтобы вернуться к созданию приложения.

1. Выберите **Intents** (Намерения) в меню слева, чтобы получить список намерений.

1. Выберите **Создать намерение** и введите имя `ApplyForJob`. 

    ![Диалоговое окно LUIS для создания намерения](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

1. Добавьте в намерение несколько фраз, которые вы ожидаете от пользователя, например:

    | Примеры высказываний|
    |--|
    |Заполнить заявку для вакансии 123456|
    |Это мое резюме должность 654234|
    |Вот мое резюме на должность секретаря на неполный рабочий день.|
    |Я подаю заявку на получение офисной работы в сфере искусства, прилагая эти документы.|
    |Я подаю заявку на прохождение летней практики в области научных исследований и разработки в Сан-Диего|
    |Я запрашиваю разрешение на отправку своего резюме на временную должность в кафе.|
    |Я отправляю свое резюме на должность в новой команде Autocar в Колумбусе, Огайо|
    |Я хочу подать заявку на вакансию в бухгалтерии|
    |Документы на прохождение практики по бухгалтерскому учету на должности 456789 здесь|
    |Должность 567890 и мои документы|
    |Мои документы для прохождения практики в сфере бухгалтерского учета в Талсе прилагаются.|
    |Мои документы на должность курьера на праздники|
    |Отправьте мое резюме для заявки на новую бухгалтерскую должность в Сиэтле|
    |Отправить резюме на должность инженера|
    |Это моя краткая биография для должности 234123 в Тампе.|

<!--

    [![Screenshot of entering new utterances for ApplyForJob intent](media/luis-quickstart-intents-only/utterance-applyforjob.png "Screenshot of entering new utterances for ApplyForJob intent")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    The labeled intent is outlined in red because LUIS is currently uncertain the intent is correct. Training the app tells LUIS the utterances are on the correct intent. 

-->

## <a name="train-again"></a>Повторное обучение

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-again"></a>Повторная публикация

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-again"></a>Получение прогноза намерения еще раз

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. В новом окне браузера введите `Can I submit my resume for job 235986` в конце URL-адреса. 

    ```json
    {
      "query": "Can I submit my resume for job 235986",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9634406
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9634406
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0171300638
        },
        {
          "intent": "None",
          "score": 0.00670867041
        }
      ],
      "entities": []
    }
    ```

    Результаты включают в себя новое намерение **ApplyForJob**, а также существующие намерения. 

## <a name="client-application-next-steps"></a>Дальнейшие действия клиентского приложения

Выполнив запрос, приложение LUIS возвращает ответ JSON. LUIS не предоставляет ответы на пользовательские высказывания, а только определяет, какой тип информации запрашивается на естественном языке. Дальнейшие строки при общении предоставляются клиентским приложением, таким как служба Azure Bot. 

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Связанные сведения

* [Типы сущностей](luis-concept-entity-types.md)
* [Обучение активной версии приложения LUIS](luis-how-to-train.md)
* [Как опубликовать предложение](luis-how-to-publish-app.md)
* [Тестирование приложения LUIS на портале LUIS](luis-interactive-test.md)
* [Служба Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>Дополнительная информация

В этом руководстве создано приложение "Управление персоналом" и 2 намерения, добавлены примеры высказываний для каждого намерения, в том числе, для намерения "None", которые обучены, опубликованы и протестированы на конечной точке. Ниже приведены основные этапы создания модели LUIS. 

Продолжите работу с этим приложением, [добавив простую сущность и список фраз](luis-quickstart-primary-and-secondary-data.md).

> [!div class="nextstepaction"]
> [Добавление предварительно созданных намерений и сущностей к этому приложению](luis-tutorial-prebuilt-intents-entities.md)
