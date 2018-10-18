---
title: Удаление группы с помощью Azure Active Directory | Документы Майкрософт
description: Сведения об удалении группы с помощью Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 014fe487d23a6c75e94ca2708ed15044bd6cf53b
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574337"
---
# <a name="how-to-delete-a-group-using-azure-active-directory"></a>Практическое руководство. Удаление группы с помощью Azure Active Directory
Удаление группы может потребоваться по разным причинам. Вот наиболее распространенные из них:

- Неправильно задан **тип группы**.

- По ошибке создана неправильная или повторяющаяся группа. 

- Группа больше не нужна.

## <a name="to-delete-a-group"></a>Удаление группы
1. Войдите на [портал Azure](https://portal.azure.com) с учетной записью глобального администратора каталога.

2. Выберите **Azure Active Directory**, а затем щелкните **Группы**.

3. На странице **Группы — Все группы** найдите и выберите группу, которую нужно удалить. В этом примере мы выберем группу **MDM policy — East**.

    ![Страница "Группы — Все группы" с выделенным именем группы](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. На странице **Обзор MDM policy — East** нажмите **Удалить**.

    Группа будет удалена из вашего клиента Azure Active Directory.

    ![Страница "Обзор MDM policy — East" с выделенной командой "Удалить"](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>Дополнительная информация

- Если вы по ошибке удалили группу, ее можно создать заново. Дополнительные сведения см. в статье [Как создать простую группу и добавить в нее участников](active-directory-groups-create-azure-portal.md).

- Если вы по ошибке удалили группу Office 365, ее можно восстановить. Дополнительные сведения см. в статье [Восстановление удаленной группы Office 365](../users-groups-roles/groups-restore-deleted.md).
