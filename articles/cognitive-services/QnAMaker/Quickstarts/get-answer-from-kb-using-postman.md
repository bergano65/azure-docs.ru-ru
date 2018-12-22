---
title: Краткое руководство. Получение ответа из базы знаний в QnA Maker с помощью Postman
titlesuffix: Azure Cognitive Services
description: В этом кратком руководстве описывается, как получить ответ из базы знаний c помощью Postman.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 12/11/2018
ms.author: diberry
ms.openlocfilehash: 476e982bdddd41c1daf06c3a673d964ce2a85f98
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53270898"
---
# <a name="quickstart-get-an-answer-from-knowledge-base-using-postman"></a>Краткое руководство. Получение ответа из базы знаний с помощью Postman

В этом кратком руководстве описывается, как получить ответ из базы знаний с помощью Postman.

## <a name="prerequisites"></a>Предварительные требования

* Последняя версия [**Postman**](https://www.getpostman.com/).
* Необходимо иметь [службу QnA Maker](../How-To/set-up-qnamaker-service-azure.md) и [базу знаний с вопросами и ответами](../Tutorials/create-publish-query-in-portal.md). 

## <a name="publish-to-get-endpoint"></a>Публикация для получения конечной точки

Когда вы будете готовы создать ответ на вопрос из вашей базы знаний, [опубликуйте](../How-to/publish-knowledge-base.md) базу знаний.

## <a name="use-production-endpoint-with-postman"></a>Использование рабочей конечной точки с помощью Postman

После публикации базы знаний на странице **публикации** отображаются параметры запроса HTTP для создания ответа. В представлении по умолчанию отображаются параметры, необходимые для создания ответа от [Postman](https://www.getpostman.com).

[![Публикация результатов](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png)](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png#lightbox)

Для создания ответа с помощью Postman выполните следующие действия:

1. Откройте Postman. 
1. Выберите стандартный блок для создания базового запроса.
1. Укажите в качестве **имени запроса** `Generate QnA Maker answer`, а в качестве **коллекции** — `Generate QnA Maker answers`.
1. В рабочей области выберите метод HTTP **POST**.
1. Объедините значения HOST и POST, чтобы создать полный URL-адрес, и укажите этот адрес. 

    [![Указание метода Post и полного URL-адреса в Postman](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png#lightbox)

1. Выберите вкладку **Headers** (Заголовки) под строкой URL-адреса, а затем выберите **Bulk Edit** (Массовое изменение). 
1. Скопируйте заголовки в текстовую область.

    [![Указание заголовков в Postman](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png#lightbox)

1. Выберите вкладку **Body** (Текст).
1. Выберите **необработанный** формат и введите представление вопроса в формате JSON.

    [![Указание значения JSON текста в Postman](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png#lightbox)

1. Нажмите кнопку **Send** (Отправить).
1. Будет возвращен ответ, а также другие сведения, которые могут быть важны для клиентского приложения. 

    [![Указание значения JSON текста в Postman](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png)](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png#lightbox)

## <a name="use-staging-endpoint-with-curl"></a>Использование промежуточной конечной точки с помощью cURL

Если вы хотите получить ответ от промежуточной конечной точки, используйте логический параметр строки запроса `isTest` со значением `true`.

`isTest=true`

## <a name="next-steps"></a>Дополнительная информация

На странице публикации также предоставляются сведения для [формирования ответа](get-answer-from-kb-using-curl.md) с помощью cURL. 

> [!div class="nextstepaction"]
> [Использование метаданных во время создания ответа](../How-to/metadata-generateanswer-usage.md)