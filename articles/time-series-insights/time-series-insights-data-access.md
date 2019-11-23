---
title: Configure security to grant data access - Azure Time Series Insights Preview | Microsoft Docs
description: Learn how to configure security, permissions, and manage data access policies in your Azure Time Series Insights Preview environment.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/20/2019
ms.custom: seodec18
ms.openlocfilehash: b79ca1d93baf1941d5de8db0c314f9cd21e51056
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74328071"
---
# <a name="grant-data-access-to-an-environment"></a>Предоставление доступа к данным в среде

В этой статье рассматриваются два типа политик доступа службы "Аналитика временных рядов Azure" (предварительная версия).

> [!TIP]
> Read [Authentication and Authorization](time-series-insights-authentication-and-authorization.md) for Azure Active Directory app registration steps.

## <a name="sign-in-to-time-series-insights"></a>Sign in to Time Series Insights

1. Войдите на [портале Azure](https://portal.azure.com/).
1. Найдите среду "Аналитика временных рядов". Введите `Time Series` в поле **Поиск**. Выберите **Среда временных рядов** в результатах поиска.
1. Выберите среду Time Series Insights в списке.

## <a name="grant-data-access"></a>Предоставление доступа к данным

Сделайте следующее, чтобы предоставить доступ к данным для субъекта-пользователя.

1. Выберите **Политики доступа к данным**, а затем нажмите кнопку **+ Добавить**.

    [![Select and add a Data Access Policy](media/data-access/data-access-select-add-button.png)](media/data-access/data-access-select-add-button.png#lightbox)

1. Выберите **Выбор пользователя**. Выполните поиск по имени пользователя или адресу электронной почты, чтобы найти пользователя, которого следует добавить. Select **Select** to confirm the selection.

    [![Select a user to add](media/data-access/data-access-select-user-to-confirm.png)](media/data-access/data-access-select-user-to-confirm.png#lightbox)

1. Выберите **Выбор ролей**. Выберите соответствующую роль доступа для пользователя.

    * Выберите **Участник**, если вы хотите разрешить пользователю изменять ссылочные данные и предоставить другим пользователям среды доступ к сохраненным запросам и перспективам.

    * В противном случае выберите **Читатель**, чтобы разрешить пользователю обращаться к данным в среде и сохранять в ней личные (не общие) запросы.

   Нажмите кнопку **ОК**, чтобы подтвердить выбор роли.

    [![Confirm the selected role](media/data-access/data-access-select-a-role.png)](media/data-access/data-access-select-a-role.png#lightbox)

1. На странице **Выбор роли пользователя** нажмите кнопку **ОК**.

    [![Select OK on the Select User Role page](media/data-access/data-access-confirm-user-and-role.png)](media/data-access/data-access-confirm-user-and-role.png#lightbox)

1. Убедитесь, что на странице **Политики доступа к данным** перечислены пользователи и соответствующие роли.

    [![Verify the correct users and roles](media/data-access/data-access-verify-and-confirm-assignments.png)](media/data-access/data-access-verify-and-confirm-assignments.png#lightbox)

## <a name="provide-guest-access-from-another-azure-ad-tenant"></a>Provide guest access from another Azure AD tenant

The `Guest` role isn’t a management role. Это термин, используемый для учетной записи, приглашенной из одного клиента в другой. После того как гостевая учетная запись приглашена в каталог клиента, к ней могут быть применены те же права управления доступом, что и в любой другой учетной записи. Предоставить доступ для управления к среде "Аналитика временных рядов Azure" можно с помощью колонки "Управление доступом (IAM)". Кроме того, предоставить доступ к данным в среде можно в колонке "Политики доступа к данным". Дополнительные сведения о гостевом доступе к клиенту Azure Active Directory см. в статье [Добавление пользователей службы совместной работы B2B на портале Azure](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Выполните эти действия, чтобы предоставить гостевой доступ к среде службы "Аналитика временных рядов" пользователю Azure Active Directory из другого клиента.

1. Выберите **Политики доступа к данным**, а затем нажмите кнопку **+ Пригласить**.

    [![Select Data Access Polices, then + Invite](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. Введите адрес электронной почты пользователя, которого вы хотите пригласить. Этот адрес электронной почты должен быть связан с Azure AD. Вы также можете добавить к приглашению личное сообщение.

    [![Enter the email address to find the selected user](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. Найдите всплывающее окно с подтверждением, которое отображается на экране.

    [![Look for the confirmation bubble to appear](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. Выберите **Выбор пользователя**. Найдите адрес электронной почты приглашенного гостевого пользователя, чтобы найти пользователя, которого необходимо добавить. Then, **Select** to confirm the selection.

    [![Select the user and confirm the selection](media/data-access/data-access-select-invited-person-confirmation.png)](media/data-access/data-access-select-invited-person-confirmation.png#lightbox)

1. Выберите **Выбор ролей**. Выберите соответствующую роль гостевого пользователя.

    * Выберите **Участник**, если вы хотите разрешить пользователю изменять ссылочные данные и предоставить другим пользователям среды доступ к сохраненным запросам и перспективам.

    * В противном случае выберите **Читатель**, чтобы разрешить пользователю обращаться к данным в среде и сохранять в ней личные (не общие) запросы.

   Нажмите кнопку **ОК**, чтобы подтвердить выбор роли.

    [![Confirm the role choice](media/data-access/data-access-select-ok-and-confirm.png)](media/data-access/data-access-select-ok-and-confirm.png#lightbox)

1. На странице **Выбор роли пользователя** нажмите кнопку **ОК**.

1. Убедитесь, что на странице **Политики доступа к данным** перечислены гостевые пользователи и соответствующие роли.

    [![Verify that users and roles are correctly assigned](media/data-access/data-access-confirm-invited-users-and-roles.png)](media/data-access/data-access-confirm-invited-users-and-roles.png#lightbox)

1. Now, the guest user will receive an invitation email at the email address specified above. The guest user will select **Get Started** to confirm their acceptance and connect to Azure Cloud.

    [![Guest selects Get Started to accept](media/data-access/data-access-email-invitation.png)](media/data-access/data-access-email-invitation.png#lightbox)

1. After selecting **Get Started** the guest user will be presented with a permissions box associated with the administrator's organization. Upon granting permission by selecting **Accept**, they will be signed in.

    [![Guest reviews permissions and accepts](media/data-access/data-access-grant-permission-sign-in.png)](media/data-access/data-access-grant-permission-sign-in.png#lightbox)

1. The administrator [shares the environment URL](time-series-insights-parameterized-urls.md) with their guest.

1. After the guest user is signed in to the email address you used to invite them, and they accept the invitation, they will be directed to Azure portal. 

1. The guest can now access the shared environment using the environment URL provided by the administrator. They can enter that URL into their web browser for immediate access.

1. The guest user will see the administrator's tenant by selecting their profile icon in the upper-right corner of the Time Series explorer.

    [![Avatar selection on insights.azure.com](media/data-access/data-access-select-tenant-and-instance.png)](media/data-access/data-access-select-tenant-and-instance.png#lightbox)


    After the guest user selects the administrator's tenant, they will have the ability to select the shared Time Series Insights environment. 
    
    They now have all the capabilities associated with the role that you provided them with in **step 5**.

    [![Guest user selects your Azure tenant from drop-down](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте о том, [как добавить источник событий концентратора событий Azure](./time-series-insights-how-to-add-an-event-source-eventhub.md) в среду "Аналитика временных рядов".

* Отправьте [события в источник событий](./time-series-insights-send-events.md).

* Просмотрите [свою среду в обозревателе службы "Аналитика временных рядов" (предварительная версия)](./time-series-insights-update-explorer.md).
