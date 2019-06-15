---
title: Закрыть рабочую или учебную учетную запись в неуправляемом каталоге — Azure Active Directory | Документация Майкрософт
description: Как закрыть рабочую или учебную учетную запись в неуправляемый каталог Azure Active Directory.
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
ms.openlocfilehash: 39b359ef7feeaec541ba17e98a5d1e9b74c6403a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65958005"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-directory"></a>Закрыть рабочую или учебную учетную запись в неуправляемом каталоге

Если вы являетесь пользователем в неуправляемых организации Azure Active Directory (Azure AD) и вы больше не нужно использовать приложения этой организации или обслуживания — это все взаимосвязи с вашей учетной записи можно закрыть в любое время. Неуправляемый каталог нет глобального администратора. В неуправляемом каталоге закрыть учетные записи самостоятельно, без необходимости обратитесь к администратору.

Пользователи в неуправляемом каталоге часто создаются во время самообслуживания регистрации. Примером может служить информационным работником в организации, зарегистрировавший это бесплатная служба. Дополнительные сведения о самостоятельной регистрации, см. в разделе [новые возможности самообслуживания регистрации для Azure Active Directory?](directory-self-service-signup.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>Перед началом работы

Перед закрытием учетной записи, следует убедиться в наличии следующих элементов:

* Убедитесь, что вы являетесь пользователем неуправляемых Azure каталога AD. Не удается закрыть учетную запись, если вы входите в управляемый каталог. Если входит в управляемый каталог и хотите закрыть свою учетную запись, свяжитесь с администратором. Сведения о том, как определить, принадлежат ли вы неуправляемого каталога, см. в разделе [удаление пользователя из неуправляемого клиента](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant).

* Сохраните все данные, которые вы хотите сохранить. Сведения о том, как отправить запрос на экспорт, см. в разделе [получение и экспорт системных журналов для неуправляемых клиентов](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants).

> [!WARNING]
> Закрытие учетной записи является необратимой операцией. При закрытии учетной записи, будут удалены все личные данные. Больше не будет иметь доступ к вашей учетной записи и данные, связанные с вашей учетной записи.

## <a name="close-your-account"></a>Закрыть свою учетную запись

Чтобы закрыть неуправляемых рабочей или учебной учетной записи, выполните следующие действия.

1. Войдите в [закрыть свою учетную запись](https://go.microsoft.com/fwlink/?linkid=873123), учетной записи, которую вы хотите закрыть.

1. На **Мои запросы данных**выберите **закрыть учетную запись**.

    ![Мои запросы данных — закрыть учетную запись](./media/users-close-account/close-account.png)

1. Просмотрите сообщение подтверждения, а затем выберите **Да**.

    ![Мои запросы данных — подтверждение закрытия](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Что такое самообслуживания регистрации для Azure Active Directory?](directory-self-service-signup.md)
- [Удаление пользователя из неуправляемого клиента](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [Доступ к и экспорт системных журналов для неуправляемых клиентов](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)
