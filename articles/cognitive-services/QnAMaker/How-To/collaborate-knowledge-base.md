---
title: Совместная работа с базой знаний — QnA Maker
description: QnA Maker позволяет нескольким пользователям совместно работать с базой знаний. Эта функция предоставляется с помощью управления доступом на основе ролей в Azure (Azure RBAC).
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 20eb58f346322cb78bff85af3d6a0d366090763d
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2020
ms.locfileid: "87446948"
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

    ![Список ресурсов QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.png)

1. Перейдите на вкладку **Управление доступом (IAM)**.

    ![IAM в QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.png)

1. Выберите **Добавить**.

    ![Добавление IAM в QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.png)

1. Выберите роль из следующего списка:

    |Роль|
    |--|
    |Владелец|
    |Участник|
    |Модуль чтения Cognitive Services QnA Maker|
    |Редактор Cognitive Services QnA Maker|
    |Пользователь служб Cognitive Services|

    :::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-add-role-iam.png" alt-text="QnA Maker Добавление роли IAM.":::

1. Введите адрес электронной почты пользователя и нажмите кнопку " **сохранить**".

    ![Добавление адреса электронной почты для IAM в QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.png)

### <a name="view-qna-maker-knowledge-bases"></a>Просмотр QnA Maker баз знаний

Когда пользователь, которому вы предоставили доступ к QnA Maker службе с журналами на [портале QnA Maker](https://qnamaker.ai), он может просматривать все базы знаний в этой службе в зависимости от их роли.

При выборе базы знаний ее текущая роль на этом QnA Makerном ресурсе отображается рядом с именем базы знаний.

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="Снимок экрана базы знаний в режиме редактирования с именем роли в круглых скобках рядом с именем базы знаний в левом верхнем углу веб-страницы.":::

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Тестирование базы знаний](./test-knowledge-base.md)

Дополнительные сведения о совместной работе:
* Управление доступом на основе ролей в [Azure](../../../active-directory/role-based-access-control-configure.md)
* QnA Maker [Основные понятия](../Concepts/role-based-access-control.md) управления доступом на основе ролей
