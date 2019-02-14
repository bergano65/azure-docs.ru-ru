---
title: Публикация базы знаний
titleSuffix: QnA Maker - Azure Cognitive Services
description: Публикация базы знаний — это последний шаг в предоставлении базы знаний в качестве конечной точки ответа на вопрос. При публикации базы знаний содержимое раздела вопросов и ответов базы знаний переносится из тестового указателя в производственный указатель в службе поиска Azure.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 12/11/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 585e5f8f955a405b08bef42588ab5caff71c7bba
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55865919"
---
# <a name="publish-a-knowledge-base-using-the-qna-maker-portal"></a>Публикация базы знаний с помощью портала QnA Maker

Публикация базы знаний — это последний шаг в предоставлении базы знаний в качестве конечной точки ответа на вопрос для клиентского приложения. 

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

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Анализ базы знаний](./get-analytics-knowledge-base.md)
