---
title: Закрыть работу или школьный счет в неуправляемом каталоге Azure AD
description: Как закрыть работу или школьную учетную запись в неуправляемом каталоге Azure Active.
services: active-directory
author: rolyon
manager: mtillman
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 05/20/2019
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c101c0ef7932151e675c5c514ac558e6e0f94b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73815719"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-directory"></a>Закройте свой рабочий или школьный счет в неуправляемом каталоге

Если вы являетесь пользователем неуправляемой организации Active Directory (Azure AD) и вам больше не нужно использовать приложения этой организации или поддерживать с ней какую-либо связь, вы можете закрыть свою учетную запись в любое время. Неуправляемый каталог не имеет глобального администратора. Пользователи в неуправляемом каталоге могут закрыть свои учетные записи самостоятельно, не связываясь с администратором.

Пользователи в неуправляемом каталоге часто создаются во время регистрации самообслуживания. Примером может быть информационный работник в организации, который подписывается на бесплатную услугу. Для получения дополнительной информации о регистрации самообслуживания [см.](directory-self-service-signup.md)

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>Перед началом

Прежде чем вы сможете закрыть свою учетную запись, вы должны подтвердить следующие элементы:

* Убедитесь, что вы являетесь пользователем неуправляемого каталога Azure AD. Вы не можете закрыть свою учетную запись, если вы принадлежите к управляемому каталогу. Если вы принадлежите к управляемому каталогу и хотите закрыть учетную запись, вы должны связаться с администратором. Для получения информации о том, как определить, принадлежит ли вы к неуправляемому каталогу, [см.](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)

* Сохраните все данные, которые вы хотите сохранить. Для получения информации о том, [Accessing and exporting system-generated logs for Unmanaged Tenants](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)как отправить экспортный запрос, см.

> [!WARNING]
> Закрытие учетной записи необратимо. При закрытии учетной записи все персональные данные будут удалены. У вас больше не будет доступа к вашей учетной записи и данным, связанным с вашей учетной записью.

## <a name="close-your-account"></a>Закрытие учетной записи

Чтобы закрыть неуправляемую работу или школьный счет, выполните следующие действия:

1. Воспользуйтесь, чтобы [закрыть учетную запись,](https://go.microsoft.com/fwlink/?linkid=873123)используя учетную запись, которую вы хотите закрыть.

1. На **моих запросах данных**выберите **Закрыть учетную запись**.

    ![Мои запросы данных - Закрыть учетную запись](./media/users-close-account/close-account.png)

1. Просмотрите сообщение подтверждения, а затем выберите **Да**.

    ![Мои запросы данных - Подтвердите закрытие](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Что такое регистрация самообслуживания для каталога Azure Active?](directory-self-service-signup.md)
- [Удаление пользователя из неуправляемого клиента](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [Доступ к системным журналам для неуправляемых клиентов и их экспорт](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)
