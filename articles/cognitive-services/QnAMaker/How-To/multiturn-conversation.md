---
title: Множественная перевращение диалогов — QnA Maker
titleSuffix: Azure Cognitive Services
description: Используйте приглашения и контекст для управления несколькими процессами, называемыми многоповоротными, для программы-робота с одного вопроса на другой. Множественная передача — это возможность иметь обратную и сквозную беседу, когда контекст предыдущего вопроса влияет на следующий вопрос и ответ.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: diberry
ms.openlocfilehash: 318df27ebb822f49c1f8881d0bf68ac7167dea36
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351296"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Использование дальнейших подсказок для создания диалога с несколькими шагами

Используйте запросы к исполнению и контекст для управления несколькими процессами, которые называются _многофакторными_, для программы-робота с одного вопроса на другой.

Чтобы увидеть, как работает Multi-Turn, просмотрите следующее демонстрационное видео:

[![Multi — включение диалога в QnA Maker](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>Что такое множественная беседа?

Ответить на некоторые вопросы можно одним из них. При проектировании бесед с клиентским приложением (Чат Bot) пользователь может задать вопрос, для которого необходимо выполнить фильтрацию или уточнение, чтобы определить правильный ответ. Вы сделаете этот поток через все возможные вопросы, предоставляя пользователю *запросы к исполнению*.

Когда пользователь запросит вопрос, QnA Maker возвращает ответ _и_ все дальнейшие подсказки. Этот ответ позволяет представлять дальнейшие вопросы как варианты выбора. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Пример многофункциональной беседы с помощью Chat Bot

С помощью множественного включения Bot робота управляет разговором с пользователем, чтобы определить окончательный ответ, как показано на следующем рисунке:

![Диалоговое окно с множественной обобщением с запросом, с помощью которого пользователь проходит через беседу.](../media/conversational-context/conversation-in-bot.png)

На предыдущем рисунке пользователь начал диалог, вводя **мою учетную запись**. В базе знаний есть три связанные пары вопросов и ответов. Чтобы уточнить ответ, пользователь выбирает один из трех вариантов в базе знаний. Вопрос (#1) содержит три следующих приглашения, которые отображаются в роботе чата в виде трех вариантов (#2). 

Когда пользователь выбирает параметр (#3), отображается следующий список параметров уточнения (#4). Эта последовательность продолжится (#5), пока пользователь не определит правильный, окончательный ответ (#6).

> [!NOTE]
> На предыдущем рисунке установлен флажок **включить множественные включения** , чтобы убедиться, что запросы отображаются. 

### <a name="use-multi-turn-in-a-bot"></a>Использование множественного включения в роботе

Для управления контекстным диалогом измените клиентское приложение, [добавив код в программу Bot](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting). Добавление кода позволяет пользователям видеть запросы.  

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Создание многофакторной беседы из структуры документа

Когда вы создаете базу знаний, в разделе **Заполнение статьи KB** отображается флажок **Включить множественное извлечение из URL-адресов, файлов PDF или DOCX** . 

![Флажок для включения множественного извлечения](../media/conversational-context/enable-multi-turn.png)

При выборе этого параметра для импортированного документа диалоговое обсуждение с множественной обобщением может быть неявно включено в структуру документа. Если эта структура существует, QnA Maker создает запрос на выдачу дальнейших действий, которые задают пару вопросов и ответов в рамках процесса импорта. 

Структура множественной переворачивания может быть выведена только из URL-адресов, PDF-файлов или файлов DOCX. Пример структуры см. в образе [PDF-файла пользовательской документации по Microsoft Surface](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf). В связи с размером этого PDF-файла для ресурса QnA Maker требуется **ценовая категория поиска** **B** (15 индексов) или выше. 

![! [Пример структуры в руководстве пользователя] (.. /медиа/конверсатионал-контекст/импорт-филе-ВИС-конверсатионал-структуре.ПНГ)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

При импорте документа PDF QnA Maker определяет дальнейшие подсказки от структуры до создания диалогового потока. 

1. В QnA Maker выберите **создать базу знаний**.
1. Создайте или используйте существующую службу QnA Maker. В предыдущем примере Microsoft Surface, поскольку PDF-файл слишком велик для меньшего уровня, используйте службу QnA Maker со **службой поиска** **B** (15 индексов) или выше.
1. Введите имя базы знаний, например **руководство по рабочей области**.
1. Установите флажок **Включить множественное извлечение из URL-адресов, файлов PDF или DOCX** . 
1. Выберите URL-адрес ручной области, **https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/qna-maker/data-source-formats/product-manual.pdf** .

1. Нажмите кнопку **создать базу знаний** . 

    После создания базы знаний отображается представление пар «вопрос-ответ».

## <a name="show-questions-and-answers-with-context"></a>Отображение вопросов и ответов с контекстом

Сократите отображаемые пары вопросов и ответов только с помощью контекстных диалогов. 

Выберите **Параметры представления**, а затем **Показать контекст (Предварительная версия)** . В списке отображаются пары вопросов и ответов, которые содержат подсказки для дальнейших действий. 

![Фильтрация пар вопросов и ответов с помощью контекстных диалогов](../media/conversational-context/filter-question-and-answers-by-context.png)

Контекст множественной переворачивания отображается в первом столбце.

![! ["Контекст (Предварительная версия)"] (.. /медиа/конверсатионал-контекст/СУРФАЦЕ-мануал-ПДФ-фоллов-УП-промпт.ПНГ)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

На предыдущем рисунке **#1** обозначает полужирный текст в столбце, который обозначает текущий вопрос. Родительский вопрос — это верхний элемент в строке. Все приведенные ниже вопросы являются связанными парами вопросов и ответов. Эти элементы доступны для выбора, поэтому можно сразу же переходить к другим элементам контекста. 

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>Добавление существующей пары вопросов и ответов в качестве дальнейших запросов

Исходный вопрос, **Моя учетная запись**, содержит дальнейшие подсказки, такие как **учетные записи и вход в**систему. 

![«Учетные записи и вход в систему» — ответы и дальнейшие подсказки](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Добавление дальнейших запросов к существующей паре вопросов и ответов, которая не связана. Так как вопрос не связан с какой-либо парой вопросов и ответов, необходимо изменить параметр текущего представления.

1. Чтобы связать существующую пару "вопрос-ответ" как дальнейшие действия, выберите строку для пары "вопрос-ответ". Для руководства по рабочей области выполните поиск по слову **выход** , чтобы сократить список.
1. В строке для **выхода**в столбце **ответ** выберите команду **Добавить дальнейшие действия**.
1. В полях во всплывающем окне **запроса к исполнению (Предварительная версия)** введите следующие значения:

    |Поле|Значение|
    |--|--|
    |Отображаемый текст|Введите **отключить устройство**. Это пользовательский текст, отображаемый в запросе к исполнению.|
    |Только контекст| Установите этот флажок. Ответ возвращается только в том случае, если вопрос указывает контекст.|
    |Ссылка на ответ|Введите **использовать экран входа** , чтобы найти существующую пару вопросов и ответов.|


1.  Возвращается одно совпадение. Выберите этот ответ в качестве дальнейших действий и нажмите кнопку **сохранить**. 

    ![Страница "запрос к исполнению (Предварительная версия)"](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. После добавления дальнейших действий выберите **сохранить и обучить** в верхней области навигации.
  
### <a name="edit-the-display-text"></a>Изменение отображаемого текста 

При создании запроса на выдачу и вводе существующей пары "вопрос-ответ" в качестве **ссылки на ответ**можно ввести новый **отображаемый текст**. Этот текст не заменяет существующий вопрос и не добавляет новый альтернативный вопрос. Он отделен от этих значений. 

1. Чтобы изменить отображаемый текст, найдите и выберите вопрос в поле **контекст** .
1. В строке для этого вопроса выберите в столбце ответ дальнейшие действия. 
1. Выберите отображаемый текст, который нужно изменить, и нажмите кнопку **изменить**.

    ![Команда редактирования для отображаемого текста](../media/conversational-context/edit-existing-display-text.png)

1. Во всплывающем окне **запроса к исполнению** измените существующий отображаемый текст. 
1. Завершив редактирование отображаемого текста, нажмите кнопку **сохранить**. 
1. В верхней панели навигации **Сохраните и обучить**.


<!--

## To find the best prompt answer, add metadata to follow-up prompts 

If you have several follow-up prompts for a specific question-and-answer pair but you know, as the knowledge base manager, that not all prompts should be returned, use metadata to categorize the prompts in the knowledge base. You can then send the metadata from the client application as part of the GenerateAnswer request.

In the knowledge base, when a question-and-answer pair is linked to follow-up prompts, the metadata filters are applied first, and then the follow-ups are returned.

1. Add metadata to each of the two follow-up question-and-answer pairs:

    |Question|Add metadata|
    |--|--|
    |*Feedback on a QnA Maker service*|"Feature":"all"|
    |*Feedback on an existing feature*|"Feature":"one"|
    
    ![The "Metadata tags" column for adding metadata to a follow-up prompt](../media/conversational-context/add-metadata-feature-to-follow-up-prompt.png) 

1. Select **Save and train**. 

    When you send the question **Give feedback** with the metadata filter **Feature** with a value of **all**, only the question-and-answer pair with that metadata is returned. QnA Maker doesn't return both question-and-answer pairs, because both don't match the filter. 

-->

## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>Добавление новой пары "вопрос-ответ" в качестве запроса к исполнению

При добавлении новой пары "вопрос-ответ" в базу знаний каждая пара должна быть связана с существующим вопросом в качестве дальнейших запросов.

1. На панели инструментов базы знаний найдите и выберите существующую пару вопросов и ответов для **учетных записей и входа**. 

1. В столбце **ответ** для этого вопроса выберите команду **Добавить дальнейшие действия**. 
1. В разделе **"дальнейшие действия" (Предварительная версия)** создайте новую строку дальнейших действий, введя следующие значения: 

    |Поле|Значение|
    |--|--|
    |Отображаемый текст|*Создайте учетную запись Windows*. Настраиваемый текст, отображаемый в запросе к исполнению.|
    |Только контекст|Установите этот флажок. Этот ответ возвращается только в том случае, если вопрос указывает на контекст.|
    |Ссылка на ответ|Введите следующий текст в качестве ответа:<br>*[Создайте](https://account.microsoft.com/) учетную запись Windows с новой или существующей учетной записью электронной почты*.<br>При сохранении и обучении базы данных этот текст будет преобразован. |
    |||

    ![Создание нового вопроса и ответа на запрос](../media/conversational-context/create-child-prompt-from-parent.png)


1. Выберите **создать**и нажмите кнопку **сохранить**. 

    Это действие создает новую пару вопросов и ответов и связывает выбранный вопрос в качестве дальнейших запросов. В столбце **контекст** для обоих вопросов указывается связь с запросом к исполнению. 

1. Выберите **Параметры представления**, а затем [**Показать контекст (Предварительная версия)** ](#show-questions-and-answers-with-context).

    Новый вопрос показывает, как он связан.

    ![Создание нового запроса к исполнению](../media/conversational-context/new-qna-follow-up-prompt.png)

    Родительский вопрос отображает новый вопрос в качестве одного из вариантов.

    ![! [В столбце контекст для обоих вопросов указывается связь с запросом к исполнению] (.. /медиа/конверсатионал-контекст/Чилд-промпт-креатед.ПНГ)](../media/conversational-context/child-prompt-created.png#lightbox)

1. После добавления дальнейших действий выберите **сохранить и обучить** на верхней панели навигации.

## <a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>Включение множественного включения во время тестирования запросов к исполнению

При проверке вопроса с помощью запросов к исполнению в области **тестирования** выберите **включить множественную**настройку, а затем введите свой вопрос. Ответ включает в себя запросы к исполнению.

![Ответ включает в себя запросы к исполнению](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

Если не включить множественный режим, ответ будет возвращен, но дальнейшие запросы не возвращаются.

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>Запрос JSON для возврата начального ответа и последующих запросов.

Используйте пустой объект `context`, чтобы запросить ответ на вопрос пользователя и включить в него запросы к исполнению. 

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>Ответ JSON для возврата начального ответа и последующих запросов.

В предыдущем разделе был запрошен ответ и все последующие запросы к **учетным записям и входу**. Ответ содержит сведения о приглашении, которые находятся в *ответах [0]. Context*, и текст, отображаемый пользователю. 

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
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start, and right-click your name. Then select Sign out. ",
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

Массив `prompts` предоставляет текст в свойстве `displayText` и значении `qnaId`. Вы можете отобразить эти ответы в качестве следующих отображаемых вариантов в потоке диалога, а затем отправить выбранный `qnaId` обратно в QnA Maker в следующем запросе. 

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Запрос JSON для возврата непервоначального ответа и дальнейших запросов

Заполните объект `context`, чтобы включить предыдущий контекст.

В следующем запросе JSON текущим вопросом является *Использование Windows Hello для входа* , а предыдущий вопрос — *учетные записи и вход*. 

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

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Ответ JSON для возврата непервоначального ответа и последующих запросов

Ответ QnA Maker _женератеансвер_ JSON содержит запросы к исполнению в свойстве `context` первого элемента в объекте `answers`:

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

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>Запрос к базе знаний с помощью идентификатора QnA Maker

В ответе на первый вопрос возвращаются все запросы к исполнению и связанные с ней `qnaId`. Теперь, когда у вас есть идентификатор, вы можете передать его в текст запроса дальнейших действий. Если текст запроса содержит `qnaId`, а объект контекста (который содержит предыдущие свойства QnA Maker), то Женератеансвер будет возвращать точный вопрос по ИДЕНТИФИКАТОРу, а не использовать алгоритм ранжирования для поиска ответа по тексту вопроса. 

## <a name="display-prompts-and-send-context-in-the-client-application"></a>Отображение запросов и отправка контекста в клиентском приложении 

Вы добавили приглашения в базу знаний и проверили последовательность в области тестирования. Теперь эти запросы необходимо использовать в клиентском приложении. В случае с Bot Framework запросы не отображаются автоматически в клиентских приложениях. Запросы можно отобразить в виде предложенных действий или кнопок в рамках ответа на запрос пользователя в клиентских приложениях, включив этот [пример этой платформы](https://aka.ms/qnamakermultiturnsample) в коде. Клиентское приложение должно хранить текущий идентификатор QnA Maker и пользовательский запрос, а также передавать их в [объект Context API женератеансвер](#a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts) для следующего пользовательского запроса. 

## <a name="display-order-is-supported-in-the-update-api"></a>Порядок просмотра поддерживается в API обновления

[Отображаемый текст и порядок показа](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto), возвращаемые в ответе JSON, поддерживаются для редактирования [API обновления](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). 

<!--

FIX - Need to go to parent, then answer column, then edit answer. 

-->

## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>Создание базы знаний с помощью многофункциональных запросов с помощью API создания

Вы можете создать базу знаний с несколькими инструкциями с помощью [API QnA Maker Create](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create). Запросы добавляются в массив `prompts` свойства `context`. 


## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>Добавление и удаление многофункциональных запросов с помощью API обновления

Вы можете добавлять или удалять многострочные запросы с помощью [API обновления QnA Maker](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update).  Запросы добавляются в массив `promptsToAdd` свойства `context` и массив `promptsToDelete`. 


## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения об контекстных диалогах см. в этом [образце диалогового окна](https://aka.ms/qnamakermultiturnsample) или дополнительные сведения о [концептуальном проектировании для нескольких диалогов](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Перенос базы знаний](../Tutorials/migrate-knowledge-base.md)
