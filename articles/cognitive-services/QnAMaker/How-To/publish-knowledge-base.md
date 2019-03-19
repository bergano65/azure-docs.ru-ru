---
title: Публикация базы знаний
titleSuffix: QnA Maker API - Azure Cognitive Services
description: Публикации в базе знаний со службой API службы QnA Maker соответствует последнему шагу для предоставления знаний как конечную точку ответа на вопрос. При публикации базы знаний содержимое раздела вопросов и ответов базы знаний переносится из тестового указателя в производственный указатель в службе поиска Azure.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/11/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: c65654e7d6b2e66a07116ea9555ed316ace88912
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58091959"
---
# <a name="publish-a-knowledge-base-using-the-qna-maker-api-service-portal"></a>Публикация базы знаний с помощью портала службы API службы QnA Maker

Публикации в базе знаний со службой API службы QnA Maker соответствует последнему шагу для предоставления знаний как конечную точку ответа на вопрос. 

При публикации базы знаний ее содержимое раздела вопросов и ответов переносится из тестового индекса в рабочий индекс в службе "Поиск Azure".

![Публикация из тестового указателя в производственный](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу. 

## <a name="publish-a-knowledge-base"></a>Публикация базы знаний

1. После завершения изменений в базе знаний выберите команду **Опубликовать** на верхней панели навигации. Вы можете публиковать количество баз знаний для поиска Azure, не превышающее заданного количества. 

    ![Публикация базы знаний](../media/qnamaker-how-to-publish-kb/publish.png)

2. Еще раз выберите **Опубликовать**, чтобы просмотреть сведения о конечной точке, которые могут использоваться в вашем приложении или бот-коде.

    ![Успешно опубликованная база знаний](../media/qnamaker-how-to-publish-kb/publish-success.png)
    
## <a name="clean-up-resources"></a>Очистка ресурсов

Когда вы закончите работу с базой знаний, удалите ее на портале QnA Maker.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Анализ базы знаний](./get-analytics-knowledge-base.md)
