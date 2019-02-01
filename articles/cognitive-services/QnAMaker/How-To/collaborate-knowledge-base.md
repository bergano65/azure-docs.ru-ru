---
title: Совместная работа с базой знаний — Qna Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker позволяет нескольким пользователям совместно работать с базой знаний. Эта возможность предоставляется в рамках управления доступом на основе ролей в Azure.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: tulasim
ms.openlocfilehash: c7b970d9d906e9a703e396d6c9358d7dde733250
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55220065"
---
# <a name="collaborate-on-your-knowledge-base"></a>Совместная работа с базой знаний

QnA Maker позволяет нескольким пользователям совместно работать с базой знаний. Эта возможность предоставляется в рамках [управления доступом на основе ролей](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) в Azure. 

Чтобы предоставить другому пользователю доступ к службе QnA Maker, сделайте следующее.

1. Войдите на портал Azure и перейдите к ресурсу QnA Maker.

    ![Список ресурсов QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

2. Перейдите на вкладку **Управление доступом (IAM)**.

    ![IAM в QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

3. Выберите **Добавить**.

    ![Добавление IAM в QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

4. Выберите роль **Владелец** или **Участник**. Вы не можете предоставить доступ только для чтения с помощью управления доступом на основе ролей. Роль владельца и участника имеет право на чтение и запись для службы QnA Maker.

    ![Добавление роли IAM QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

5. Введите адрес электронной почты пользователя, которому следует предоставить доступ, и нажмите кнопку "Сохранить".

    ![Добавление адреса электронной почты для IAM в QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Теперь, когда пользователь, которому предоставлен доступ к службе QnA Maker, выполнит вход на [портал QnA Maker](https://qnamaker.ai), он сможет просматривать все базы знаний в этой службе.

Помните, что доступ к конкретной базе знаний в службе QnA Maker предоставить невозможно. Если требуется более детальное управление доступом, рекомендуем распределить базы знаний по разным службам QnA Maker.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Тестирование базы знаний](./test-knowledge-base.md)
