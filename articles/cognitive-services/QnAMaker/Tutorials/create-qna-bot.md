---
title: Создание бота QnA с помощью службы Azure Bot — QnA Maker
titleSuffix: Azure Cognitive Services
description: Создайте чат-бот QnA на странице публикации для существующей базы знаний. Этот бот использует пакет SDK Bot Framework версии 4. Нет необходимости писать код для сборки бота, весь код уже доступен для вас.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: diberry
ms.openlocfilehash: 4bb987a5a091871bec2c0cc8cec6d9ab804bb244
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697993"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v4"></a>Руководство по Создание бота QnA с помощью службы Azure Bot версии 4

Создайте чат-бот QnA на странице **публикации** для существующей базы знаний. Этот бот использует пакет SDK Bot Framework версии 4. Нет необходимости писать код для сборки бота, весь код уже доступен для вас.

**В этом руководстве рассмотрено, как выполнять следующие задачи.**

<!-- green checkmark -->
> [!div class="checklist"]
> * Создайте службу Azure Bot из существующей базы знаний
> * диалог с ботом для проверки правильности работы; 

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется опубликованная база знаний. Если у вас ее нет, выполните инструкции в статье [Руководство по созданию базы знаний на портале QnA Maker](create-publish-query-in-portal.md), чтобы создать базу знаний QnA Maker с вопросами и ответами.

<a name="create-a-knowledge-base-bot"></a>

## <a name="create-a-qna-bot"></a>Создание бота QnA

Создайте бот в качестве клиентского приложения для базы знаний. 

1. На портале QnA Maker перейдите на страницу **Публикация** и опубликуйте базу знаний. Нажмите кнопку **Создать бот**. 

    ![На портале QnA Maker перейдите на страницу публикации и опубликуйте базу знаний. Нажмите кнопку "Create Bot" (Создать бота).](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png)

    Портал Azure открывается на странице конфигурации создания ботов.

1.  Чтобы создать бот, введите параметры:

    |Параметр|Значение|Назначение|
    |--|--|--|
    |Имя бота|`my-tutorial-kb-bot`|Это — имя ресурса Azure для бота.|
    |Subscription|Просмотрите назначение.|Выберите ту же подписку, которую вы использовали для создания ресурсов QnA Maker.|
    |Resource group|`my-tutorial-rg`|Группа ресурсов, используемая для всех ресурсов Azure, связанных с ботами.|
    |Location|`west us`|Расположение ресурса Azure бота.|
    |Ценовая категория|`F0`|Уровень "Бесплатный" для службы ботов Azure.|
    |Имя приложения.|`my-tutorial-kb-bot-app`|Это — веб-приложение для поддержки только вашего бота. Оно не должно совпадать с именем приложения, которое уже используется службой QnA Maker. Общий доступ к веб-приложению QnA Maker ни с каким другим ресурсом не поддерживается.|
    |Язык пакета SDK|C#|Это — основной язык программирования, используемый в пакете SDK Bot Framework. Возможны следующие варианты: [C#](https://github.com/Microsoft/botbuilder-dotnet) или [Node.js](https://github.com/Microsoft/botbuilder-js).|
    |ключ проверки подлинности QnA;|**Не изменять**|Это значение заполняется вами.|
    |расположение или план службы приложений;|**Не изменять**|В этом руководстве расположение значения не имеет.|
    |Хранилище Azure|**Не изменять**|Данные общения хранятся в таблице службы хранилища Azure.|
    |Application Insights|**Не изменять**|Данные журнала отправляются в Application Insights.|
    |Microsoft App ID (Идентификатор приложения Майкрософт)|**Не изменять**|Обязательно нужны имя пользователя и пароль Active Directory.|

    ![Создайте бот базы знаний с помощью этих параметров.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base.png)

    Подождите несколько минут, пока не получите уведомление об успешном завершении создания бота.

<a name="test-the-bot"></a>

## <a name="chat-with-the-bot"></a>Чат с ботом

1. На портале Azure откройте новый ресурс бота из уведомления. 

    ![На портале Azure откройте новый ресурс бота из уведомления.](../media/qnamaker-tutorials-create-bot/azure-portal-notifications.png)

1. В разделе **Управление ботом** выберите **Test in Web Chat** (Тестировать в веб-чате) и введите: `How large can my KB be?`. Ответ бота: 


    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.`


    ![Проверка нового бота базы знаний.](../media/qnamaker-tutorial-create-publish-query-in-portal/test-bot-in-web-chat-in-azure-portal.png)

    Подробности о ботах Azure см. в статье [Использование QnA Maker для получения ответов на вопросы](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs)

## <a name="clean-up-resources"></a>Очистка ресурсов

Когда вы завершите работу с ботом из этого руководства, удалите его на портале Azure. 

Если вы создали новую группу ресурсов для ресурсов бота, удалите ее. 

Если вы не создали новую группу ресурсов, необходимо найти ресурсы, связанные с ботом. Самый простой способ — поиск по названию и приложению бота. К ресурсам бота относятся следующие компоненты:

* план службы приложений;
* служба "Поиск";
* служба Cognitive Services;
* Служба приложений.
* Он также может использовать службу Application Insights и хранилище для аналитических сведений (необязательно).


## <a name="related-to-qna-maker-bots"></a>Связанные с ботами QnA Maker

* Бот раздела справки QnA Maker, используемый на портале QnA Maker, доступен в виде [примера бота](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-support).
    ![Значок бота справки QnA Maker — красный робот](../media/qnamaker-tutorials-create-bot/answer-bot-icon.PNG)
* [Боты оценки работоспособности](https://docs.microsoft.com/HealthBot/qna_model_howto) используют QnA Maker в качестве одной из [языковых моделей](https://docs.microsoft.com/HealthBot/qna_model_howto).


[!INCLUDE [Bot Information](../../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Концепция базы знаний](../concepts/knowledge-base.md)

## <a name="see-also"></a>См. также

- [Управление базой знаний](https://qnamaker.ai)
- [Включение бота в разных каналах](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
