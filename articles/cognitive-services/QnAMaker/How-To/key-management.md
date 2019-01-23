---
title: Управление ресурсами и ключами — QnA Maker
titleSuffix: Azure Cognitive Services
description: 'Служба QnA Maker работает с двумя видами ключей: ключами подписки и ключами конечной точки.'
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 11/26/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 2b199e10b1a70bc44065d422d32acd3bea182721
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/15/2019
ms.locfileid: "54302057"
---
# <a name="how-to-manage-keys-in-qna-maker"></a>Управление ключами в QnA Maker

Служба QnA Maker работает с двумя видами ключей: **ключами подписки** и **ключами конечной точки**.

![Управление ключами](../media/qnamaker-how-to-key-management/key-management.png)

1. **Ключи подписки** используются для доступа к [API службы управления QnA Maker](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff). Эти API-интерфейсы позволяют редактировать базу знаний.  

2. **Ключи конечной точки** используются для доступа к конечной точке базы знаний, чтобы пользователь мог получить ответ на свой вопрос. Эта конечная точка обычно используется в коде бота или клиентского приложения, использующего службу QnA Maker.
 
## <a name="subscription-keys"></a>Ключи подписки
Можно просмотреть и сбросить ключи подписки на портале Azure, где был создан ресурс QnA Maker. 
1. Перейдите к ресурсу QnA Maker на портале Azure.

    ![Список ресурсов QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Перейдите в раздел **Ключи**.

    ![Ключ подписки](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>Ключи конечной точки

Ключами конечной точки можно управлять на [портале QnA Maker](https://qnamaker.ai).

1. Войдите на [портал QnA Maker](https://qnamaker.ai), перейдите в свой профиль и щелкните **Service settings** (Параметры службы).

    ![Ключ конечной точки](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Просмотрите или сбросьте свои ключи.

    ![Диспетчер ключей конечной точки](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Если вы подозреваете, что ключи были скомпрометированы, обновите их. Для этого может потребоваться внести соответствующие изменения в код клиентского приложения или бота.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Create a knowledge base in a different language](./language-knowledge-base.md) (Создание базы знаний на разных языках)
