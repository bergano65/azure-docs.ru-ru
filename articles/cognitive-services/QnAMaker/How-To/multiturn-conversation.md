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
ms.date: 05/20/2019
ms.author: diberry
ms.openlocfilehash: 0aab2250d6692d7db99c55b23604c08f5fe619a6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235683"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Дальнейшие действия используйте предлагает создать несколько включает диалога

Использовать последующие приглашения командной строки и контекст для управления несколькими очереди, известный как _несколькими Включение_, для вашего бота от одного вопроса, на другой.

В следующем видео демонстрации чтобы увидеть, как это сделать.

[![](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample).

## <a name="what-is-a-multi-turn-conversation"></a>Что такое диалога несколькими включить?

Не удается ответить на некоторые вопросы по одной очереди. При разработке беседы клиентского приложения (чатбот), пользователь может запросить вопроса, на который необходимо отфильтровать или уточнять, чтобы определить правильный ответ. Этот поток с вопросами возможна, предоставляя пользователю **дальнейшие действия запрашивает**.

Когда пользователь запрашивает вопрос, QnA Maker возвращает ответ _и_ каких-либо дальнейших запросов. Это позволяет представить на вопросы по обработке результатов как варианты. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Пример нескольких включить разговор с чат-бот

Чат-бот управляет диалога с пользователем вопрос по вопрос, чтобы определить окончательный ответ.

![Грамматики голосового управления потока управлять состояния диалога в системе многофакторной включить диалоговое окно, предоставляя запросы в ответы, представленными в виде параметров продолжить диалог.](../media/conversational-context/conversation-in-bot.png)

На предыдущем рисунке, пользователь ввел `My account`. База знаний содержит три пары связанных QnA. Пользователь должен выбрать один из трех вариантов для уточнения ответ. В базе знаний вопрос (1), содержит три дальнейших запросов, представленные в чат-бот как один из трех вариантов (2). 

Когда пользователь выбирает Выбор (3), представлен далее список уточнение вариантов (4). Это можно продолжить (5), пока не определен правильно и окончательный ответ (6).

В предыдущем образе **включить несколькими Включение** выбрано для отображения запросов. 

### <a name="using-multi-turn-in-a-bot"></a>Использование нескольких Включение в программы-робота

Вам нужно изменить клиентское приложение для управления контекстные диалога. Необходимо будет добавить [код для вашего бота](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting) на экране см. в разделе.  

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Создание нескольких очередь диалога на основе структуры документа

При создании базы знаний, вы увидите флажок для включения нескольких Включение извлечения. 

![При создании базы знаний, вы увидите флажок для включения нескольких Включение извлечения.](../media/conversational-context/enable-multi-turn.png)

Если выбран этот параметр, при импорте документа, диалог несколькими свою очередь могут быть выведены из структуры. Если существует в этой структуре, QnA Maker создает исполнению prompt пары QnA. 

Только несколькими включить структуру можно вывести из URL-адреса, PDF и DOCX файлы. 

Следующий снимок Microsoft Surface [PDF-файл](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf) предназначен для использования в качестве вручную. Из-за размера этот PDF-файл, Azure QnA Maker ресурсов требует поиска, ценовой B (15 индексов) или более поздней версии. 

![! [При импорте документа, контекстные диалога могут быть выведены из структуры. Если существует в этой структуре, QnA Maker создает исполнению prompt пары QnA, как часть импорта документа.] (.. / media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

При импорте в PDF-документе, QnA Maker определяет последующие запросы из структуры для создания грамматики голосового управления потока. 

1. В **шаг 1**выберите **Создание базы знаний** на верхней панели навигации.
1. В **шаг 2**создайте или используйте существующую службу QnA. Убедитесь, что для использования службы QnA со службой поиска столбца B (15 индексов) или более поздней версии, так как область вручную PDF-файл слишком велик для меньшего размера уровня.
1. В **шаг 3**, введите имя для вашей базы знаний, например `Surface manual`.
1. В **шаг 4**выберите **включить несколькими включить извлечение из URL-адреса, .pdf или DOCX-файлы.** Выберите URL-адрес для поверхности вручную

    ```text
    https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/qna-maker/data-source-formats/product-manual.pdf
    ```

1. Выберите **создать Базой** кнопки. 

    После создания набора знаний, отображает представление пар вопросов и ответов.

## <a name="show-questions-and-answers-with-context"></a>Показать вопросов и ответов с контекстом

Уменьшите пары вопросов и ответов, отображается только для пользователей с контекстные диалогов. 

1. Выберите **просмотреть параметры**, а затем выберите **Show контекста (Предварительная версия)** . В списке отображаются пары вопросов и ответов, содержащий последующие запросы. 

    ![Фильтровать вопрос и ответить на пары, контекстные диалогов](../media/conversational-context/filter-question-and-answers-by-context.png)

2. Контекст нескольких Включение отображается в первом столбце.

    ![! [При импорте в PDF-документе, QnA Maker определяет последующие запросы из структуры для создания грамматики голосового управления потока. ](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

    На предыдущем рисунке #1 указывает, выделенный жирным текст на столбец, который обозначает текущий вопрос. Вопрос родительским является верхний элемент в строке. Возникли вопросы ниже — это связанный пары вопросов и ответов. Эти элементы выделяются, поэтому можно сразу перейти к другим элементам контекст. 

## <a name="add-existing-qna-pair-as-follow-up-prompt"></a>Добавить существующие пары QnA как последующие строки

Исходный вопрос `My account` имеет последующие запросы, например `Accounts and signing in`. 

![Правильно возвращает исходный вопрос «Моя учетная запись» «Учетные записи и входа в систему» ответить и уже имеет связанного последующие запросы.](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Добавьте запрос по обработке результатов для существующей пары QnA, сейчас не связан. Поскольку вопрос не связано с любой парой QnA, текущее состояние представления необходимо изменить.

1. Чтобы связать существующей пары QnA качестве обработки результатов запроса, выберите строку для пары вопросов и ответов. Surface вручную, выполните поиск `Sign out` для сокращения списка.
1. В строке для `Signout`выберите **последующие строки Добавить** из **ответов** столбца.
1. В **строке дальнейших действий (Предварительная версия)** всплывающем окне введите следующее:

    |Поле|Value|
    |--|--|
    |Отображаемый текст|`Turn off the device`. Это текст, выбранный для отображения в окне результатов запроса.|
    |Только для контекста|Выбран. Этот ответ будет возвращается только в том случае, если вопрос указывает контекст.|
    |Ссылка на ответов|Введите `Use the sign-in screen` для поиска существующей пары QnA.|


1.  Возвращается одно совпадение. Выберите этот ответ как дальнейших действий, а затем **Сохранить**. 

    ![Найдите ссылку на строке дальнейших действий к диалоговому окну ответов для ответ, используя текст ответа.](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. После добавления обработки результатов запроса, не забудьте выбрать **сохранить и обучение** на верхней панели навигации.
  
### <a name="edit-the-display-text"></a>Изменить отображаемый текст 

При создании последующие строки, а существующей пары QnA выбран в качестве **ссылку, чтобы ответ**, можно ввести новое **отображения текста**. Этот текст не заменяет существующий вопрос, а не добавлять новый альтернативный вопрос. Он отличается от этих значений. 

1. Чтобы изменить отображаемый текст, найдите и выберите вопрос в **контекст** поля.
1. На этот вопрос строке выберите последующие строки в столбце ответов. 
1. Выберите текст, вы хотите изменить, а затем выберите **изменить**.

    ![Выберите текст, который вы хотите изменить, а затем выберите "Правка".](../media/conversational-context/edit-existing-display-text.png)

1. **Строке дальнейших действий** всплывающее окно позволяет изменять существующие отображаемый текст. 
1. После завершения редактирования отображаемого текста, выберите **Сохранить**. 
1. Не забудьте выбрать **сохранить и обучение** на верхней панели навигации.


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

## <a name="add-new-qna-pair-as-follow-up-prompt"></a>Добавьте новую пару QnA как последующие строки

Добавьте новую пару QnA базы знаний. Должна быть связана пара QnA к вопросу существующих качестве обработки результатов запроса.

1. Панель инструментов в базе знаний, поиск и Выбор существующей пары QnA для `Accounts and Signing In`. 

1. В **ответов** столбец для этого вопроса, выберите **последующие строки Добавить**. 
1. **Строке дальнейших действий (Предварительная версия)** , создайте новую строку по обработке результатов, указав следующие значения: 

    |Текстовое поле|Value|
    |--|--|
    |**Отображаемый текст**|`Create a Windows Account`. Это текст, выбранный для отображения в окне результатов запроса.|
    |**Только для контекста**|Выбран. Этот ответ будет возвращается только в том случае, если вопрос указывает контекст.|
    |**Ссылку, чтобы ответить**|Введите следующий текст в качестве ответа:<br>`[Create](https://account.microsoft.com/) a Windows account with a new or existing email account.`<br>При сохранении и обучать базы данных, этот текст будет преобразован в |
    |||

    ![Создание нового приглашения QnA](../media/conversational-context/create-child-prompt-from-parent.png)


1. Выберите **создать** выберите **Сохранить**. 

    Это создается новая пара вопросов и ответов и связывается выбранного вопроса качестве обработки результатов запроса. **Контекст** столбец оба вопроса указывает исполнению отношение "prompt". 

1. Изменение **просмотреть параметры** для [Показать контекст](#show-questions-and-answers-with-context).

    Новый вопрос показано, как он связан.

    ![Создать новую строку по обработке результатов ](../media/conversational-context/new-qna-follow-up-prompt.png)

    Вопрос родительского показано новый вопрос в качестве одного из его вариантов.

    ![! [Столбце контекст, оба вопроса показывает отношение обработки результатов запроса.] (.. / media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. После добавления обработки результатов запроса, не забудьте выбрать **сохранить и обучение** на верхней панели навигации.

## <a name="enable-multi-turn-when-testing-follow-up-prompts"></a>Включение нескольких свою очередь, при появлении запроса тестирования дальнейшие действия

При тестировании на вопрос с дальнейшими действиями, запрашивает в **теста** области выберите **включить несколькими Включение**и введите свой вопрос. Ответ включает указаниям по обработке результатов.

![При тестировании на вопрос в области тестирования, ответ включает указаниям по обработке результатов.](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

Если не включить несколькими свою очередь, будет возвращаться ответ, но последующие запросы не возвращаются.

## <a name="json-request-to-return-initial-answer-and-follow-up-prompts"></a>Запрос JSON для возврата начальной ответов и последующие запросы

Используйте пустой `context` объект для запроса ответ на вопрос пользователя и включить последующие запросы. 

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
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
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way he or she likes. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 100.0,
            "id": 15,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    },
                    {
                        "displayOrder": 1,
                        "qnaId": 17,
                        "qna": null,
                        "displayText": "Use Windows Hello to sign in"
                    },
                    {
                        "displayOrder": 2,
                        "qnaId": 18,
                        "qna": null,
                        "displayText": "Sign out"
                    },
                    {
                        "displayOrder": 0,
                        "qnaId": 79,
                        "qna": null,
                        "displayText": "Create a Windows Account"
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start , and right-click your name. Then select Sign out. ",
            "score": 38.01,
            "id": 18,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Turn off the device"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 27.53,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

`prompts` Массива содержит текст в `displayText` свойство и `qnaId` потока значение, поэтому эти ответы можно отображать в виде Далее вариантов отображается в диалоге, отправить значения, выбранного для QnA Maker в следующий запрос. 

## <a name="json-request-to-return-non-initial-answer-and-follow-up-prompts"></a>Запрос JSON для возврата ответов не начальный и последующие запросы

Заливка `context` включаемый предыдущего контекста.

В следующий запрос JSON, текущий вопрос является `Use Windows Hello to sign in` и на предыдущий вопрос был `Accounts and signing in`. 

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "qnaId": 17,
  "context": {
    "previousQnAId": 15,
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
                "Use Windows Hello to sign in"
            ],
            "answer": "**Use Windows Hello to sign in**\n\nSince Surface Pro 4 has an infrared (IR) camera, you can set up Windows Hello to sign in just by looking at the screen. \n\nIf you have the Surface Pro 4 Type Cover with Fingerprint ID (sold separately), you can set up your Surface sign you in with a touch. \n\nFor more info, see What is Windows Hello? on Windows.com. ",
            "score": 100.0,
            "id": 17,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Meet Surface Pro 4"
            ],
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere’s a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you’re ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3” display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth® \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
            "score": 21.92,
            "id": 3,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 4,
                        "qna": null,
                        "displayText": "Ports and connectors"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 19.04,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

## <a name="query-the-knowledge-base-with-the-qna-id"></a>Запрос базы знаний с Идентификатором QnA

В ответ на исходный вопрос, все последующие запросы и связанный с ним `qnaId` возвращается. Теперь, когда у вас есть идентификатор, это можно передать в тексте запроса по обработке результатов запроса. Если текст запроса содержит `qnaId`и объект контекста (который содержит в предыдущих свойствах QnA), GenerateAnswer возвратит точный вопрос по Идентификатору, вместо того чтобы использовать алгоритм для поиска ответа, текст вопроса. 

## <a name="displaying-prompts-and-sending-context-in-the-client-application"></a>Отображение запросов и отправка контекста в клиентском приложении 

Добавления запросов в базе знаний и протестировать поток в области тестирования. Теперь необходимо использовать эти запросы в клиентском приложении. Для Bot Framework запросы не запустится автоматически отображаются в клиентских приложениях. Можно отобразить запросы как предлагаемые действия или кнопки как часть ответ на запрос пользователя в клиенте приложений, включая это [пример Bot Framework](https://aka.ms/qnamakermultiturnsample) в коде. Клиентское приложение должно хранения идентификатор текущего QnA и запроса к пользователю и передать их в [объект контекста для GenerateAnswer API](#json-request-to-return-non-initial-answer-and-follow-up-prompts) для следующего запроса пользователя. 

## <a name="display-order-supported-in-api"></a>Порядок отображения поддерживается в API

[Отображения текста и порядок отображения](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto), возвращается в ответе JSON, поддерживается для редактирования, [API обновления](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). 

ИСПРАВЛЕНИЕ — нужно будет перейти к родительской, а затем столбец ответов, а затем отредактируйте ответов. 

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о контекстно-зависимое беседы с [пример диалогового окна](https://aka.ms/qnamakermultiturnsample) вы можете получить дополнительные [концептуальной bot проектирования для диалогов с несколькими Включение](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Перенос базы знаний](../Tutorials/migrate-knowledge-base.md)
