---
title: Создавайте обзоры модерации с помощью консоли REST API - Модератор контента
titleSuffix: Azure Cognitive Services
description: Используйте AA-иновки обзора содержимого Azure Content Review для создания обзоров изображений или текстовых обзоров для модерации человека.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: pafarley
ms.openlocfilehash: a9726e41a84926d00d48b51e31f534a3d8c2fe0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "72757137"
---
# <a name="create-human-reviews-rest"></a>Создание человеческих отзывов (REST)

[Отзывы](./review-api.md#reviews) хранить и отображать содержимое для человеческих модераторов для оценки. Когда пользователь завершает просмотрение, результаты отправляются в указанную конечную точку обратного вызова. В этом руководстве вы узнаете, как настроить обзоры с помощью апогея REST-обзора через консоль API. Как только вы поймете структуру AIS, вы можете легко портировать эти вызовы на любую платформу, совместимую с REST.

## <a name="prerequisites"></a>Предварительные требования

- Войти или создать учетную запись на сайте [инструмента «Обзор](https://contentmoderator.cognitive.microsoft.com/) модератора контента».

## <a name="create-a-review"></a>Создание проверки

Чтобы создать отзыв, перейдите на страницу **[обзора - Создайте](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** справочную страницу API и выберите кнопку для ключевой области (вы можете найти это в URL-адресе endpoint на странице **проверки** [инструментов Обзора).](https://contentmoderator.cognitive.microsoft.com/) Это запускает консоль API, где вы можете легко построить и запустить REST API звонки.

![Обзор - Получить выбор региона](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Введите параметры вызова REST

Введите значения для **teamName**и **Ocp-Apim-Subscription-Key:**

- **TeamName**: Идентификатор команды, созданный при настройке учетной записи [инструмента «Обзор»](https://contentmoderator.cognitive.microsoft.com/) (найден в поле **Id** на экране учетных данных инструмента «Обзор»).
- **Ocp-Apim-Подписка-Ключ**: Ваш ключ модератора контента. Вы можете найти это на вкладке **Настройки** [инструмента Обзор](https://contentmoderator.cognitive.microsoft.com).

### <a name="enter-a-review-definition"></a>Введите определение обзора

Отспособите поле **тела Запроса** для ввода запроса JSON со следующими полями:

- **Метаданные**: Пользовательские пары значений ключей, которые должны быть возвращены в конечную точку обратного вызова. Если ключ — это короткий код, который определен в [инструменте «Обзор»,](https://contentmoderator.cognitive.microsoft.com)он отображается как тег.
- **Содержимое**: В случае изображения и видео контента, это строка URL, указывающая на содержание. Для текстового содержимого это фактическая строка текста.
- **ContentId**: пользовательская строка идентификатора. эта строка передается в API и возвращается с помощью обратного вызова. Это полезно для увязки внутренних идентификаторов или метаданных с результатами задания модерации.
- **Обратный возврат:**(Необязательно) URL для получения информации обратного вызова, когда обзор завершен.

В корпусе запроса по умолчанию приведены примеры различных типов рецензий, которые можно создать:

```json
[Image]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Image",
    "Content": "<Content Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Text]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Text",
    "Content": "<Your Text Content>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Video]
[
  {
    "VideoFrames":[
      {
          "Id": "<Frame Id>",
          "Timestamp": "<Frame Timestamp",
          "FrameImage":"<Frame Image URL",
          "Metadata": [
            {
              "Key": "<Key>",
              "Value": "<Value"
            }
          ],
          "ReviewerResultTags": [
          ]
    ], 
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      },
      //For encrypted Videos
        {
          "Key": "protectedType",
          "Value": "AES or FairPlay or Widevine or Playready"
        },
        {
          "Key": "authenticationToken",
          "Value": "your viewtoken(In case of Video Indexer AES encryption type, this value is viewtoken from breakdown json)"
        },
      //For FairPlay encrypted type video include certificateUrl as well
        {
          "Key": "certificateUrl",
          "Value": "your certificate url"
        }
    ],
    "Type": "Video",
    "Content": "<Stream Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>",
    [Optional]
    "Timescale": "<Timescale of the video>
  }
]
```

### <a name="submit-your-request"></a>Отправка запроса
  
Выберите **Отправить**. Если операция успешно, **статус ответа,** `200 OK`и окно **содержимого ответа** отображает идентификатор для обзора. Скопируйте этот идентификатор, чтобы использовать на следующих шагах.

![Идентификатор проверки в поле "Response content" (Содержимое ответа) в консоли "Review - Create" (Проверка — создание)](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>Изучите новый обзор

В [инструменте «Обзор»](https://contentmoderator.cognitive.microsoft.com)выберите**видео текста**/**Video** **изображения**/ **(в** > зависимости от того, какой контент вы использовали). Содержимое, которое вы загрузили, должно отображаться, готовое к человеческому просмотру.

![Изображение футбольного мяча в инструменте проверки](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>Получение сведений о проверке

Чтобы получить подробную информацию о существующем обзоре, перейдите на ссылку [«Обзор » — получить](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) справочную страницу API и выбрать кнопку для вашего региона (региона, в котором вводится ключ).

![Выбор региона на странице "Workflow - Get" (Рабочий процесс — получение)](images/test-drive-region.png)

Введите параметры вызова REST, как в вышеуказанном разделе. Для этого шага **reviewId** — это уникальная строка ID, полученная при создании обзора.

![Получение результатов в консоли "Review - Create" (Проверка — создание)](images/test-drive-review-3.PNG)
  
Выберите **Отправить**. Если операция выполняется успешно, `200 OK`статус **"Ответ"** отображается в формате JSON, например: **Response content**

```json
{  
  "reviewId":"201712i46950138c61a4740b118a43cac33f434",
  "subTeam":"public",
  "status":"Complete",
  "reviewerResultTags":[  
    {  
      "key":"a",
      "value":"False"
    },
    {  
      "key":"r",
      "value":"True"
    },
    {  
      "key":"sc",
      "value":"True"
    }
  ],
  "createdBy":"<teamname>",
  "metadata":[  
    {  
      "key":"sc",
      "value":"true"
    }
  ],
  "type":"Image",
  "content":"https://reviewcontentprod.blob.core.windows.net/<teamname>/IMG_201712i46950138c61a4740b118a43cac33f434",
  "contentId":"0",
  "callbackEndpoint":"<callbackUrl>"
}
```

Обратите внимание на следующие поля в ответе:

- **состояние**
- **reviewerResultTags**: Это появляется, если какие-либо теги были вручную добавлены человеческой группой обзора (показано **созданноеПоле).**
- **метаданные**: Это показывает теги, которые были первоначально добавлены в обзор, прежде чем группа человеческого обзора внесла изменения.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы узнали, как создавать обзоры модерации содержимого с помощью REST API. Затем интегрируйте отзывы в сценарий сквозной модерации, такой как учебник по [модерации электронной коммерции.](./ecommerce-retail-catalog-moderation.md)