---
title: Сотрудничество на базе знаний - NA Maker
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75660755"
---
# <a name="collaborate-on-your-knowledge-base"></a>Совместная работа с базой знаний

Несколько человек могут сотрудничать со всеми базами знаний в одном и том же ресурсе nA Maker. Эта возможность предоставляется в рамках [управления доступом на основе ролей](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) в Azure.

Чтобы предоставить другому пользователю доступ к службе QnA Maker, сделайте следующее.

1. Войдите на портал Azure и перейдите на ресурс «NA Maker».

    ![Список ресурсов QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. Перейдите на вкладку **Управление доступом (IAM)**.

    ![IAM в QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. Нажмите кнопку **Добавить**.

    ![Добавление IAM в QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. Выберите роль **Владелец** или **Участник**. Вы не можете предоставить доступ только для чтения с помощью управления доступом на основе ролей. Функции владельца и автора имеют разрешения на чтение-запись в службу «Создатель» в течение чтения.

    ![Добавление роли IAM QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. Введите адрес электронной почты пользователя и нажмите **Сохранить**.

    ![Добавление адреса электронной почты для IAM в QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Когда человек, с которым вы поделились службой «NA Maker», входит в [портал «NA Maker»,](https://qnamaker.ai) они могут видеть все базы знаний в этой службе.

Помните, что доступ к конкретной базе знаний в службе QnA Maker предоставить невозможно. Если требуется более детальное управление доступом, рекомендуем распределить базы знаний по разным службам QnA Maker.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Тестирование базы знаний](./test-knowledge-base.md)
