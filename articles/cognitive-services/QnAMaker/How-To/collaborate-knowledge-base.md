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
ms.date: 01/03/2020
ms.author: diberry
ms.openlocfilehash: b5adc7ebacde056a141ca3b361b9eb2ea7900a39
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "75660755"
---
# <a name="collaborate-on-your-knowledge-base"></a>Совместная работа с базой знаний

QnA Maker позволяет нескольким пользователям совместно работать над всеми базами знаний в одном и том же QnA Maker ресурсе. Эта возможность предоставляется в рамках [управления доступом на основе ролей](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) в Azure.

Чтобы предоставить другому пользователю доступ к службе QnA Maker, сделайте следующее.

1. Войдите в портал Azure и перейдите к ресурсу QnA Maker.

    ![Список ресурсов QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. Перейдите на вкладку **Управление доступом (IAM)**.

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
