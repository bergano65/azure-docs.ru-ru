---
title: Создание бота QnA с помощью службы Azure Bot — QnA Maker
titleSuffix: Azure Cognitive Services
description: Создайте чат-бот QnA из страницы публикации для существующей базы знаний. Этот бот-помощник использует v4 Bot Framework SDK. Не нужно писать код для создания программ-роботов, весь код предоставляется для вас.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/08/2019
ms.author: tulasim
ms.openlocfilehash: 85b0004288a06a834b61f6e3d50017d35d66ce86
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59263882"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v4"></a>Руководство по Создание программы-робота, QnA с помощью Azure Служба программ-роботов v4

Создайте чат-бот QnA из **публикации** страница для существующей базы знаний. Этот бот-помощник использует v4 Bot Framework SDK. Не нужно писать код для создания программ-роботов, весь код предоставляется для вас.

**В этом руководстве рассмотрено, как выполнять следующие задачи.**

<!-- green checkmark -->
> [!div class="checklist"]
> * Создайте службу программ-роботов Azure из существующей базы знаний
> * диалог с ботом для проверки правильности работы; 

## <a name="prerequisites"></a>Технические условия

Для работы с этим руководством вам потребуется опубликованная база знаний. Если нет, выполните действия, описанные в [Create и ответов из базы Знаний](create-publish-query-in-portal.md) руководству, чтобы создать базу знаний QnA Maker с вопросами и ответами.

<a name="create-a-knowledge-base-bot"></a>

## <a name="create-a-qna-bot"></a>Создание бота QnA

Создание программы-робота, как клиентское приложение в базе знаний. 

1. На портале QnA Maker, перейдите к **публикации** страницы и публикации в базе знаний. Выберите **создания программ-роботов**. 

    ![На портале QnA Maker перейдите на страницу публикации и опубликуйте базу знаний. Нажмите кнопку "Create Bot" (Создать бота).](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png)

    Откроется портал Azure с конфигурацией создания программ-роботов.

1.  Введите параметры для создания программ-роботов.

    |Параметр|Значение|Назначение|
    |--|--|--|
    |Имя бота|`my-tutorial-kb-bot`|Это имя ресурса Azure, программ-роботов.|
    |Подписка|См. в разделе назначения.|Выберите подписку, которая использовалась для создания ресурсов QnA Maker.|
    |Группа ресурсов|`my-tutorial-rg`|Группу ресурсов, используемую для всех связанных с программ-роботов Azure ресурсов.|
    |Расположение|`west us`|Расположение ресурсов Azure bot.|
    |Ценовой уровень|`F0`|Уровень "бесплатный" Служба Azure bot.|
    |Имя приложения.|`my-tutorial-kb-bot-app`|Это веб-приложения для поддержки только бота. Это будет имя приложения не как служба QnA Maker уже использует. Совместное использование QnA Maker веб-приложения с любой другой ресурс не поддерживается.|
    |Пакет SDK для языка|C#|Это базовый язык программирования, используемый bot framework SDK. В такой ситуации C# или Node.js.|
    |ключ проверки подлинности QnA;|**Не изменяйте**|Это значение заполняется автоматически.|
    |расположение или план службы приложений;|**Не изменяйте**|В этом учебнике расположение не имеет значения.|
    |Хранилище Azure|**Не изменяйте**|Диалог данные хранятся в таблицах хранилища Azure.|
    |Application Insights|**Не изменяйте**|Ведение журнала, отправляемых в Application Insights.|
    |Microsoft App ID (Идентификатор приложения Майкрософт)|**Не изменяйте**|Active directory пользователя и пароль не требуется.|

    ![Создание программ-роботов базы знаний с этими параметрами.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base.png)

    Подождите несколько минут, пока не уведомления процесса создания программ-роботов сообщает об успешном выполнении.

<a name="test-the-bot"></a>

## <a name="chat-with-the-bot"></a>Чат с ботом

1. На портале Azure откройте новый ресурс программ-роботов из уведомления. 

    ![На портале Azure откройте новый ресурс программ-роботов из уведомления.](../media/qnamaker-tutorials-create-bot/azure-portal-notifications.png)

1. Из **управления программ-роботов**выберите **тестирования в Интернете** и введите: `How large can my KB be?`. Бот будет вернуть: 


    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.`


    ![Тестирование новых программ-роботов базы знаний.](../media/qnamaker-tutorial-create-publish-query-in-portal/test-bot-in-web-chat-in-azure-portal.png)

    Дополнительные сведения о программ-роботов Azure, см. в разделе [используйте QnA Maker, чтобы ответить на вопросы](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs)

## <a name="related-to-qna-maker-bots"></a>Связанные с программами-роботами QnA Maker

* Бот QnA Maker, используемых в портале QnA Maker, доступен в виде [образец программ-роботов](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-support-bot).
    ![Значок программ-роботов справки QnA Maker — красный робота](../media/qnamaker-tutorials-create-bot/answer-bot-icon.PNG)
* [Здравоохранения программы-роботы](https://docs.microsoft.com/HealthBot/qna_model_howto) используйте QnA Maker в качестве одного из своих [языковые модели](https://docs.microsoft.com/HealthBot/qna_model_howto).

## <a name="clean-up-resources"></a>Очистка ресурсов

Когда вы завершите работу с ботом из этого руководства, удалите его на портале Azure. 

Если вы создали группу ресурсов для ресурсов программ-роботов, удалите группу ресурсов. 

Если вы не создавали группу ресурсов, необходимо найти ресурсы, связанные с бота. Проще всего выполнить поиск по имени, программ-роботов и программ-роботов приложение. Ресурсы программ-роботов:

* план службы приложений;
* служба "Поиск";
* служба Cognitive Services;
* Служба приложений.
* Он также может использовать службу Application Insights и хранилище для аналитических сведений (необязательно).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Концепция базы знаний](../concepts/knowledge-base.md)

## <a name="see-also"></a>См. также

- [Управление базой знаний](https://qnamaker.ai)
- [Включение бота в разных каналах](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
