---
title: Краткое руководство. Создание, обучение и публикация базы знаний QnA Maker
titleSuffix: Azure Cognitive Services
description: Базу знаний QnA Maker можно создать из собственного содержимого, например часто задаваемых вопросов или руководств по продукции. В этом примере база знаний QnA Maker создается на основе простой веб-страницы с часто задаваемыми вопросами о восстановлении ключа BitLocker.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: bc64196969b23f0aad77ff4d4495e4bb3e569c32
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888244"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>Краткое руководство. Создание, подготовка и публикация базы знаний QnA Maker

Базу знаний QnA Maker можно создать из собственного содержимого, например часто задаваемых вопросов или руководств по продукции. В этой статье описано, как создать базу знаний QnA Maker на основе простой веб-страницы с часто задаваемыми вопросами о восстановлении ключа BitLocker.

Добавьте персонажа для беседы, чтобы сделать базу знаний более привлекательной для пользователей.

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisite"></a>Предварительные требования

> [!div class="checklist"]
> * Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="create-a-new-qna-maker-knowledge-base"></a>Создание новой базы знаний QnA Maker

1. Войдите на портал [QnAMaker.ai](https://QnAMaker.ai), используя учетные данные Azure.

1. На портале QnA Maker выберите **Create a knowledge base** (Создать базу знаний).

1. На странице **Создание** выберите **Создать службу QnA**. Откроется [портал Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) для настройки службы QnA Maker в вашей подписке. 

1. На портале QnA Maker выберите службу QnA Maker в раскрывающихся списках. Если вы создали новую службу QnA Maker, не забудьте обновить страницу.

   ![Снимок экрана с выбранной базой данных и службой QnA Maker](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. Назовите свою базу знаний **Мой пример базы знаний QnA**.

1. Добавьте образец документа Word в качестве URL-адреса: 

    `https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`

1. Выберите `+ Add URL`.

1. Добавьте **_профессиональную_ беседу** в свою базу знаний. 

1. Выберите **Create your KB** (Создать базу знаний).

    Процесс извлечения, во время которого прочитывается документ и определяются вопросы и ответы, занимает несколько минут.

    Когда QnA Maker успешно создаст базы знаний, откроется страница **Knowledge base** (База знаний). На этой странице можно изменить содержимое базы знаний.

## <a name="add-a-new-question-and-answer-set"></a>Добавление нового набора вопросов и ответов

1. На портале QnA Maker на странице **Edit** (Изменение) выберите **Add QnA pair** (Добавить пару вопрос–ответ).
1. Добавьте следующий вопрос: 

    `How many Azure services are used by a knowledge base?`

1. Добавьте ответ, отформатированный с помощью _markdown_:

    ` * Azure QnA Maker service\n* Azure Cognitive Search\n* Azure web app\n* Azure app plan`

    ![ Добавьте вопрос в виде текста и ответа, отформатированного с помощью markdown.](../media/qnamaker-create-publish-knowledge-base/add-question-and-answer.png)

    Символ markdown (`*`) используется для пунктов маркированного списка. Символ `\n` используется для новой строки.  

    На странице **Edit** (Изменение) отображается markdown. При использовании панели **Test** (Проверка) в дальнейшем вы увидите, что markdown отображается правильно. 

## <a name="save-and-train"></a>Сохранение и обучение

В правом верхнем углу выберите **Save and train** (Сохранить и обучить), чтобы сохранить изменения и обучить модель QnA Maker. Изменения не вступят в силу, если они не сохранены.

## <a name="test-the-knowledge-base"></a>Проверка базы знаний

1. На портале QnA Maker в правом верхнем углу выберите **Test** (Тестировать), чтобы проверить, вступили ли в силу внесенные изменения. 
1. В текстовом поле введите пример пользовательского запроса. 

    `How many Azure services are used by a knowledge base?`  

    ![ В текстовом поле введите пример пользовательского запроса. ](../media/qnamaker-create-publish-knowledge-base/test-panel-in-qna-maker.png)

1. Выберите **Inspect** (Проверить), чтобы более подробно изучить ответ. Окно тестирования используется для проверки изменений, внесенных в базу знаний, перед публикацией своей базы знаний.

1. Выберите панель **Test** (Проверка) еще раз, чтобы закрыть панель **Test** (Проверка).

## <a name="publish-the-knowledge-base"></a>Публикация базы знаний

При публикации базы знаний содержимое этой базы знаний переносится из указателя `test` в указатель `prod` в службе поиска Azure.

![Снимок экрана с перемещением содержимого базы знаний](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. На портале QnA Maker выберите **Publish** (Опубликовать). Затем, чтобы подтвердить, выберите **Опубликовать** на странице.

    Служба QnA Maker успешно опубликована. Конечную точку можно использовать в коде приложения или бота.

    ![Снимок экрана с успешной публикацией](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>Создание бота

После публикации можно создать бота на странице **Publish** (Публикация): 

* Можно создать несколько ботов, которые будут работать с одной базой знаний, для разных регионов или тарифных планов. 
* Если нужен только один бот для базы знаний, перейдите по ссылке **View all your bots on the Azure portal** (Просмотреть всех ботов на портале Azure), чтобы просмотреть список текущих ботов. 

При внесении изменений в базу знаний и ее повторной публикации не требуется предпринимать какие-либо действия с ботом. Он уже настроен для работы с базой знаний и будет учитывать все ее последующие изменения. При каждой публикации базы знаний все боты, подключенные к ней, обновляются автоматически.

1. На портале QnA Maker, на странице **Publish** (Публикация) выберите **Create bot** (Создать бота). Эта кнопка отображается только после публикации базы знаний.

    ![Снимок экрана с созданием бота](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. В новой вкладке браузера откроется страница создания службы Azure Bot на портале Azure. Настройте службу Azure Bot. 
    
    * Создавая бота, не изменяйте приведенные ниже параметры на портале Azure. Их значения автоматически заполняются для базы знаний: 
        * ключ проверки подлинности QnA;
        * расположение и план службы приложений;
    * Бот и QnA Maker могут иметь общий план службы веб-приложений, но не могут совместно использовать одно веб-приложение. Это означает, что **имя приложения** для бота должно отличаться от имени приложения для службы QnA Maker. 

1. После создания бота откройте ресурс **Bot service** (Служба бота). 
1. В разделе **управление ботом** выберите **Test in Web Chat** (Тестировать в веб-чате).
1. В приглашении чата **Type your message** (Тип сообщения), введите:

    `Azure services?`

    Чат-бот ответит, используя ответ из базы знаний. 

    ![Введите запрос пользователя в тестовый веб-чат.](../media/qnamaker-create-publish-knowledge-base/test-web-chat.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Очистите ресурсы QnA Maker и платформы бота на портале Azure. 

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения

* [Формат markdown в ответах](../concepts/data-sources-supported.md)
* [Тестирование markdown](../concepts/data-sources-supported.md#testing-your-markdown)
* [Источники данных](../Concepts/data-sources-supported.md) QnA Maker. 
* [Параметры конфигурации ресурса бота](../tutorials/create-qna-bot.md).

> [!div class="nextstepaction"]
> [Добавление вопросов с использованием метаданных](add-question-metadata-portal.md)

