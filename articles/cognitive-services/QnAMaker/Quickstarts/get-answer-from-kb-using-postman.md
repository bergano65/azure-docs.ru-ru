---
title: Краткое руководство. Получение ответа из базы знаний в QnA Maker с помощью Postman
titlesuffix: Azure Cognitive Services
description: В этом кратком руководстве описывается, как получить ответ из базы знаний c помощью Postman.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 7083285ac81aa8eafee8de49175e40934e5d05b4
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68253807"
---
# <a name="quickstart-get-an-answer-from-knowledge-base-using-postman"></a>Краткое руководство. Получение ответа из базы знаний с помощью Postman

В этом кратком руководстве описывается, как получить ответ из базы знаний с помощью Postman.

## <a name="prerequisites"></a>Предварительные требования

* Последняя версия [**Postman**](https://www.getpostman.com/).
* Необходимо иметь [службу QnA Maker](../How-To/set-up-qnamaker-service-azure.md) и [базу знаний с вопросами и ответами](../Tutorials/create-publish-query-in-portal.md). 

## <a name="publish-to-get-endpoint"></a>Публикация для получения конечной точки

Когда вы будете готовы создать ответ на вопрос из вашей базы знаний, [опубликуйте](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) базу знаний.

## <a name="use-production-endpoint-with-postman"></a>Использование рабочей конечной точки с помощью Postman

После публикации базы знаний на странице **публикации** отображаются параметры запроса HTTP для создания ответа. В представлении по умолчанию отображаются параметры, необходимые для создания ответа от [Postman](https://www.getpostman.com).

Желтые цифры на следующем рисунке обозначают пары "имя – значение", которые нужно использовать на следующих этапах.

[![Публикация результатов](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png)](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png#lightbox)

Для создания ответа с помощью Postman выполните следующие действия:

1. Откройте Postman. Если вам необходимо выбрать стандартный блок, выберите **Базовый запрос**. Укажите в качестве **имени запроса** `Generate QnA Maker answer`, а в качестве **коллекции** — `Generate QnA Maker answers`. Нажмите кнопку **Отмена**, чтобы не сохранять коллекцию.
1. В рабочей области выберите метод HTTP **POST**.

    [![Метод POST назначен в Postman](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png)](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png#lightbox)

1. В строке URL-адреса объедините значения HOST (#2 на изображении) и POST (#1 на изображении), чтобы создать полный URL-адрес. Пример URL-адреса выглядит так: 

    `https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/e1115f8c-d01b-4698-a2ed-85b0dbf3348c/generateAnswer`

    [![Введение полного URL-адреса в Postman](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png#lightbox)

1. Выберите вкладку **Headers** (Заголовки) под строкой URL-адреса, а затем выберите **Bulk Edit** (Массовое изменение). 

1. Скопируйте заголовки (#3 и #4 из изображения) в текстовую область.

    [![Указание заголовков в Postman](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png#lightbox)

1. Выберите вкладку **Body** (Текст).
1. Выберите формат **необработанных данных** и введите JSON (#5 из изображения), что представляет вопрос.

    `{"question":"How do I programmatically update my Knowledge Base?"}`

    [![Указание значения JSON текста в Postman](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png#lightbox)

1. Нажмите кнопку **Send** (Отправить).
1. Будет возвращен ответ, а также другие сведения, которые могут быть важны для клиентского приложения. 

    [![Указание значения JSON текста в Postman](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png)](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png#lightbox)

## <a name="use-staging-endpoint"></a>Использование промежуточной конечной точки

Если вы хотите получить ответ от промежуточной конечной точки, добавьте к URL-адресу свойство текста `isTest`.

## <a name="next-steps"></a>Дополнительная информация

На странице публикации также предоставляются сведения для [формирования ответа](get-answer-from-kb-using-curl.md) с помощью cURL. 

> [!div class="nextstepaction"]
> [Использование метаданных во время создания ответа](../How-to/metadata-generateanswer-usage.md)
