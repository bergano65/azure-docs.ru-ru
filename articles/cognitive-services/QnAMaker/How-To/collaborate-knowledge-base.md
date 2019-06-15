---
title: Совместная работа с базой знаний — Qna Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker позволяет нескольким пользователям совместно работать с базой знаний. Эта возможность предоставляется в рамках управления доступом на основе ролей в Azure.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: tulasim
ms.openlocfilehash: ca754f197a46fc41b6f1b432611a2177ec0afafa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61374860"
---
# <a name="collaborate-on-your-knowledge-base"></a>Совместная работа с базой знаний

QnA Maker позволяет нескольким пользователям совместно работать с базой знаний. Эта возможность предоставляется в рамках [управления доступом на основе ролей](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) в Azure. 

Чтобы предоставить другому пользователю доступ к службе QnA Maker, сделайте следующее.

1. Войдите на портал Azure и перейдите к ресурсу QnA Maker.

    ![Список ресурсов QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

2. Перейдите на вкладку **Управление доступом (IAM)** .

    ![IAM в QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

3. Выберите **Добавить**.

    ![Добавление IAM в QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

4. Выберите роль **Владелец** или **Участник**. Вы не можете предоставить доступ только для чтения с помощью управления доступом на основе ролей. Роль владельца и участника имеет право на чтение и запись для службы QnA Maker.

    ![Добавление роли IAM QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

5. Введите адрес электронной почты пользователя, которому следует предоставить доступ, и нажмите кнопку "Сохранить".

    ![Добавление адреса электронной почты для IAM в QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Теперь, когда пользователь, которому предоставлен доступ к службе QnA Maker, выполнит вход на [портал QnA Maker](https://qnamaker.ai), он сможет просматривать все базы знаний в этой службе.

Помните, что доступ к конкретной базе знаний в службе QnA Maker предоставить невозможно. Если требуется более детальное управление доступом, рекомендуем распределить базы знаний по разным службам QnA Maker.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Тестирование базы знаний](./test-knowledge-base.md)
