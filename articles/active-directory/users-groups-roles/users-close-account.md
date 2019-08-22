---
title: Закрытие рабочей или учебной учетной записи в неуправляемом каталоге — Azure Active Directory | Документация Майкрософт
description: Как закрыть рабочую или учебную учетную запись в неуправляемой Azure Active Directory.
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
ms.openlocfilehash: 0da4c6b1c1434dae564ab4876a3ab3f341a87097
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891956"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-directory"></a>Закрытие рабочей или учебной учетной записи в неуправляемом каталоге

Если вы являетесь пользователем в неуправляемой Организации Azure Active Directory (Azure AD) и вам больше не нужно использовать приложения из этой организации или не хотите поддерживать взаимосвязь с ней, вы можете закрыть учетную запись в любое время. Неуправляемый каталог не имеет глобального администратора. Пользователи в неуправляемом каталоге могут закрывать свои учетные записи самостоятельно, не обращаясь к администратору.

Пользователи в неуправляемом каталоге часто создаются во время самостоятельной регистрации. Примером может быть информационный работник в Организации, который подписывается на бесплатную службу. Дополнительные сведения о самостоятельной регистрации см. в разделе [что такое самостоятельная регистрация для Azure Active Directory?](directory-self-service-signup.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>Перед началом работы

Прежде чем можно будет закрыть учетную запись, необходимо подтвердить следующие элементы:

* Убедитесь, что вы являетесь пользователем неуправляемого каталога Azure AD. Вы не можете закрыть учетную запись, если вы принадлежите к управляемому каталогу. Если вы входите в управляемый каталог и хотите закрыть учетную запись, обратитесь к администратору. Сведения о том, как определить, входит ли вы в неуправляемый каталог, см. в разделе [Удаление пользователя из неуправляемого клиента](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant).

* Сохраните все данные, которые нужно сохранить. Сведения о том, как отправить запрос на экспорт, см. в разделе [доступ к журналам, созданным системой, и их экспорт для неуправляемых клиентов](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants).

> [!WARNING]
> Закрытие учетной записи необратимо. При закрытии учетной записи все личные данные будут удалены. У вас больше не будет доступа к вашей учетной записи и данным, связанным с вашей учетной записью.

## <a name="close-your-account"></a>Закрытие учетной записи

Чтобы закрыть неуправляемую рабочую или учебную учетную запись, выполните следующие действия.

1. Войдите, чтобы [Закрыть учетную запись](https://go.microsoft.com/fwlink/?linkid=873123), используя учетную запись, которую нужно закрыть.

1. В **окне Мои запросы к данным**выберите **Закрыть учетную запись**.

    ![Мои запросы к данным — закрытие учетной записи](./media/users-close-account/close-account.png)

1. Просмотрите сообщение с подтверждением и нажмите кнопку **Да**.

    ![Мои запросы к данным — подтвердить закрытие](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>Следующие шаги

- [Что такое самостоятельная регистрация для Azure Active Directory?](directory-self-service-signup.md)
- [Удаление пользователя из неуправляемого клиента](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [Доступ к журналам, созданным системой, и их экспорт для неуправляемых клиентов](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)
