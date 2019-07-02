---
title: Включение нескольких диалогов
titleSuffix: Azure Cognitive Services
description: Запросы и контекста можно использовать для управления несколькими очереди, известный как несколькими свою очередь, для вашего бота от одного вопроса, на другой. Включение нескольких является возможность иметь диалога туда и обратно, где контекст на предыдущий вопрос влияет на следующий вопрос и ответ.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/26/2019
ms.author: diberry
ms.openlocfilehash: 10249375922b47a40f71a60938cdd12ffe0f9b54
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508142"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Дальнейшие действия используйте предлагает создать несколько включает диалога

Использовать последующие приглашения командной строки и контекст для управления несколькими очереди, известный как _несколькими Включение_, для вашего бота от одного вопроса, на другой.

Чтобы увидеть, как работает несколькими свою очередь, просмотрите следующий видеоролик демонстрации:

[![Включение нескольких диалога в QnA Maker](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>Что такое диалога несколькими включить?

Не удается ответить на некоторые вопросы по одной очереди. При разработке беседы клиентского приложения (чатбот), пользователь может задать вопрос, нужно отфильтровать или оптимизирована, чтобы определить правильный ответ. Вы сделаете этот поток с вопросами возможных, предоставление пользователю с *дальнейшие действия запрашивает*.

Когда пользователь задает вопрос, QnA Maker возвращает ответ _и_ каких-либо дальнейших запросов. Этот ответ позволяет предоставить исполнению вопросы как варианты. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Пример нескольких включить разговор с чат-бот

С помощью нескольких свою очередь чатбот управляет диалог с пользователю определить окончательный ответ, как показано на следующем рисунке:

![Диалоговое окно несколькими включить с помощью запросов, на которые пользователю через диалог](../media/conversational-context/conversation-in-bot.png)

На предыдущем рисунке, пользователь начал диалог, введя **моей учетной записи**. База знаний содержит три связанных пары вопросов и ответов. Чтобы уточнить ответ, пользователь выбирает один из трех вариантов в базе знаний. Вопрос (1), содержит три дальнейших запросов, которые представлены в чат-бот как три варианта (2). 

Когда пользователь выбирает параметр (3), появится следующий список уточнение параметров (4). Эта последовательность продолжается (5), пока пользователь определяет, правильно, окончательный ответ (6).

> [!NOTE]
> На предыдущем рисунке **включить несколькими Включение** был установлен флажок, чтобы убедиться, что запросы отображаются. 

### <a name="use-multi-turn-in-a-bot"></a>Используйте очереди для нескольких программ-роботов

Чтобы управлять контекстные диалога, измените клиентское приложение, [Добавление кода для вашего бота](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting). Добавив код позволяет пользователям получать запросы.  

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Создание нескольких очередь диалога на основе структуры документа

При создании базы знаний, **заполните Базой** разделе отображает **включить несколькими включить извлечение из URL-адреса, .pdf или DOCX-файлы** "флажок". 

![Флажок включения нескольких Включение извлечения](../media/conversational-context/enable-multi-turn.png)

При выборе этого параметра импортируемого документа диалога несколькими свою очередь могут быть выведены из структуры документа. Если существует в этой структуре, QnA Maker создает последующие строки этой пары вопросов и ответов в ходе процесса импорта. 

Включение многофакторной структуры может выводиться только из URL-адреса, PDF-файлы, или DOCX-файлов. Пример структуры, просмотреть изображение [Microsoft Surface пользователь вручную PDF-файл](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf). Из-за размера этот PDF-файл, требуется ресурс QnA Maker **Ценовая категория поиска** из **B** (15 индексов) или более поздней версии. 

![! [Пример структуры в руководстве пользователя] (.. / media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

При импорте в PDF-документе, QnA Maker определяет дальнейших действий, запросов из структуры для создания грамматики голосового управления потока. 

1. В QnA Maker, выберите **Создание базы знаний**.
1. Создайте или используйте существующую службу QnA Maker. В приведенном выше примере Microsoft Surface, так как PDF-файл слишком велик для меньшего размера уровня, использовать службу QnA Maker с **службы поиска** из **B** (15 индексов) или более поздней версии.
1. Введите имя для вашей базы знаний, например **поверхности вручную**.
1. Выберите **включить несколькими включить извлечение из URL-адреса, .pdf или DOCX-файлы** "флажок". 
1. Выбор поверхности вручную URL-адрес, **https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/qna-maker/data-source-formats/product-manual.pdf** .

1. Выберите **создать Базой** кнопки. 

    После создания базы знаний откроется представление пар вопросов и ответов.

## <a name="show-questions-and-answers-with-context"></a>Показать вопросов и ответов с контекстом

Уменьшите отображаемый пары вопросов и ответов имеющих контекстные диалогов. 

Выберите **просмотреть параметры**, а затем выберите **Show контекста (Предварительная версия)** . В списке отображаются пары вопросов и ответов, содержащих по обработке результатов запросов. 

![Фильтровать пары вопросов и ответов по контекстные диалогов](../media/conversational-context/filter-question-and-answers-by-context.png)

Контекст нескольких Включение отображается в первом столбце.

![! [Столбец «контекста (Предварительная версия)»] (.. / media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

На предыдущем рисунке **#1** указывает полужирным шрифтом в столбец, который обозначает текущий вопрос. Вопрос родительским является верхний элемент в строке. Возникли вопросы, под ним — это связанный пары вопросов и ответов. Эти элементы доступны для выбора, таким образом, можно сразу перейти к другим элементам контекст. 

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>Добавление существующей пары вопросов и ответов в качестве результатов запроса

Исходный вопрос, **моей учетной записи**, имеет дальнейших запросов, например **учетные записи и входа в систему**. 

![«Учетные записи и входа в систему» ответы на вопросы и обработки результатов запросов](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Добавьте запрос по обработке результатов для существующей пары вопросов и ответов, не связанный в данный момент. Поскольку вопрос не связано с любой пары вопросов и ответов, текущее состояние представления должно быть изменено.

1. Чтобы связать существующей пары вопросов и ответов качестве обработки результатов запроса, выберите строку для пары вопросов и ответов. Surface вручную, выполните поиск **выйдите из системы** для сокращения списка.
1. В строке для **Signout**в **ответов** столбец, выберите **последующие строки Добавить**.
1. В полях **строке дальнейших действий (Предварительная версия)** всплывающем окне введите следующие значения:

    |Поле|Значение|
    |--|--|
    |Отображаемый текст|Введите **отключить устройство**. Это настраиваемый текст для отображения в окне результатов запроса.|
    |Только для контекста| Установите этот флажок. Ответ возвращается только в том случае, если вопрос указывает контекст.|
    |Ссылку, чтобы ответить|Введите **использовать экран входа** найти существующие пары вопросов и ответов.|


1.  Возвращается одно совпадение. Выберите этот ответ как дальнейших действий, а затем выберите **Сохранить**. 

    ![На странице «Командная строка дальнейших действий (Предварительная версия)»](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. После добавления обработки результатов запроса, выберите **сохранить и обучение** на верхней панели навигации.
  
### <a name="edit-the-display-text"></a>Изменить отображаемый текст 

При создании последующие строки, а существующей пары вопросов и ответов вводится в виде **ссылку, чтобы ответ**, можно ввести новое **отображения текста**. Этот текст не заменяет существующий вопрос, а не добавлять новый альтернативный вопрос. Он отличается от этих значений. 

1. Чтобы изменить отображаемый текст, найдите и выберите вопрос в **контекст** поля.
1. Выберите в строке этот вопрос, последующие строки в столбце ответов. 
1. Отображаемый текст, вы хотите изменить, а затем выберите **изменить**.

    ![Команды редактирования для отображения текста](../media/conversational-context/edit-existing-display-text.png)

1. В **строке дальнейших действий** всплывающем окне замените существующий текст. 
1. Закончив редактирование отображаемого текста, выберите **Сохранить**. 
1. В верхней панели навигации **сохранить и обучение**.


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

## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>Добавьте новую пару вопросов и ответов в качестве результатов запроса

При добавлении новой пары вопросов и ответов в базу знаний, каждая пара должна быть связана к существующей вопросу качестве обработки результатов запроса.

1. На панели инструментов базы знаний, поиск и Выбор существующей пары вопросов и ответов для **учетные записи и входа в систему**. 

1. В **ответов** столбец для этого вопроса, выберите **последующие строки Добавить**. 
1. В разделе **строке дальнейших действий (Предварительная версия)** , создайте новую строку по обработке результатов, указав следующие значения: 

    |Поле|Значение|
    |--|--|
    |Отображаемый текст|*Создание учетной записи Windows*. Пользовательский текст, отображаемый в окне результатов запроса.|
    |Только для контекста|Установите этот флажок. Этот ответ возвращается только в том случае, если вопрос указывает контекст.|
    |Ссылку, чтобы ответить|Введите следующий текст в качестве ответа:<br>*[Создание](https://account.microsoft.com/) учетную запись Windows, с учетной записью электронной почты нового или существующего*.<br>При сохранении и обучать базы данных, этот текст будет преобразован. |
    |||

    ![Создание нового приглашения вопрос и ответ](../media/conversational-context/create-child-prompt-from-parent.png)


1. Выберите **создать**, а затем выберите **Сохранить**. 

    Это действие создает новую пару вопросов и ответов и ссылки выбранного вопроса в качестве результатов запроса. **Контекст** столбец оба вопроса указывает исполнению отношение "prompt". 

1. Выберите **просмотреть параметры**, а затем выберите [ **Show контекста (Предварительная версия)** ](#show-questions-and-answers-with-context).

    Новый вопрос показано, как он связан.

    ![Создать новую строку по обработке результатов](../media/conversational-context/new-qna-follow-up-prompt.png)

    Вопрос родительского отображает новый вопрос в виде одного из его вариантов.

    ![! [Столбце контекст, оба вопроса показывает отношение обработки результатов запроса] (.. / media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. После добавления обработки результатов запроса, выберите **сохранить и обучение** в верхней панели навигации.

## <a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>Включение нескольких включить во время тестирования по обработке результатов запросов

При тестировании вопрос с дальнейшими действиями, запрашивает в **тестирования** области выберите **включить несколькими Включение**и введите свой вопрос. Ответ включает указаниям по обработке результатов.

![Ответ включает в себя дальнейшие запросы](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

Если не включить несколькими свою очередь, возвращается ответ, но последующие запросы не возвращаются.

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>Запрос JSON для возврата начальной ответ и последующие запросы

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

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>Ответа JSON для возврата начальной ответ и последующие запросы

Предыдущий раздел запрошенный ответ и все последующие запросы на **учетные записи и входа в систему**. Ответ включает запрос сведений, который находится в каталоге *.context ответы [0]* и текст, отображаемый для пользователя. 

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

`prompts` Массива содержит текст в `displayText` свойство и `qnaId` значение. Эти ответы можно показать как Далее вариантов отображается в диалоге потока и отправить выбранный `qnaId` к QnA Maker в следующем запросе. 

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Запрос JSON для возврата ответов не начальный и последующие запросы

Заливка `context` к предыдущему контексту объекта.

В следующий запрос JSON, текущий вопрос является *использования Windows Hello для входа в* и на предыдущий вопрос был *учетные записи и входа в систему*. 

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

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Ответа JSON для возврата ответов не начальный и последующие запросы

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

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>Запрос базы знаний с Идентификатором QnA Maker

В ответ на исходный вопрос, все последующие запросы и связанный с ним `qnaId` возвращается. Теперь, когда у вас есть идентификатор, это можно передать в тексте запроса по обработке результатов запроса. Если текст запроса содержит `qnaId`и объект контекста (который содержит в предыдущих свойствах QnA Maker), GenerateAnswer возвратит точный вопрос по Идентификатору, вместо того чтобы использовать алгоритм для поиска ответа, текст вопроса. 

## <a name="display-prompts-and-send-context-in-the-client-application"></a>Отображать подсказки и отправка контекста в клиентском приложении 

Добавления запросов в базе знаний и протестировать поток в области тестирования. Теперь необходимо использовать эти запросы в клиентском приложении. Для Bot Framework экране не отображаются автоматически в клиентских приложениях. Можно отобразить запросы как предлагаемые действия или кнопки как часть ответ на запрос пользователя в клиентских приложениях, включая это [пример Bot Framework](https://aka.ms/qnamakermultiturnsample) в коде. Клиентское приложение должно хранения идентификатор текущего QnA Maker и запроса к пользователю и передать их в [объект контекста для GenerateAnswer API](#a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts) для следующего запроса пользователя. 

## <a name="display-order-is-supported-in-the-update-api"></a>Порядок отображения поддерживается в API обновления

[Отображения текста и порядок отображения](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto), возвращается в ответе JSON, поддерживается для редактирования, [API обновления](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). 

<!--

FIX - Need to go to parent, then answer column, then edit answer. 

-->

## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>Создавать базы знаний с несколькими включить запросы, используя API создания

Вы можете создать обращение знаний с несколькими включить запросы с помощью [API создания QnA Maker](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create). Запросы добавляются в `context` свойства `prompts` массива. 


## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>Добавление или удаление нескольких включить запросы с помощью API обновления

Можно добавлять и удалять запросы несколькими включить с помощью [обновления API службы QnA Maker](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update).  Запросы добавляются в `context` свойства `promptsToAdd` массива и `promptsToDelete` массива. 


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о контекстно-зависимое диалогов из этого [пример диалогового окна](https://aka.ms/qnamakermultiturnsample) или Узнайте больше о [концептуальной bot проектирования для диалогов с несколькими Включение](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Перенос базы знаний](../Tutorials/migrate-knowledge-base.md)
