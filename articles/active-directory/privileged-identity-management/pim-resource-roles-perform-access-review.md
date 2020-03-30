---
title: Просмотр доступа к функциям ресурсов Azure в PIM - Azure AD Документы Майкрософт
description: Узнайте, как просмотреть доступ к функциям ресурсов Azure в управлении привилегированными удостоверениями данных Azure AD (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ddb4d0ff1339f1427c5041528cdbe464a345b37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847002"
---
# <a name="review-access-to-azure-resource-roles-in-privileged-identity-management"></a>Просмотр доступа к функциям ресурсов Azure в привилегированном управлении идентификацией

Обзоры доступа Privileged Identity Management (PIM) могут помочь обеспечить доступ к привилегированным ролям в Active Directory Azure (Azure AD). В этой статье будут рассмотрены ваши привилегированные назначения ролей в обзоре доступа Azure AD.

Если вам назначена административная роль, администратор может потребовать сяре, чтобы подтвердить необходимость в роли. Запрос на подтверждение может прийти по электронной почте, которая включает ссылку, или вы можете подтвердить на [портале Azure](https://portal.azure.com).

Если вы администратор привилегированных ролей и вас интересуют функции проверки доступа, дополнительные сведения см. в статье [Как запустить проверку доступа в управлении привилегированными пользователями Azure AD](pim-resource-roles-start-access-review.md).

## <a name="approve-or-deny-access"></a>Утверждение или отклонение доступа

Вы можете одобрить или отказать в доступе в зависимости от того, используете ли вы эту роль или нет. Чтобы оставаться в этой роли, выберите **Утвердить**, а когда доступ вам больше не требуется — **Запретить**. Ваш статус меняется только после того, как рецензент применяет результаты.

Выполните следующие действия, чтобы найти проверку доступа и завершить ее.

1. Войдите на [портал Azure](https://portal.azure.com/).
1. Выберите **Активный каталог Azure** и откройте **привилегированное управление идентификацией.**
1. Выберите **доступ к обзору**.

   ![Скриншот приложения Privileged Identity Management с выбранным лезвием доступа Review](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

1. Выберите проверку, которую необходимо завершить.
1. Выберите **Утвердить** или **отказать**. В **поле "Обеспечьте причину"** введите бизнес-обоснование своего решения, если это необходимо.

   ![Снимок экрана страницы просмотра подробностей](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Прополнить обзор доступа к моим ролям Azure AD в Привилегированном управлении идентификацией](pim-how-to-perform-security-review.md)
