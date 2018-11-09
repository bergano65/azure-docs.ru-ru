---
title: Руководство по созданию бота QnA с помощью службы Azure Bot — QnA Maker
titleSuffix: Azure Cognitive Services
description: В этом руководстве приводятся пошаговые инструкции по созданию бота QnA с помощью службы Azure Bot версии 3 на портале Azure.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker`
ms.topic: article
ms.date: 10/25/2018
ms.author: tulasim
ms.openlocfilehash: 19c56cf05e307deca52808b0eeba65b8949ffc0b
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212749"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v3"></a>Руководство по созданию бота QnA с помощью службы Azure Bot версии 3

В этом руководстве приведены пошаговые инструкции по созданию бота QnA с помощью службы Azure Bot версии 3 на [портале Azure](https://portal.azure.com). Вам не придется писать код. Для подключения опубликованной базы знаний к боту достаточно лишь изменить параметры приложения бота. 

> [!Note] 
> Эта статья основана на версии 3 пакета SDK для ботов. Инструкции для версии 4 вы найдете [здесь](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs). 

**В этом руководстве рассмотрено, как выполнять следующие задачи.**

<!-- green checkmark -->
> [!div class="checklist"]
> * создание службы Azure Bot с помощью шаблона QnA Maker;
> * диалог с ботом для проверки правильности работы; 
> * подключение опубликованной базы знаний к боту;
> * тестирование бота с помощью вопроса.

Для работы с этой статьей можно использовать бесплатную [службу](../how-to/set-up-qnamaker-service-azure.md) QnA Maker.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется опубликованная база знаний. Если у вас ее нет, выполните инструкции в статье [Создание базы знаний](../How-To/create-knowledge-base.md), чтобы создать службу QnA Maker с вопросами и ответами.

## <a name="create-a-qna-bot"></a>Создание бота QnA

1. На портале Azure выберите **Создать ресурс**.

    ![создание службы бота](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. В поле поиска введите **Бот веб-приложения**.

    ![выбор службы бота](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. В **Службе Bot** введите необходимые сведения.

    - В поле **Имя приложения** укажите имя бота. При развертывании бота в облаке имя используется в качестве поддомена (например, mynotesbot.azurewebsites.net).
    - Укажите подписку, группу ресурсов, план службы приложений и расположение.

4. Чтобы использовать шаблоны версии 3, выберите для пакета SDK версию **SDK v3** (SDK версии 3) и язык **C#** или **Node.js**.

    ![Параметры пакета SDK для бота](../media/qnamaker-tutorials-create-bot/bot-v3.png)

5. Выберите в поле шаблона бота **Question and Answer** (Вопрос и ответ), а затем сохраните параметры шаблона, нажав кнопку **Выбрать**.

    ![выбор службы бота](../media/qnamaker-tutorials-create-bot/bot-v3-template.png)

6. Просмотрите параметры, а затем нажмите кнопку **Создать**. Это действие позволяет создать службу бота и развернуть ее в Azure.

    ![выбор службы бота](../media/qnamaker-tutorials-create-bot/bot-blade-settings-v3.png)

7. Убедитесь, что служба бота развернута.

    - Щелкните **Уведомления** (значок колокольчика, расположенный в верхней части портала Azure). Уведомление изменится с **Развертывание начато** на **Развертывание прошло успешно**.
    - После того как уведомление изменится на **Развертывание прошло успешно**, в этом уведомлении выберите **Go to resource** (Перейти к ресурсу).

## <a name="chat-with-the-bot"></a>Чат с ботом

При выборе **Перейти к ресурсу** выполняется переход к странице ресурса бота.

Выберите **Тестировать в веб-чате**, чтобы открыть панель веб-чата. Введите "hi" (привет) в окне веб-чата.

![веб-чат бота QnA](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat.PNG)

Бот выдаст ответ: "Задайте QnAKnowledgebaseId и QnASubscriptionKey в параметрах приложения. Этот ответ подтверждает, что бот QnA получил сообщение, однако с ним еще не связана база знаний QnA Maker. 

## <a name="connect-your-qna-maker-knowledge-base-to-the-bot"></a>Подключение базы знаний QnA Maker к боту

1. Откройте **параметры приложения** и задайте в полях **QnAKnowledgebaseId**, **QnAAuthKey** и **QnAEndpointHostName** значения для вашей базы знаний QnA Maker.

    ![параметры приложения](../media/qnamaker-tutorials-create-bot/application-settings.PNG)

1. Получите идентификатор базы знаний, URL-адрес узла и ключ конечной точки на вкладке параметров базы знаний в на портале QnA Maker.

    - Войдите в [QnA Maker](https://qnamaker.ai).
    - Перейдите к базе знаний.
    - Выберите вкладку **Параметры**.
    - **Опубликуйте** базу знаний, если она еще не опубликована.

    ![Значения QnA Maker](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

> [!NOTE]
> Если вы хотите подключить предварительную версию базы знаний к боту QnA, установите для параметра **Ocp-Apim-Subscription-Key** значение **QnAAuthKey**. Оставьте поле **QnAEndpointHostName** пустым.

## <a name="test-the-bot"></a>Тестирование бота

На портале Azure выберите **Test in Web Chat** (Тестировать в веб-чате), чтобы протестировать бот. 

![Бот QnA Maker](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat-response.PNG)

Бот QnA будет предоставлять ответы из базы знаний.

## <a name="clean-up-resources"></a>Очистка ресурсов

Когда вы завершите работу с ботом из этого руководства, удалите его на портале Azure. Бот содержит следующие службы:

* план службы приложений;
* служба "Поиск";
* служба Cognitive Services;
* Служба приложений.
* Он также может использовать службу Application Insights и хранилище для аналитических сведений (необязательно).

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Концепция базы знаний](../concepts/knowledge-base.md)

## <a name="see-also"></a>См. также

- [Управление базой знаний](https://qnamaker.ai)
- [Включение бота в разных каналах](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
