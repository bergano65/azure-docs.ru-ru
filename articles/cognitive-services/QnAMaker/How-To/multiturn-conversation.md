---
title: Включение нескольких диалогов
titleSuffix: Azure Cognitive Services
description: Запросы и контекста можно использовать для управления несколькими очереди, известный как несколькими свою очередь, для вашего бота от одного вопроса, на другой. Несколькими свою очередь является возможность копирования и далее диалога, где контекст на предыдущий вопрос влияет на следующий вопрос и ответ.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 05/14/2019
ms.author: diberry
ms.openlocfilehash: f12b55e9b00e933e13f84832b8cc36267a1da05f
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65954861"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Дальнейшие действия используйте предлагает создать несколько включает диалога

Использовать последующие приглашения командной строки и контекст для управления несколькими очереди, известный как _несколькими Включение_, для вашего бота от одного вопроса, на другой.

Узнайте от [демонстрационный видеоролик](https://aka.ms/multiturnexample).

## <a name="what-is-a-multi-turn-conversation"></a>Что такое диалога несколькими включить?

Некоторые виды диалога невозможно выполнить в одной очереди. При разработке беседы клиентского приложения (чатбот), пользователь может запросить вопроса, на который необходимо отфильтровать или уточнять, чтобы определить правильный ответ. Этот поток с вопросами возможна, предоставляя пользователю **дальнейшие действия запрашивает**.

Когда пользователь запрашивает вопрос, QnA Maker возвращает ответ _и_ каких-либо дальнейших запросов. Это позволяет представить на вопросы по обработке результатов как варианты. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Пример нескольких включить разговор с чат-бот

Чат-бот управляет диалога вопрос по вопрос с пользователем, чтобы определить, окончательный ответ.

![Грамматики голосового управления потока управлять состояния диалога в системе многофакторной включить диалоговое окно, предоставляя запросы в ответы, представленными в виде параметров продолжить диалог.](../media/conversational-context/conversation-in-bot.png)

На предыдущем рисунке необходимо уточнить перед возвратом ответа на вопрос пользователя. В базе знаний вопрос (1), содержит четыре дальнейших запросов, представленный в чат-бот четыре варианта (2). 

Когда пользователь выбирает Выбор (3), представлен далее список уточнение вариантов (4). Это можно продолжить (5), пока не определен правильно и окончательный ответ (6).

Вам нужно изменить клиентское приложение для управления контекстные диалога.

## <a name="create-a-multi-turn-conversation-from-documents-structure"></a>Создание нескольких очередь диалога из структуры документа
При создании базы знаний, вы увидите флажок для включения нескольких Включение извлечения. Если выбран этот параметр, при импорте документа, диалог несколькими свою очередь могут быть выведены из структуры. Если существует в этой структуре, QnA Maker создает исполнению prompt пары QnA. Только несколькими включить структуру можно вывести из URL-адреса, PDF и DOCX файлы. 

Следующий снимок Microsoft Surface [PDF-файл](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf) предназначен для использования в качестве вручную. 

![! [При импорте документа, контекстные диалога могут быть выведены из структуры. Если существует в этой структуре, QnA Maker создает исполнению prompt пары QnA, как часть импорта документа.] (.. / media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

При импорте в PDF-документе, QnA Maker определяет последующие запросы из структуры для создания грамматики голосового управления потока. 

![! [При импорте в PDF-документе, QnA Maker определяет последующие запросы из структуры для создания грамматики голосового управления потока. ](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

## <a name="show-questions-and-answers-with-context"></a>Показать вопросов и ответов с контекстом

1. Уменьшите пары вопросов и ответов, отображается только для пользователей с контекстные диалогов. Выберите **просмотреть параметры**, а затем выберите **Show контекста (Предварительная версия)**. Список будет пустым, пока вы не добавите первой пары вопросов и ответов, предлагая по обработке результатов. 

    ![Фильтровать вопрос и ответить на пары, контекстные диалогов](../media/conversational-context/filter-question-and-answers-by-context.png)

## <a name="add-new-qna-pair-as-follow-up-prompt"></a>Добавьте новую пару QnA как последующие строки

1. Выберите **QnA, добавьте пары**. 
1. Введите новый текст вопроса, `Give feedback.` ответ из `What kind of feedback do you have?`.

1. В **ответов** столбец для этого вопроса, выберите **последующие строки Добавить**. 
1. **Строке дальнейших действий (Предварительная версия)** всплывающее окно позволяет искать возникает вопрос существующий или введите новый вопрос. Создайте новую строку путем ввода следующих значений: 

    |Текстовое поле|Value|
    |--|--|
    |**Отображаемый текст**|`Feedback on an QnA Maker service`|
    |**Ссылку, чтобы ответить**|`How would you rate QnA Maker??`|
    |||

    ![Создание нового приглашения QnA](../media/conversational-context/create-child-prompt-from-parent.png)

1. Проверьте **только для контекста**. **Только для контекста** параметр указывает, что этот пользователь текст будет понятен _только_ Если в ответ на предыдущий вопрос. В этом сценарии текст приглашения не имеет смысла любой как автономный вопрос, он имеет смысл только в контексте на предыдущий вопрос.
1. Выберите **создать** выберите **Сохранить**. 

    Это создается новая пара вопросов и ответов и связывается выбранного вопроса качестве обработки результатов запроса. **Контекст** столбец оба вопроса указывает исполнению отношение "prompt". 

    ![! [Столбце контекст, оба вопроса показывает отношение обработки результатов запроса.] (.. / media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Выберите **последующие строки Добавить** для `Give feedback` вопрос, чтобы добавить другой строке по обработке результатов. Откроется **строке дальнейших действий (Предварительная версия)** всплывающее окно.

1. Создайте новую строку путем ввода следующих значений:

    |Текстовое поле|Value|
    |--|--|
    |**Отображаемый текст**|`Feedback on an existing feature`|
    |**Ссылку, чтобы ответить**|`Which feature would you like to give feedback on?`|
    |||

1. Проверьте **только для контекста**. **Только для контекста** параметр указывает, что этот пользователь текст будет понятен _только_ Если в ответ на предыдущий вопрос. В этом сценарии текст приглашения не имеет смысла любой как автономный вопрос, он имеет смысл только в контексте на предыдущий вопрос.

1. Щелкните **Сохранить**. 

    Это создается новый вопрос и связывается вопрос как prompt вопросом к `Give feedback` вопрос.
    
    На этом этапе top вопрос имеет два запроса по обработке результатов, понравилось на предыдущий вопрос `Give feedback`.

    ![! [На этом этапе top вопрос имеет два запроса по обработке результатов, понравилось на предыдущий вопрос, «Отправить отзыв».] (.. / media/conversational-context/all-child-prompts-created.png)](../media/conversational-context/all-child-prompts-created.png#lightbox)

1. Выберите **сохранить и обучение** для обучения knowledge base, новые вопросы. 

## <a name="add-existing-qna-pair-as-follow-up-prompt"></a>Добавить существующие пары QnA как последующие строки

1. Если вы хотите связать существующей пары QnA качестве обработки результатов запроса, выберите строку для пары вопросов и ответов.
1. Выберите **последующие строки Добавить** в этой строке.
1. В **строке дальнейших действий (Предварительная версия)** всплывающее окно, в поле поиска введите текст ответа. Возвращаются все совпадения. Выбрать ответ как дальнейших действий и проверьте **только для контекста**, а затем выберите **Сохранить**. 

    ![Найдите ссылку на строке дальнейших действий к диалоговому окну ответов для ответ, используя текст ответа.](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

    После добавления обработки результатов запроса, не забудьте выбрать **сохранить и обучение**.
  
<!--

## To find best prompt answer, add metadata to follow-up prompts 

If you have several follow-up prompts for a given QnA pair, but you know as the knowledge base manager, that not all prompts should be returned, use metadata to categorize the prompts in the knowledge base, then send the metadata from the client application as part of the GenerateAnswer request.

In the knowledge base, when a question-and-answer pair is linked to follow-up prompts, the metadata filters are applied first, then the follow-ups are returned.

1. For the two follow-up QnA pairs, add metadata to each one:

    |Question|Add metadata|
    |--|--|
    |`Feedback on an QnA Maker service`|"Feature":"all"|
    |`Feedback on an existing feature`|"Feature":"one"|
    
    ![Add metadata to follow-up prompt so it can be filtered in conversation response from service](../media/conversational-context/add-metadata-feature-to-follow-up-prompt.png) 

1. Save and train. 

    When you send the question `Give feedback` with the metadata filter `Feature` with a value of `all`, only the QnA pair with that metadata will be returned. Both QnA pairs are not returned because they both do not match the filter. 

-->

## <a name="test-the-qna-set-to-get-all-the-follow-up-prompts"></a>Запрашивает тест, для которого настраиваются QnA, чтобы получать все дальнейшие действия

При тестировании на вопрос с дальнейшими действиями, запрашивает в **теста** области выберите **включить несколькими Включение**и введите свой вопрос. Ответ включает указаниям по обработке результатов.

![При тестировании на вопрос в области тестирования, ответ включает указаниям по обработке результатов.](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

## <a name="json-request-to-return-initial-answer-and-follow-up-prompts"></a>Запрос JSON для возврата начальной ответов и последующие запросы

Используйте пустой `context` объект для запроса ответ на вопрос пользователя и включить последующие запросы. 

```JSON
{
  "question": "accounts and signing in",
  "top": 30,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="json-response-to-return-initial-answer-and-follow-up-prompts"></a>Ответ JSON для возврата начальной ответов и последующие запросы

Выше Запрошенный ответ и все последующие запросы на `Accounts and signing in`. Ответ включает данные приглашения, расположенный `answers[0].context`, включать текст, отображаемый для пользователя. 

```JSON
{
    "answers": [
        {
            "questions": [
                "Accounts and signing in"
            ],
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way they like. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 86.96,
            "id": 37,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 38,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    },
                    {
                        "displayOrder": 1,
                        "qnaId": 39,
                        "qna": null,
                        "displayText": "Use Windows Hello to sign in"
                    },
                    {
                        "displayOrder": 2,
                        "qnaId": 40,
                        "qna": null,
                        "displayText": "Sign out"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 32.27,
            "id": 38,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start , and right-click your name. Then select Sign out. ",
            "score": 27.0,
            "id": 40,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ],
    "debugInfo": null
}
```

`prompts` Массива содержит текст в `displayText` свойство и `qnaId` значение, поэтому эти ответы можно отображать в виде следующего отображаемых вариантов в поток передачи сообщений. 

## <a name="json-request-to-return-non-initial-answer-and-follow-up-prompts"></a>Запрос JSON для возврата ответов не начальный и последующие запросы

Заливка `context` включаемый предыдущего контекста.

В следующий запрос JSON, текущий вопрос является `Use Windows Hello to sign in` и на предыдущий вопрос был `Accounts and signing in`. 

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 30,
  "userId": "Default",
  "isTest": false,
  "qnaId": 39,
  "context": {
    "previousQnAId": 37,
    "previousUserQuery": "accounts and signing in"
  }
}
``` 

##  <a name="json-response-to-return-non-initial-answer-and-follow-up-prompts"></a>Ответ JSON для возврата ответов не начальный и последующие запросы

QnA Maker _GenerateAnswer_ ответ JSON включает дальнейшие запросы в `context` свойства первого элемента в `answers` объекта:

```JSON
{
    "answers": [
        {
            "questions": [
                "Give feedback"
            ],
            "answer": "What kind of feedback do you have?",
            "score": 100.0,
            "id": 288,
            "source": "Editorial",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 291,
                        "qna": null,
                        "displayText": "Feedback on an QnA Maker service"
                    },
                    {
                        "displayOrder": 0,
                        "qnaId": 292,
                        "qna": null,
                        "displayText": "Feedback on an existing feature"
                    }
                ]
            }
        }
    ]
}
```

## <a name="displaying-prompts-and-sending-context-in-the-client-application"></a>Отображение запросов и отправка контекста в клиентском приложении 

Если вы добавили запросы в базе знаний и тестирования потока в области тестирования, запросы не запустится автоматически отображаются в клиентских приложениях. Можно отобразить запросы как предлагаемые действия или кнопки как часть ответ на запрос пользователя в клиенте приложений, включая это [пример Bot Framework](https://aka.ms/qnamakermultiturnsample) в коде. Клиентское приложение должно хранения идентификатор текущего QnA и запроса к пользователю и передать их в [объект контекста для GenerateAnswer API](#json-request-to-return-non-initial-answer-and-follow-up-prompts) для следующего запроса пользователя.

## <a name="display-order-supported-in-api"></a>Порядок отображения поддерживается в API

Порядок отображения, возвращается в ответе JSON, поддерживается для редактирования только API-интерфейсом. 

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о контекстно-зависимое беседы с [пример диалогового окна](https://aka.ms/qnamakermultiturnsample) вы можете получить дополнительные [концептуальной bot проектирования для диалогов с несколькими Включение](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Перенос базы знаний](../Tutorials/migrate-knowledge-base.md)
