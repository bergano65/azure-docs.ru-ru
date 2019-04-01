---
title: Создание проверки "умеренность" с помощью консоли, REST API Content Moderator
titlesuffix: Azure Cognitive Services
description: Используйте API проверки содержимого модератора Azure для создания изображения или текстовом обзоры для Модерация.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: beadbfc09526f738ba90252787b5b0910a2f7163
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58755368"
---
# <a name="create-human-reviews-rest"></a>Создание пользовательской проверки (REST)

[Обзоры](./review-api.md#reviews) хранения и отображения содержимого для человека модераторов для оценки. Когда пользователь завершает проверку, результаты отправляются в конечную точку заданный обратный вызов. В этом руководстве вы узнаете, как настроить обзоров с помощью REST API проверки через консоль API. Поняв структуру API-интерфейсы, легко можно перенести эти вызовы на любую платформу совместимые с REST.

## <a name="prerequisites"></a>Технические условия

- Войдите или создайте учетную запись на Content Moderator [средство проверки](https://contentmoderator.cognitive.microsoft.com/) сайта.

## <a name="create-a-review"></a>Создания и выполнения проверки

Для создания и выполнения проверки, перейдите к **[для просмотра – создать](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** API ссылку на страницу и нажмите кнопку для вашего ключа региона (его можно найти в URL-адрес конечной точки на **учетные данные** страницы из [средство проверки](https://contentmoderator.cognitive.microsoft.com/)). Это запускает консоль API, где можно легко создать и выполнить вызовы REST API.

![Для просмотра – Выбор региона Get](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Введите параметры вызова REST

Введите значения для **Имякоманды**, и **Ocp-Apim-Subscription-Key**:

- **teamName**: Идентификатор команды, созданный при настройке вашего [средство проверки](https://contentmoderator.cognitive.microsoft.com/) учетной записи (в **идентификатор** на экран ввода учетных данных средства проверки).
- **Ocp-Apim-Subscription-Key**: Ваш ключ Content Moderator. Его можно найти на **параметры** вкладке [средство проверки](https://contentmoderator.cognitive.microsoft.com).

### <a name="enter-a-review-definition"></a>Введите определение проверки

Изменить **текст запроса** поле для ввода запроса JSON со следующими полями:

- **Метаданные**. Пользовательские пары ключ значение возвращается в конечную точку обратного вызова. Если ключ является короткий код, который определен в [средство проверки](https://contentmoderator.cognitive.microsoft.com), он отображается как тег.
- **Содержимого**: В случае содержимого изображений и видео это строку URL-адреса, указывающие на содержимое. Для текстового содержимого это фактический текстовая строка.
- **ContentId**: Строка пользовательского идентификатора. эта строка передается в API и возвращается с помощью обратного вызова. Это полезно для связывания внутренние идентификаторы или метаданные с результатами задания "умеренность".
- **CallbackEndpoint**: (Необязательно) URL-адрес для получения сведения об обратном вызове, по завершении проверки.

Текст запроса по умолчанию показаны примеры различных типов проверок, которые можно создать:

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
  
Нажмите кнопку **Отправить**. Если операция прошла успешно, **состояние ответа** — `200 OK`и **содержимое ответа** поле отображается идентификатор для проверки. Скопируйте этот идентификатор, чтобы использовать на следующих шагах.

![Идентификатор проверки в поле "Response content" (Содержимое ответа) в консоли "Review - Create" (Проверка — создание)](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>Изучите новый отзыв

В [средство проверки](https://contentmoderator.cognitive.microsoft.com)выберите **проверки** > **изображение**/**текст** / **Видео** (в зависимости от того, какое содержимое использовался). Должно отобразиться содержимое, который вы загрузили, все готово для пользовательской проверки.

![Изображение футбольного мяча в инструменте проверки](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>Получить сведения о проверке

Чтобы получить сведения о существующей рецензии, перейдите в раздел [для просмотра – получить](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) API ссылку на страницу и нажмите кнопку "," для вашего региона (регион, в котором осуществляется ключа).

![Выбор региона на странице "Workflow - Get" (Рабочий процесс — получение)](images/test-drive-region.png)

Введите параметры вызова REST, как описано в разделе выше. Для выполнения этого шага **reviewId** является строкой уникального идентификатора, полученный при создании проверки.

![Получение результатов в консоли "Review - Create" (Проверка — создание)](images/test-drive-review-3.PNG)
  
Нажмите кнопку **Отправить**. Если операция прошла успешно, **состояние ответа** — `200 OK`и **содержимое ответа** поле отображает сведения о проверке в формате JSON, как в следующем:

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

Запишите следующие поля в ответе:

- **состояние**
- **reviewerResultTags**: Она отображается, если команда пользовательской проверки вручную добавлены теги (показано **createdBy** поля).
- **metadata**. Это показывает, теги, которые изначально были добавлены во время проверки, прежде чем изменения команда пользовательской проверки.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы узнали, как создание обзоров модерации содержимого, с помощью REST API. Затем интегрировать проверки в сценарии "умеренность" end-to-end, такими как [Модерация Электронная коммерция](./ecommerce-retail-catalog-moderation.md) руководства.