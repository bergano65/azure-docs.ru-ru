---
title: Обновление службы QnA Maker — QnA Maker
titleSuffix: Azure Cognitive Services
description: Предоставьте общий доступ или обновите службы QnA Maker, чтобы лучше управлять ресурсами.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/25/2019
ms.author: diberry
ms.openlocfilehash: df4aa2d6a3c4690fb1fc38b0f4f7d49afccdd657
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640488"
---
# <a name="share-or-upgrade-your-qna-maker-service"></a>Предоставление общего доступа или обновление службы QnA Maker
Предоставьте общий доступ или обновите службы QnA Maker, чтобы лучше управлять ресурсами. 

Вы можете обновить отдельные компоненты стека QnA Maker после их создания. См. [дополнительные сведения о зависимых компонентах и выборе номера SKU](https://aka.ms/qnamaker-docs-capacity).

## <a name="share-existing-services-with-qna-maker"></a>Совместное использование существующих служб с QnA Maker

QnA Maker создает несколько ресурсов Azure. Чтобы сократить затраты на управление и преимущества совместного использования затрат, используйте следующую таблицу, чтобы понять, что вы можете и не можете поделиться:

|Служба|Общий доступ|
|--|--|
|Cognitive Services|X|
|План обслуживания приложений|✔|
|Служба приложений|X|
|Application Insights|✔|
|Служба поиска|✔|

## <a name="upgrade-qna-maker-management-sku"></a>Обновление номера SKU для управления службой QnA Maker

Если вам необходимо расширить число вопросов и ответов в базе знаний за пределы текущего уровня, перейдите на ценовую категорию службы QnA Maker. 

Чтобы обновить номер SKU для управления службой QnA Maker, сделайте следующее:

1. Перейдите к ресурсу QnA Maker на портале Azure и выберите **Ценовая категория**.

    ![Ресурс QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

2. Определите соответствующий номер SKU и нажмите **Выбрать**.

    ![Цены на QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>Обновление службы приложений

 Если потребуется, чтобы база знаний обслуживала большее количество запросов из клиентского приложения, перейдите на ценовую категорию службы приложений.

Масштаб службы приложений можно [увеличить](https://docs.microsoft.com/azure/app-service/manage-scale-up) или уменьшить.

1. При необходимости перейдите к ресурсу службы приложений на портале Azure и выберите **Увеличить масштаб** или **Уменьшить масштаб**.

    ![Масштабирование службы приложения QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-azure-search-service"></a>Обновление службы Поиска Azure

Если вы планируете использовать несколько баз знаний, перейдите на ценовую категорию службы поиска Azure. 

Сейчас вы не можете обновить номер SKU Поиска Azure. Но можно создать новый ресурс Поиска Azure с требуемым номером SKU, восстановить туда данные, а затем привязать его к стеку QnA Maker.

1. Создайте ресурс Поиска Azure на портале Azure и выберите требуемый номер SKU.

    ![Ресурс Поиска Azure в QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

2. Восстановите индексы из исходного ресурса Поиска Azure на новый. См. [пример кода резервного копирования и восстановления](https://github.com/pchoudhari/QnAMakerBackupRestore).

3. После восстановления данных перейдите на новый ресурс поиска Azure, выберите **Ключи**, а затем запишите **имя** и **ключ администратора**.

    ![Ключи поиска Azure в QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

4. Чтобы связать новый ресурс поиска Azure со стеком QnA Maker, перейдите к службе приложений QnA Maker.

    ![Служба приложений QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

5. Выберите **Параметры приложения** и замените поля **AzureSearchName** и **AzureSearchAdminKey** из шага 3.

    ![Настройка службы приложений QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

6. Перезапустите службу приложений.

    ![Перезапуск службы приложений QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Use QnA Maker API](../Quickstarts/csharp.md) (Использование API службы QnA Maker)
