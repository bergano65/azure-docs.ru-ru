---
title: Совместная работа с базой знаний — QnA Maker
description: QnA Maker позволяет нескольким пользователям совместно работать с базой знаний. Эта возможность предоставляется в рамках управления доступом на основе ролей в Azure.
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 1a2908b4b65017f427682627ce5d83b186956a58
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650780"
---
# <a name="collaboration-with-authors-and-editors"></a>Совместная работа с авторами и редакторами

Совместная работа предоставляется на уровне ресурсов QnA Maker, что позволяет ограничить доступ участников совместной работы на основе роли участника совместной работы. Узнайте больше об [основных понятиях](../Concepts/role-based-access-control.md)проверки подлинности QnA Maker для совместной работы.

## <a name="add-role-based-access-rbac-to-your-qna-maker-resource"></a>Добавление доступа на основе ролей (RBAC) в ресурс QnA Maker

QnA Maker позволяет нескольким пользователям совместно работать над всеми базами знаний в одном и том же QnA Maker ресурсе. Эта возможность предоставляется в рамках [управления доступом на основе ролей](../../../active-directory/role-based-access-control-configure.md) в Azure.

## <a name="access-at-the-qna-maker-resource-level"></a>Доступ на уровне ресурсов QnA Maker

Вы не можете предоставить общий доступ к определенной базе знаний в службе QnA Maker. Если требуется более детализированное управление доступом, рассмотрите возможность распределения баз знаний по различным QnA Maker ресурсам, а затем добавьте роли для каждого ресурса.

## <a name="add-role-to-resource"></a>Добавить роль в ресурс

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>Добавление учетной записи пользователя в ресурс QnA Maker

В следующих шагах используется роль совместной работы, но любые [роли](../reference-role-based-access-control.md) можно добавить с помощью этих шагов.

1. Войдите на портал [Azure](https://portal.azure.com/) и перейдите к ресурсу QnA Maker.

    ![Список ресурсов QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. Перейдите на вкладку **Управление доступом (IAM)**.

    ![IAM в QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. Нажмите **Добавить**.

    ![Добавление IAM в QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. Выберите роль из следующего списка:

    |Роль|
    |--|
    |Владелец|
    |Участник|
    |Модуль чтения QnA Maker|
    |Редактор QnA Maker|
    |Пользователь служб Cognitive Services|

    ![Добавление роли IAM QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. Введите адрес электронной почты пользователя и нажмите кнопку " **сохранить**".

    ![Добавление адреса электронной почты для IAM в QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Когда пользователь, которому вы предоставили доступ к QnA Maker службе с журналами на [портале QnA Maker](https://qnamaker.ai), он может просматривать все базы знаний в этой службе в зависимости от их роли.

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Тестирование базы знаний](./test-knowledge-base.md)

Дополнительные сведения о совместной работе:
* Управление доступом на основе ролей в [Azure](../../../active-directory/role-based-access-control-configure.md)
* QnA Maker [Основные понятия](../Concepts/role-based-access-control.md) управления доступом на основе ролей
