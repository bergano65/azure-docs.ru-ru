---
title: Краткое руководство. Получение ответа из базы знаний в QnA Maker с помощью cURL
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве описывается, как получить ответ из базы знаний c помощью cURL.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/16/2019
ms.author: diberry
ms.openlocfilehash: ad4b40d649d4e4cbc17d6aec5d8bc7308012b927
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60913750"
---
# <a name="quickstart-get-answer-from-knowledge-base-using-curl"></a>Краткое руководство. Получение ответа из базы знаний с помощью cURL

В этом кратком руководстве описывается, как получить ответ из базы знаний с помощью cURL.

## <a name="prerequisites"></a>Технические условия

* Последние [**cURL**](https://curl.haxx.se/).
* Необходимо иметь [службу QnA Maker](../How-To/set-up-qnamaker-service-azure.md) и [базу знаний с вопросами и ответами](../Tutorials/create-publish-query-in-portal.md).

## <a name="publish-to-get-endpoint"></a>Публикация для получения конечной точки

Когда вы будете готовы создать ответ на вопрос из вашей базы знаний, [опубликуйте](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) базу знаний.

## <a name="use-production-endpoint-with-curl"></a>Использование рабочей конечной точки с помощью cURL

После публикации базы знаний на странице **публикации** отображаются параметры запроса HTTP для создания ответа. Вкладка **CURL** отображает параметры, необходимые для создания ответа из командной строки, [CURL](https://www.getpostman.com).

[![Публикация результатов](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png)](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png#lightbox)

Для создания ответа с помощью CURL выполните следующие действия:

1. Скопируйте текст на вкладке CURL. 
1. Откройте командную строку или терминал и вставьте текст.
1. Измените вопрос, чтобы он имел отношение к вашей базы знаний. Будьте осторожны, чтобы не удалить содержащий JSON, сопровождающий вопрос.
1. Введите команду. 
1. Ответ включает необходимые сведения об ответе. 

    ```bash
    > curl -X POST https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/1111f8c-d01b-4698-a2de-85b0dbf3358c/generateAnswer -H "Authorization: EndpointKey 111841fb-c208-4a72-9412-03b6f3e55ca1" -H "Content-type: application/json" -d "{'question':'How do I programmatically update my Knowledge Base?'}"
    {
      "answers": [
        {
          "questions": [
            "How do I programmatically update my Knowledge Base?"
          ],
          "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600",
          "score": 100.0,
          "id": 18,
          "source": "Custom Editorial",
          "metadata": [
            {
              "name": "category",
              "value": "api"
            }
          ]
        }
      ]
    }
    ```

## <a name="use-staging-endpoint-with-curl"></a>Использование промежуточной конечной точки с помощью cURL

Если вы хотите получить ответ от промежуточной конечной точки, используйте `isTest` тела свойства.

```json
isTest:true
```

## <a name="next-steps"></a>Дальнейшие действия

На странице публикации также предоставляются сведения для [формирования ответа](get-answer-from-kb-using-postman.md) с помощью Postman. 

> [!div class="nextstepaction"]
> [Использование метаданных во время создания ответа](../How-to/metadata-generateanswer-usage.md)
