---
title: Утверждение или отклонение запросов для ролей Azure AD в PIM в Azure AD | Документация Майкрософт
description: Узнайте, как утверждать или отклонять запросы для ролей Azure AD в Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f663f7daad19e77dcc1cc95a6a324e881d92b28
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847134"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Утверждение или отклонение запросов ролей Azure AD в управление привилегированными пользователями

С помощью Azure Active Directory (Azure AD) управление привилегированными пользователями (PIM) можно настроить роли, требующие утверждения для активации, и выбрать одного или нескольких пользователей или группы в качестве делегированных утверждающих. Делегированные утверждающие лица имеют 24 часа на утверждение запросов. Если запрос не был утвержден в течение 24 часов, пользователю, наделенному соответствующим правом, необходимо повторно отправить запрос. Продолжительность 24-часового периода для утверждения нельзя изменить.

Выполните действия, описанные в этой статье, чтобы утвердить или отклонить запросы для ролей Azure AD.

## <a name="view-pending-requests"></a>Просмотр ожидающих запросов

В качестве делегированного утверждающего лица вы получите уведомление по электронной почте, когда запрос роли Azure AD ожидает утверждения. Эти ожидающие запросы можно просмотреть в управление привилегированными пользователями.

1. Войдите на [портал Azure](https://portal.azure.com/).

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

## <a name="next-steps"></a>Дальнейшие действия

- [Уведомления по электронной почте в управление привилегированными пользователями](pim-email-notifications.md)
- [Утверждение или отклонение запросов ролей ресурсов Azure в управление привилегированными пользователями](pim-resource-roles-approval-workflow.md)
