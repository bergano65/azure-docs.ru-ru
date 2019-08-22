---
title: Совместная работа с базой знаний — QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker позволяет нескольким пользователям совместно работать с базой знаний. Эта возможность предоставляется в рамках управления доступом на основе ролей в Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: d9c91d54fb357807682cd57f46b04454e4e2cfec
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876664"
---
# <a name="collaborate-on-your-knowledge-base"></a>Совместная работа с базой знаний

QnA Maker позволяет нескольким пользователям совместно работать с базой знаний. Эта возможность предоставляется в рамках [управления доступом на основе ролей](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) в Azure. 

Чтобы предоставить другому пользователю доступ к службе QnA Maker, сделайте следующее.

1. Войдите в портал Azure и перейдите к ресурсу QnA Maker.

    ![Список ресурсов QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. Перейдите на вкладку **Управление доступом (IAM)** .

    ![IAM в QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. Выберите **Добавить**.

    ![Добавление IAM в QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. Выберите роль **Владелец** или **Участник**. Вы не можете предоставить доступ только для чтения с помощью управления доступом на основе ролей. Роли владельца и участника имеют разрешения на доступ для чтения и записи к службе QnA Maker.

    ![Добавление роли IAM QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. Введите адрес электронной почты пользователя и нажмите кнопку " **сохранить**".

    ![Добавление адреса электронной почты для IAM в QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Когда пользователь, которому вы предоставили доступ к службе QnA Maker, записывается на [портал QnA Maker](https://qnamaker.ai) , он может видеть все базы знаний в этой службе.

Помните, что доступ к конкретной базе знаний в службе QnA Maker предоставить невозможно. Если требуется более детальное управление доступом, рекомендуем распределить базы знаний по разным службам QnA Maker.

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Тестирование базы знаний](./test-knowledge-base.md)
