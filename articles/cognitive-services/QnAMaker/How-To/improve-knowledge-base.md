---
title: Расширение базы знаний в QnA Maker
titleSuffix: Azure Cognitive Services
description: Активное обучение позволяет улучшить качество базы знаний, предлагая на основе пользовательских отзывов альтернативные вопросы для пар существующих вопросов и ответов. Проверенные предложения можно добавить к существующим вопросам или отклонить. База знаний не изменяется автоматически. Необходимо принять предложения для изменения вступили в силу. Так вы добавите новые вопросы, не меняя и не удаляя существующие.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/21/2019
ms.author: diberry
ms.openlocfilehash: 739ae64c6b32958271260bcbd01b339c1b108f11
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337431"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Использование активного обучения для повышения знаний

Активное обучение позволяет улучшить качество базы знаний, предлагая на основе пользовательских отзывов альтернативные вопросы для пар существующих вопросов и ответов. Проверенные предложения можно добавить к существующим вопросам или отклонить. 

База знаний не изменяется автоматически. Чтобы обновить ее, необходимо принять предложения. Так вы добавите новые вопросы, не меняя и не удаляя существующие.

## <a name="what-is-active-learning"></a>Что такое активное обучение?

QnA Maker изучает новые варианты вопросов с помощью механизма явных и неявных отзывов.
 
* Неявные отзывы — средство ранжирования понимает, что на пользовательский вопрос есть несколько ответов со сходными оценками, воспринимая это как отзыв. 
* Явные отзывы — если из базы знаний возвращаются ответы со сходными оценками, клиентское приложение спрашивает пользователя, какой из вопросов является правильным. Явные отзывы пользователя отправляются в QnA Maker через API обучения. 

Каждый метод передает средству ранжирования сгруппированные по сходству запросы.

## <a name="how-active-learning-works"></a>Принцип работы активного обучения

Активное обучение активируется на основе оценок нескольких лучших ответов, возвращаемых QnA Maker на любой заданный запрос. Если оценки отличаются незначительно, запрос считается возможным _предложением_ для каждого из возможных вариантов ответа. 

Все предложения группируются по сходству, и лучшие предложения для альтернативных вопросов отображаются с учетом частоты конкретных запросов пользователей. Активное обучение предоставляет наилучшие возможные предложения, если конечные точки получают приемлемое количество разных запросов использования.

Когда кластеризованы 5 или больше подобных запросов, каждые 30 минут, QnA Maker предлагает вопросы пользователей в конструктор базы знаний, чтобы утвердить или отклонить.

После вопросы предлагаются на портале QnA Maker, необходимо просмотреть и принять или отклонить эти предложения. 

## <a name="upgrade-your-version-to-use-active-learning"></a>Обновить версию для использования активное обучение

Активное обучение поддерживается в среде выполнения версии 4.4.0 и выше. Если ваша база знаний создана с помощью более ранней версии, [обновите среду выполнения](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates), чтобы воспользоваться этой функцией. 

## <a name="best-practices"></a>Рекомендации

