---
title: Обновление службы QnA Maker — QnA Maker
titleSuffix: Azure Cognitive Services
description: Вы можете обновить отдельные компоненты стека QnA Maker после их создания.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/24/2019
ms.author: tulasim
ms.openlocfilehash: e37a903112f30917ff5051305bb2144981de1c9d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55883278"
---
# <a name="upgrade-your-qna-maker-service"></a>Обновление службы QnA Maker
Вы можете обновить отдельные компоненты стека QnA Maker после их создания. См. [дополнительные сведения о зависимых компонентах и выборе номера SKU](https://aka.ms/qnamaker-docs-capacity).

## <a name="upgrade-qna-maker-management-sku"></a>Обновление номера SKU для управления службой QnA Maker

Если вам необходимо расширить число вопросов и ответов в базе знаний за пределы текущего уровня, перейдите на ценовую категорию службы QnA Maker. 

Чтобы обновить номер SKU для управления службой QnA Maker, сделайте следующее:

1. Перейдите к ресурсу QnA Maker на портале Azure и выберите **Ценовая категория**.

    ![Ресурс QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

2. Определите соответствующий номер SKU и нажмите **Выбрать**.

    ![Цены на QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>Обновление службы приложений

 Если потребуется, чтобы база знаний обслуживала большее количество запросов из клиентского приложения, перейдите на ценовую категорию службы приложений.

Масштаб службы приложений можно [увеличить](https://docs.microsoft.com/azure/app-service/web-sites-scale) или уменьшить.

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

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Use QnA Maker API](../Quickstarts/csharp.md) (Использование API службы QnA Maker)
