---
title: Расширение базы знаний в QnA Maker
titleSuffix: Azure Cognitive Services
description: ''
author: diberry
manager: nitinme
displayName: active learning, suggestion, dialog prompt, train api, feedback loop, autolearn, auto-learn, user setting, service setting, services setting
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/29/2019
ms.author: diberry
ms.openlocfilehash: 6feb521aa47ca813b3067451c8c77111deb60e73
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55874011"
---
# <a name="use-active-learning-to-improve-knowledge-base"></a>Использование активного обучения для расширения базы знаний

Активное обучение позволяет улучшить качество базы знаний, предлагая на основе пользовательских отзывов альтернативные вопросы для пар существующих вопросов и ответов. Проверенные предложения можно добавить к существующим вопросам или отклонить. 

База знаний не изменяется автоматически. Чтобы обновить ее, необходимо принять предложения. Так вы добавите новые вопросы, не меняя и не удаляя существующие.

## <a name="active-learning"></a>Активное обучение

QnA Maker изучает новые варианты вопросов с помощью механизма явных и неявных отзывов.
 
* Неявные отзывы — средство ранжирования понимает, что на пользовательский вопрос есть несколько ответов со сходными оценками, воспринимая это как отзыв. 
* Явные отзывы — если из базы знаний возвращаются ответы со сходными оценками, клиентское приложение спрашивает пользователя, какой из вопросов является правильным. Явные отзывы пользователя отправляются в QnA Maker через API обучения. 

Каждый метод передает средству ранжирования сгруппированные по сходству запросы.

После этого QnA Maker предлагает пользовательские вопросы разработчику базы знаний, чтобы утвердить или отклонить их.

## <a name="how-active-learning-works"></a>Принцип работы активного обучения

Активное обучение активируется на основе оценок нескольких лучших ответов, возвращаемых QnA Maker на любой заданный запрос. Если оценки отличаются незначительно, запрос считается возможным _предложением_ для каждого из возможных вариантов ответа. 

Все предложения группируются по сходству, и лучшие предложения для альтернативных вопросов отображаются с учетом частоты конкретных запросов пользователей. Активное обучение предоставляет наилучшие возможные предложения, если конечные точки получают приемлемое количество разных запросов использования.

## <a name="upgrade-version-to-use-active-learning"></a>Обновление версии для использования активного обучения

Активное обучение поддерживается в среде выполнения версии 4.4.0 и выше. Если ваша база знаний создана с помощью более ранней версии, [обновите среду выполнения](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates), чтобы воспользоваться этой функцией. 

## <a name="best-practices"></a>Рекомендации

Рекомендации по использованию активного обучения см. в статье [Рекомендации по использованию базы знаний QnA Maker](../Concepts/best-practices.md#active-learning).

## <a name="score-proximity-between-knowledge-base-questions"></a>Сходные оценки для вопросов из базы знаний

Если вопрос получает оценку с высокой степенью достоверности (например, 80 %), диапазон оценок, используемых для активного обучения, расширяется (приблизительно на 10 %). Если оценка достоверности снижается (например, до 40 %), диапазон оценок также сокращается (приблизительно на 4 %). 

Для определения сходства оценок применяется сложный алгоритм. Диапазоны в предыдущих примерах не требуют внесения исправлений. Они позволяют понять действие самого алгоритма.

## <a name="turn-on-active-learning"></a>Включение активного обучения

По умолчанию активное обучение отключено. Чтобы просматривать предлагаемые вопросы, включите эту функцию. 

1. Для этого щелкните свое **имя** и перейдите в раздел [**Service Settings**](https://www.qnamaker.ai/UserSettings) (Параметры службы) в верхнем правом углу экрана на портале QnA Maker.  

    ![Включение активного обучения на странице параметров службы](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Найдите службу QnA Maker, а затем активируйте переключатель **Active Learning** (Активное обучение). 

    [![Включение активного обучения на странице параметров службы](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    Когда вы включите **активное обучение**, база знаний будет предлагать новые вопросы с регулярными интервалами с учетом вопросов, отправленных пользователем. Вы можете отключить **активное обучение** с помощью этого же переключателя.

## <a name="add-active-learning-suggestion-to-knowledge-base"></a>Добавление предложений активного обучения в базу знаний

1. Чтобы просмотреть предлагаемые вопросы, на странице базы знаний щелкните **Edit** (Изменить) и нажмите **Show Suggestions (Показать предложения)**. 

    [![Включение отображения предложений на странице параметров службы](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Отфильтруйте базу знаний по парам вопросов и ответов, чтобы отображались только предложения. Для этого щелкните **Filter by Suggestions** (Фильтр по предложениям).

    [![Фильтрация по предложениям для просмотра только пар вопросов и ответов](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1.  В каждом разделе вопросов с предложениями показаны новые вопросы с флажками `✔` и `x` для принятия и отклонения предложений соответственно. Установите флажок, чтобы добавить вопрос. 

    [![Включение активного обучения на странице параметров службы](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Вы можете добавлять и удалять _все предложения_, щелкнув **Add all** (Добавить все) или **Reject all** (Отклонить все).

1. Выберите **Save and Train** (Сохранить и обучить), чтобы сохранить изменения в базе знаний.


## <a name="determine-best-choice-when-several-questions-have-similar-scores"></a>Определение лучшего варианта, когда у нескольких вопросов сходные оценки

Если оценки нескольких вопросов практически совпадают, разработчик клиентского приложения может запросить уточнения.

### <a name="use-the-top-property-in-the-generateanswer-request"></a>Использование свойства top в запросе GenerateAnswer

Если вы отправляете вопрос в QnA Maker, чтобы получить ответ, можно использовать фрагмент запроса JSON для возврата нескольких лучших ответов:

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

Если клиентское приложение (например, чат-бот) получает ответ, возвращаются три лучших вопроса:

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ •+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

### <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Дальнейшие действия клиентского приложения, когда у нескольких вопросов сходные оценки

Клиентское приложение отображает все вопросы, позволяя пользователю выбрать вопрос, который наиболее точно передает его намерение. 

Когда пользователь выберет один из существующих вопросов, пользовательский отзыв отправляется в API [обучения](http://www.aka.ms/activelearningsamplebot) QnA Maker, чтобы продолжить цикл получения отзывов для активного обучения. 

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

Дополнительные сведения об использовании активного обучения см. на примере [службы Azure Bot на C#](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-activelearning-bot).

## <a name="next-steps"></a>Дополнительная информация
 
> [!div class="nextstepaction"]
> [Use QnA Maker API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) (Использование API службы QnA Maker)