Рекомендации по использованию активного обучения см. в статье [Рекомендации по использованию базы знаний QnA Maker](../Concepts/best-practices.md#active-learning).

## <a name="score-proximity-between-knowledge-base-questions"></a>Сходные оценки для вопросов из базы знаний

Если вопрос получает оценку с высокой степенью достоверности (например, 80 %), диапазон оценок, используемых для активного обучения, расширяется (приблизительно на 10 %). Если оценка достоверности снижается (например, до 40 %), диапазон оценок также сокращается (приблизительно на 4 %). 

Для определения сходства оценок применяется сложный алгоритм. Диапазоны в предыдущих примерах не требуют внесения исправлений. Они позволяют понять действие самого алгоритма.

## <a name="turn-on-active-learning"></a>Включение активного обучения

По умолчанию активное обучение отключено. Чтобы просматривать предлагаемые вопросы, включите эту функцию. 

1. Выберите **публикации** Чтобы опубликовать базу знаний. Активное обучение запросы собираются из только конечная точка GenerateAnswer API прогноза. Запросы к области теста на портале Qna Maker не влияют на активное обучение.

1. Для этого щелкните свое **имя** и перейдите в раздел [**Service Settings**](https://www.qnamaker.ai/UserSettings) (Параметры службы) в верхнем правом углу экрана на портале QnA Maker.  

    ![Включение активное обучение возможных альтернатив вопрос, на странице параметров службы. Выберите свое имя пользователя в правом верхнем меню, а затем выберите параметры службы.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Найдите службу QnA Maker, а затем активируйте переключатель **Active Learning** (Активное обучение). 

    [![На странице параметров службы Переключите режим активное обучение. Если вы не сможете переключить функцию, может потребоваться обновление службы.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    Когда вы включите **активное обучение**, база знаний будет предлагать новые вопросы с регулярными интервалами с учетом вопросов, отправленных пользователем. Вы можете отключить **активное обучение** с помощью этого же переключателя.

## <a name="add-active-learning-suggestion-to-knowledge-base"></a>Добавление предложений активного обучения в базу знаний

1. Чтобы просмотреть предлагаемые вопросы, на странице базы знаний щелкните **Edit** (Изменить) и нажмите **Show Suggestions (Показать предложения)**. 

    [![В разделе "Изменить" на портале выберите Показать предложения, чтобы увидеть новый вопрос альтернативы активное обучение.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Фильтровать базы знаний с парами вопросов и ответов, чтобы показывать только варианты, выбрав **фильтр по предложения**.

    [![Используйте фильтр по переключателю предложения для просмотра только активное обучение предлагаемые вопрос альтернатив.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1.  В каждом разделе вопросов с предложениями показаны новые вопросы с флажками `✔` и `x` для принятия и отклонения предложений соответственно. Установите флажок, чтобы добавить вопрос. 

    [![Выберите или отклонить альтернативы активное обучение предлагаемые вопрос, выбрав зелеными флажками или красным удаления метки.](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Вы можете добавлять и удалять _все предложения_, щелкнув **Add all** (Добавить все) или **Reject all** (Отклонить все).

1. Выберите **Save and Train** (Сохранить и обучить), чтобы сохранить изменения в базе знаний.

1. Выберите **публикации** разрешить внесение изменений, доступен из GenerateAnswer API.

    Когда кластеризованы 5 или больше подобных запросов, каждые 30 минут, QnA Maker предлагает вопросы пользователей в конструктор базы знаний, чтобы утвердить или отклонить.

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

Когда пользователь выбирает один из существующих вопросов, клиентское приложение отправляет выбора пользователя с помощью API службы QnA Maker Train обратной связи. Ваши отзывы завершения активного обучения цикл обратной связи. 

Используйте [программ-роботов Azure C# пример](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-activelearning-bot) чтобы увидеть активное обучение в сценарии end-to-end.

## <a name="train-api"></a>Обучение API

Активное обучение отзыв отправляется QnA Maker вместе с запросом Train API POST. Подпись API является:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|Свойство запроса HTTP|ИМЯ|type|Назначение|
|--|--|--|--|
|Параметр URL-адреса маршрута|Идентификатор базы знаний|строка|Идентификатор GUID для базы знаний.|
|Узла поддомен|Имя ресурса QnAMaker|строка|Имя узла для вашей службы QnA Maker в вашей подписке Azure. Доступно на странице "Параметры" после ее публикации в базе знаний. |
|Заголовок|Content-Type|строка|Тип носителя текста, отправляемого в API. Значение по умолчанию —: `application/json`|
|Заголовок|Авторизация|строка|Ключ конечной точки (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Текст запроса POST|Объект JSON|JSON|Отзыв обучения|

Текст JSON имеет несколько параметров:

|Свойство текста JSON|type|Назначение|
|--|--|--|--|
|`feedbackRecords`|array|Список отзыва.|
|`userId`|строка|Идентификатор лица, принимая на предложенные вопросы. Формат идентификатора пользователя зависит от пользователя. Например адрес электронной почты может быть допустимый идентификатор пользователя в архитектуре. Необязательный элемент.|
|`userQuestion`|строка|Точный текст вопроса. Обязательный элемент.|
|`qnaID`|number|Идентификатор вопроса, найден в [GenerateAnswer ответа](metadata-generateanswer-usage.md#generateanswer-response-properties). |

Пример текста JSON выглядит как:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "<question-text>",
            "qnaId": 1
        }
    ]
}
```

Успешный ответ возвращается в состояние 204 и нет текста ответа JSON. 

<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>Активное обучение сохранен экспортированный базы знаний

Если приложение имеет активное обучение включена, и экспортировать приложение, `SuggestedQuestions` столбца в файле tsv сохраняет данные активное обучение. 

`SuggestedQuestions` Столбец представляет собой объект JSON, сведения о неявных (`autosuggested`) и явного (`usersuggested`) [отзывы](#active-learning). Пример объекта JSON для один пользователь отправил вопрос `help` является:

```JSON
[
    {
        "clusterHead": "help",
        "totalAutoSuggestedCount": 1,
        "totalUserSuggestedCount": 0,
        "alternateQuestionList": [
            {
                "question": "help",
                "autoSuggestedCount": 1,
                "userSuggestedCount": 0
            }
        ]
    }
]
```

При повторном импорте это приложение, активное обучение продолжает собирать информацию и рекомендуем предложения для вашей базы знаний. 

## <a name="next-steps"></a>Дальнейшие действия
 
> [!div class="nextstepaction"]
> [Использование метаданных с GenerateAnswer API](metadata-generateanswer-usage.md)
