---
title: Многократные разговоры - NA Maker
description: Используйте запросы и контекст для управления несколькими поворотами, известными как мульти-поворот, для вашего бота от одного вопроса к другому. Мульти-поворот — это возможность вести разговор взад и вперед, когда контекст предыдущего вопроса влияет на следующий вопрос и ответ.
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 8ef244e1b6baf480189bb90ea5ff53138a6f377a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261471"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Использование дальнейших подсказок для создания диалога с несколькими шагами

Используйте последующие запросы и контекст для управления несколькими поворотами, известными как _мульти-поворот,_ для вашего бота от одного вопроса к другому.

Чтобы узнать, как работает мульти-поворот, просмотрите следующее демонстрационное видео:

[![Многократный разворот разговор в Зна Maker](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>Что такое многоповоротный разговор?

На некоторые вопросы нельзя ответить за один ход. При проектировании клиентского приложения (чат-бот) разговоры, пользователь может задать вопрос, который должен быть отфильтроваен или уточнен, чтобы определить правильный ответ. Вы делаете этот поток через вопросы возможно, представляя пользователю *последующие подсказки.*

Когда пользователь задает вопрос, создатель nA возвращает ответ _и_ любые последующие запросы. Этот ответ позволяет представить последующие вопросы в качестве выбора.

> [!CAUTION]
> Запросы с несколькими поворотами не извлекаются из документов часто задаваемых вопросов. Если вам нужна многоповоротная добыча, удалите вопросительные знаки, обозначающие пары nA как часто задаваемые вопросы.

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Пример многоповоротной беседы с чат-ботом

С несколькими поворотами чат-бот управляет разговором с пользователем, чтобы определить окончательный ответ, как показано на следующем изображении:

![Диалог с подсказками, которые направляют пользователя через разговор](../media/conversational-context/conversation-in-bot.png)

На предыдущем изображении пользователь начал разговор, введя **мою учетную запись**. База знаний имеет три взаимосвязанные пары вопросов и ответов. Чтобы уточнить ответ, пользователь выбирает один из трех вариантов в базе знаний. Вопрос (#1), имеет три последующих запросов, которые представлены в чат-бот в виде трех вариантов (#2).

Когда пользователь выбирает опцию (#3), представляется следующий список вариантов уточнения (#4). Эта последовательность продолжается (#5) до тех пор, пока пользователь не определит правильный, окончательный ответ (#6).


### <a name="use-multi-turn-in-a-bot"></a>Используйте мульти-поворот в боте

После публикации КБ можно выбрать кнопку **Create Bot** для развертывания бота nA Maker в службу ботов Azure. Запросы будут отображаться в чате клиентов, которые вы включили для вашего бота.

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Создание многоповоротного разговора из структуры документа

При создании базы знаний в разделе **Populate ваш** раздел KB отображает **извлечение нескольких поворотов Enable из URL-адресов, .pdf или .docx файлы** чек-бокса.

![Проверить поле для обеспечения многоповоротной добычи](../media/conversational-context/enable-multi-turn.png)

При выборе этой опции создатель nA извлекает иерархию, присутствуюую в структуре документа. Иерархия преобразуется в последующие запросы, а корень иерархии служит в качестве родительского nA. В некоторых документах корень иерархии не имеет содержания, которое могло бы служить ответом, вы можете предоставить "Текст ответа по умолчанию", который будет использоваться в качестве замены текста ответа для извлечения таких иерархий.

Структура мульти-поворота может быть выведена только из URL-адресов, файлов PDF или DOCX. Для примера структуры просмотрите изображение [файла PDF-файла пользователя Microsoft Surface под руководством.](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)

![! «Пример структуры в руководстве пользователя» (.. /media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

### <a name="building-your-own-multi-turn-document"></a>Создание собственного документа с несколькими оборотами

Если вы создаете документ с несколькими поворотами, пожалуйста, имейте в виду следующие рекомендации:

* Используйте заголовки и подзаголовки для обозначения иерархии. Например, можно h1 обозначить родительские qnA и h2, чтобы обозначить nA, которое следует воспринимать как оперативное. Используйте небольшой размер заголовка для обозначения последующей иерархии. Не используйте стиль, цвет или какой-либо другой механизм, чтобы подразумевать структуру в документе, nA Maker не будет извлекать многооборотные подсказки.

* Первый символ заголовка должен быть капитализирован.

* Не заканчивайте заголовок с `?`вопросительным знаком, .

* Можно использовать [документ образца](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx) в качестве примера для создания собственного документа с несколькими оборотами.

### <a name="adding-files-to-a-multi-turn-kb"></a>Добавление файлов в КБ с несколькими оборотами

При добавлении иерархического документа создатель nA определяет последующие запросы из структуры для создания разговорного потока.

1. В qnA Maker выберите существующую базу знаний, которая была создана с **помощью многоповоротного извлечения из URL-адресов, .pdf или .docx файлов.** включено.
1. Перейдите на страницу **«Настройки»,** выберите файл или URL для добавления.
1. **Сохранить и обучить** базу знаний.

> [!Caution]
> Поддержка использования экспортируемого файла tSV или XLS с несколькими оборотами знаний в качестве источника данных для новой или пустой базы знаний не поддерживается. Для добавления экспортируемых многооборотных запросов в базу знаний необходимо **импортировать** этот тип файла со **страницы «Настройки»** портала «NA Maker».


## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>Создание базы знаний с помощью многоповоротных запросов с помощью API Create

Вы можете создать случай знаний с помощью мультиповоротных запросов с помощью API создания [API.](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create) Запросы добавляются в `context` `prompts` массив свойства.

## <a name="show-questions-and-answers-with-context"></a>Показать вопросы и ответы с контекстом

Уменьшите отображаемые пары вопросов и ответов только на пары с контекстуальными разговорами.

Выберите **параметры просмотра,** а затем выберите **контекст Показать.** Список отображает пары вопросов и ответов, содержащие последующие запросы.

![Фильтр ас-ответ пар по контекстуальным разговорам](../media/conversational-context/filter-question-and-answers-by-context.png)

Контекст с несколькими поворотами отображается в первом столбце.

![! «Контекст (ПРЕВИАН)» колонка» (.. /media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

На предыдущем изображении **#1** указывает жирный текст в столбце, что означает текущий вопрос. Родительский вопрос — это верхний элемент в строке. Любые вопросы ниже это связанные пары вопросов и ответов. Эти элементы можно выбрать, так что вы можете сразу перейти к другим элементам контекста.

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>Добавление существующей пары вопросов и ответов в качестве последующего запроса

Оригинальный вопрос, **Моя учетная запись**, имеет последующие запросы, такие как **счета и входе в**.

![Ответы и последующие запросы "Счета и входе"](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Добавьте подсказку по последующей деятельности в существующую пару вопросов и ответов, которая в настоящее время не связана. Поскольку вопрос не связан с парой вопросов и ответов, необходимо изменить настройку текущего представления.

1. Чтобы связать существующую пару вопросов и ответов в качестве запроса, выберите строку для пары вопросов и ответов. Для руководства Surface ищите **знак,** чтобы уменьшить список.
1. В строке для **Signout**в столбце **«Ответ»** выберите **подсказку «Добавить последующий запрос».**
1. В полях в **всплывающем** окне прослеждение последующего окна введите следующие значения:

    |Поле|Значение|
    |--|--|
    |Отображаемый текст|Введите **Выключить устройство.** Это пользовательский текст для отображения в последующей подсказке.|
    |Контекст-только| Выберите этот флажок. Ответ возвращается только в том случае, если вопрос определяет контекст.|
    |Ссылка на ответ|Введите **Используйте экран входа,** чтобы найти существующую пару вопросов и ответов.|


1.  Один матч возвращается. Выберите этот ответ в качестве последующей деятельности, а затем выберите **Сохранить**.

    ![Страница "Подсказка (PREVIEW)" страница](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. После добавления запроса последующей деятельности выберите **Сохранить и тренироваться** в верхней навигации.

### <a name="edit-the-display-text"></a>Редактирование текста дисплея

При создании запроса последующих запросов и введении существующей пары вопросов и ответов в виде **ссылки для ответа,** вы можете ввести новый **текст Display.** Этот текст не заменяет существующий вопрос и не добавляет новый альтернативный вопрос. Он отделен от этих значений.

1. Чтобы отсеивать текст отображения, ищите и выберите вопрос в поле **Контекста.**
1. В строке для этого вопроса выберите запрос последующей деятельности в столбце ответа.
1. Выберите текст отображения, который вы хотите отменить, а затем выберите **Edit.**

    ![Команда редактирования текста дисплея](../media/conversational-context/edit-existing-display-text.png)

1. В **всплывающем** окне последующего всплывающее окно измените существующий текст отображения.
1. Когда вы закончите редактирование текста дисплея, выберите **Сохранить**.
1. В верхней панели навигации, **Сохранить и поезд**.


## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>Добавление новой пары вопросов и ответов в качестве последующего запроса

При добавлении новой пары вопросов и ответов в базу знаний каждая пара должна быть связана с существующим вопросом в качестве последующего запроса.

1. На панели инструментов базы знаний, поиск и выберите существующую пару вопросов и ответов для **учетных записей и входе в**систему.

1. В колонке **«Ответ»** для этого вопроса выберите **подсказку «Добавить продолжение».**
1. Под **запросом последующей деятельности (PREVIEW)** создайте новую подсказку последующей деятельности, введя следующие значения:

    |Поле|Значение|
    |--|--|
    |Отображаемый текст|*Создайте учетную запись Windows*. Пользовательский текст для отображения в последующей подсказке.|
    |Контекст-только|Выберите этот флажок. Этот ответ возвращается только в том случае, если вопрос определяет контекст.|
    |Ссылка на ответ|Введите следующий текст в качестве ответа:<br>* [Создайте](https://account.microsoft.com/) учетную запись Windows с новой или существующей учетной записью электронной почты.*<br>При сохранении и обучении базы данных этот текст будет преобразован. |
    |||

    ![Создайте новый оперативный вопрос и ответ](../media/conversational-context/create-child-prompt-from-parent.png)


1. Выберите **Создать новый,** а затем выберите **Сохранить**.

    Это действие создает новую пару вопросов и ответов и связывает выбранный вопрос в качестве последующего запроса. В столбце **Context** для обоих вопросов указывается быстрая взаимосвязь для последующей деятельности.

1. Выберите **параметры просмотра,** а затем выберите [**контекст Показать (PREVIEW).**](#show-questions-and-answers-with-context)

    Новый вопрос показывает, как это связано.

    ![Создание нового запроса последующей деятельности](../media/conversational-context/new-qna-follow-up-prompt.png)

    Родительский вопрос отображает новый вопрос как один из его вариантов.

    ![! «В колонке «Контекст» по обоим вопросам указывается на оперативную связь с последующими сообщениями» (.. /media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. После добавления запроса последующей деятельности выберите **Сохранить и тренироваться** в верхней панели навигации.

<a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>

## <a name="view-multi-turn-during-testing-of-follow-up-prompts"></a>Просмотр мультиоборота во время тестирования последующих запросов

При тестировании вопроса с помощью последующих запросов в **тестовом** стеле ответ включает в себя последующие запросы.

![Ответ включает в себя последующие запросы](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>Запрос JSON на возврат первоначального ответа и последующих запросов

Используйте `context` пустой объект, чтобы запросить ответ на вопрос пользователя и включить последующие запросы.

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>Ответ JSON на возвращение первоначального ответа и последующих запросов

Предыдущий раздел запросил ответ и любые последующие запросы для **счетов и входе в**систему . Ответ включает в себя оперативную информацию, которая находится в *ответах, и*текст для отображения пользователю.

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

Массив `prompts` предоставляет текст `displayText` в свойстве и значении. `qnaId` Вы можете показать эти ответы в качестве следующего отображаемого выбора в потоке разговоров, а затем отправить выбранный `qnaId` обратно в SnA Maker в следующем запросе.

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Запрос JSON на возврат неисходимого ответа и последующих запросов

Заполните `context` объект, чтобы включить предыдущий контекст.

В следующем запросе JSON, текущий вопрос *: Использование Windows Hello для вхустройства,* а предыдущий вопрос был *учетным записями и входе.*

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

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Ответ JSON на возврат неисходимого ответа и последующих запросов

Ответ NA Maker _GenerateAnswer_ JSON включает в себя `context` последующие запросы в `answers` свойстве первого элемента объекта:

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
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere's a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you're ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3" display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth&reg; \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
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

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>Запрос базы знаний с идентификатором производителя nA

Если вы строите пользовательское приложение с использованием функции multi-turn. В ответе на первоначальный вопрос возвращаются любые `qnaId` последующие запросы и связанные с ними. Теперь, когда у вас есть идентификатор, вы можете передать это в орган запроса последующих запросов. Если тело запроса `qnaId`содержит объект и контекстный объект (который содержит предыдущие свойства nA Maker), то GenerateAnswer вернет точный вопрос по ID, вместо того, чтобы использовать алгоритм ранжирования, чтобы найти ответ текстом вопроса.


## <a name="display-order-is-supported-in-the-update-api"></a>Заказ дисплея поддерживается в API обновления

Текст [отображения и порядок отображения,](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto)возвращенный в ответ JSON, поддерживается для редактирования [API обновления.](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)

## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>Добавление или удаление мультиповоротных запросов с помощью API обновления

Вы можете добавлять или удалять мульти-оборотные запросы с помощью [API обновления создателя.](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)  Запросы добавляются в `context` `promptsToAdd` массив свойства и `promptsToDelete` массив.

## <a name="export-knowledge-base-for-version-control"></a>База экспортных знаний для управления версиями

Создатель версии поддерживает управление версиями, включая многооборотные шаги в экспортируемом файле.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о контекстных беседах из этого [образца диалога](https://aka.ms/qnamakermultiturnsample) или узнайте больше о [концептуальном дизайне ботов для многоходовых разговоров.](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0)

> [!div class="nextstepaction"]
> [Перенос базы знаний](../Tutorials/migrate-knowledge-base.md)
