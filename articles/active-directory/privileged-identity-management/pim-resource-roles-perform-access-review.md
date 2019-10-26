---
title: Проверка доступа к ролям ресурсов Azure в управление привилегированными пользователями Azure Active Directory | Документация Майкрософт
description: Узнайте, как просматривать доступ к ролям ресурсов Azure в Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d5837bba537990f4a2b49a74a1266bd09aba0d5
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895600"
---
# <a name="review-access-to-azure-resource-roles-in-privileged-identity-management"></a>Проверка доступа к ролям ресурсов Azure в управление привилегированными пользователями

Проверки доступа управление привилегированными пользователями (PIM) могут помочь защитить доступ к привилегированным ролям в Azure Active Directory (Azure AD). В этой статье описаны действия по выполнению проверки назначений привилегированных ролей в проверке доступа Azure AD.

Если вы назначены административной роли, вам может потребоваться выполнить проверку доступа администратора, чтобы подтвердить потребность в роли. Запрос на подтверждение может содержать сообщение электронной почты, содержащее ссылку, или подтверждение в [портал Azure](https://portal.azure.com).

Если вы администратор привилегированных ролей и вас интересуют функции проверки доступа, дополнительные сведения см. в статье [Как запустить проверку доступа в управлении привилегированными пользователями Azure AD](pim-resource-roles-start-access-review.md).

## <a name="approve-or-deny-access"></a>Утверждение или отклонение доступа

Вы можете утвердить или отклонить доступ в зависимости от того, используется ли эта роль. Чтобы оставаться в этой роли, выберите **Утвердить**, а когда доступ вам больше не требуется — **Запретить**. Состояние изменится только после того, как проверяющий применяет результаты.

Выполните следующие действия, чтобы найти проверку доступа и завершить ее.

1. Войдите на [портале Azure](https://portal.azure.com/).
1. Выберите **Azure Active Directory** и откройте **Управление привилегированными пользователями**.
1. Выберите **проверить доступ**.

   ![Снимок экрана управление привилегированными пользователями приложения с выбранной колонкой "Проверка доступа"](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

1. Выберите проверку, которую необходимо завершить.
1. Выберите **утвердить** или **отклонить**. В **поле указать причину**введите Деловое обоснование для решения при необходимости.

   ![Снимок экрана страницы просмотра подробностей](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Выполните проверку доступа к моим ролям Azure AD в управление привилегированными пользователями](pim-how-to-perform-security-review.md)
