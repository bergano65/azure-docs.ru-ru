---
title: Утверждение или отклонение запросов для ролей Azure AD в PIM-Azure Active Directory | Документация Майкрософт
description: Узнайте, как утверждать или отклонять запросы для ролей Azure AD в Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60a8d373a7e6edeaefd933e4f8ec8ee11e3c14ee
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804036"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-pim"></a>Утверждение или отклонение запросов для ролей Azure AD в PIM

С помощью Azure Active Directory (Azure AD) управление привилегированными пользователями (PIM) можно настроить роли, требующие утверждения для активации, и выбрать одного или нескольких пользователей или группы в качестве делегированных утверждающих. Делегированные утверждающие лица имеют 24 часа на утверждение запросов. Если запрос не был утвержден в течение 24 часов, пользователю, наделенному соответствующим правом, необходимо повторно отправить запрос. Продолжительность 24-часового периода для утверждения нельзя изменить.

Выполните действия, описанные в этой статье, чтобы утвердить или отклонить запросы для ролей Azure AD.

## <a name="view-pending-requests"></a>Просмотр ожидающих запросов

В качестве делегированного утверждающего лица вы получите уведомление по электронной почте, когда запрос роли Azure AD ожидает утверждения. Эти ожидающие утверждения запросы можно просмотреть в PIM.

1. Войдите на [портале Azure](https://portal.azure.com/).

1. Откройте страницу **Azure AD Privileged Identity Management**.

1. Выберите **Роли Azure AD**.

1. Выберите **Утверждение запросов**.

    ![Роли Azure AD — утверждение запросов](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

    Отобразится список запросов, ожидающих утверждения.

## <a name="approve-requests"></a>Утверждение запросов

1. Выберите запросы, которые требуется утвердить, а затем выберите **Утвердить**, чтобы открыть область "Утверждение выбранных запросов".

    ![Список утвержденных запросов с выделенным параметром утверждения](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. В области **Причина утверждения** введите причину.

    ![Утвердить панель выбранных запросов с причиной утверждения](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Нажмите кнопку **Утвердить**.

    Символ состояния будет обновлен с вашим разрешением.

    ![Утвердить панель выбранных запросов после нажатия кнопки "утвердить"](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>Отклонение запросов

1. Выберите запросы, которые требуется отклонить, а затем выберите **Запретить**, чтобы открыть область "Отклонение выбранных запросов".

    ![Список утвержденных запросов с выделенным параметром Deny](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. В области **Причина отклонения** введите причину.

    ![Отклонить выбранную панель запросов с причиной запрета](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Выберите **Отклонить**.

    Символ состояния будет обновлен с вашим отказом.

## <a name="next-steps"></a>Следующие шаги

- [Уведомления по электронной почте в PIM](pim-email-notifications.md)
- [Подтверждение или отклонение запросов для ролей ресурсов Azure в PIM](pim-resource-roles-approval-workflow.md)
