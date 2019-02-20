---
title: Подтверждение или отклонение запросов на роли каталога Azure AD в PIM | Документация Майкрософт
description: Узнайте, как подтвердить или отклонить запросы на роли каталога Azure AD в Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 02/08/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68b0dba0280ce7875e797634a5dc2254cb889ad7
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56244987"
---
# <a name="approve-or-deny-requests-for-azure-ad-directory-roles-in-pim"></a>Подтверждение или отклонение запросов на роли каталога Azure AD в PIM

С помощью Azure Active Directory Privileged Identity Management (PIM) можно настроить роли, чтобы требовать утверждения при активации, а также выбрать одного или нескольких пользователей или групп в качестве делегированных утверждающих лиц. Делегированные утверждающие лица имеют 24 часа на утверждение запросов. Если запрос не был утвержден в течение 24 часов, пользователю, наделенному соответствующим правом, необходимо повторно отправить запрос. Продолжительность 24-часового периода для утверждения нельзя изменить.

Выполните действия, описанные в этой статье, чтобы утверждать или отклонять запросы для ролей каталога Azure AD.

## <a name="view-pending-requests"></a>Просмотр ожидающих запросов

При наличии запроса роли каталога Azure AD, ожидающего вашего утверждения, вы, как делегированное утверждающее лицо, получите уведомление по электронной почте. Эти ожидающие утверждения запросы можно просмотреть в PIM.

1. Войдите на [портале Azure](https://portal.azure.com/).

1. Откройте страницу **Azure AD Privileged Identity Management**.

1. Щелкните **Роли каталога Azure AD**.

1. Выберите **Утверждение запросов**.

    ![Роли каталога Azure AD PIM. Роли](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

    Отобразится список запросов, ожидающих утверждения.

## <a name="approve-requests"></a>Утверждение запросов

1. Выберите запросы, которые требуется утвердить, а затем выберите **Утвердить**, чтобы открыть область "Утверждение выбранных запросов".

    ![Список утвержденных запросов PIM](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. В области **Причина утверждения** введите причину.

    ![Утверждение выбранных запросов PIM](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Нажмите кнопку **Утвердить**.

    Символ состояния будет обновлен с вашим разрешением.

    ![Утверждение выбранных запросов PIM](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>Отклонение запросов

1. Выберите запросы, которые требуется отклонить, а затем выберите **Запретить**, чтобы открыть область "Отклонение выбранных запросов".

    ![Список утвержденных запросов PIM](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. В области **Причина отклонения** введите причину.

    ![Отклонение выбранных запросов PIM](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Выберите **Отклонить**.

    Символ состояния будет обновлен с вашим отказом.

## <a name="next-steps"></a>Дополнительная информация

- [Уведомления по электронной почте в PIM](pim-email-notifications.md)
- [Подтверждение или отклонение запросов для ролей ресурсов Azure в PIM](pim-resource-roles-approval-workflow.md)
