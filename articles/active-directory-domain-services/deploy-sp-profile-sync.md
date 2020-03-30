---
title: Включить службу профиля пользователя SharePoint с Azure AD DS Документы Майкрософт
description: Узнайте, как настроить домена с управлением активных доменов Azure Active Directory Domain Services для поддержки синхронизации профиля для сервера SharePoint
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: 9d983015927d2635f69a327a9c5b168056542519
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613866"
---
# <a name="configure-azure-active-directory-domain-services-to-support-user-profile-synchronization-for-sharepoint-server"></a>Настройка службы функционального домена Azure Active Directory для поддержки синхронизации профиля пользователя для сервера SharePoint

SharePoint Server включает в себя службу синхронизации профилей пользователей. Эта функция позволяет хранить профили пользователей в центральном месте и доступны на нескольких сайтах и фермах SharePoint. Для настройки службы профиля пользователя SharePoint Server соответствующие разрешения должны быть предоставлены в домене Azure Active Directory Domain Services (Azure AD DS). Для получения дополнительной [user profile synchronization in SharePoint Server](https://technet.microsoft.com/library/hh296982.aspx)информации см.

В этой статье показано, как настроить DD Azure AD, чтобы позволить службе синхронизации профиля пользователя SharePoint Server.

## <a name="before-you-begin"></a>Перед началом

Для завершения этой статьи необходимы следующие ресурсы и привилегии:

* Активная подписка Azure.
    * Если у вас еще нет подписки Azure, создайте [учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Связанный с вашей подпиской клиент Azure Active Directory, синхронизированный с локальным или облачным каталогом.
    * Если потребуется, [создайте клиент Azure Active Directory][create-azure-ad-tenant] или [свяжите подписку Azure со своей учетной записью][associate-azure-ad-tenant].
* Управляемый домен доменных служб Azure Active Directory, включенный и настроенный в клиенте Azure AD.
    * При необходимости заполните учебник для [создания и настройки экземпляра служб доменов Active Directory Службы Azure.][create-azure-ad-ds-instance]
* VM управления Windows Server, который соединен с доменом Azure AD DS.
    * При необходимости, завершить учебник для [создания управления VM][tutorial-create-management-vm].
* Учетная запись пользователя, входящая в группу *администраторов Azure AD DC* в клиенте Azure AD.
* Учетная запись службы sharePoint для службы синхронизации профиля пользователя.
    * При необходимости смотрите [план административных и сервисных учетных записей в SharePoint Server][sharepoint-service-account].

## <a name="service-accounts-overview"></a>Обзор учетных записей служб

В домене Azure AD DS, управляемом AD DS, группа безопасности под названием **AAD DC Service Accounts** существует в составе организационной единицы *пользователей* (OU). Участникам этой группы безопасности делегируются следующие привилегии доступа:

- **Повторите каталог Изменения** привилегии на корень DSE.
- **Привилегия «Повторная информация» изменяет сяркопизм** каталога в контексте именования *конфигурации* (контейнер).`cn=configuration`

Группа безопасности **службы AAD DC** также является членом встроенной группы **Pre-Windows 2000 Compatible Access.**

При добавлении в эту группу безопасности учетная запись службы синхронизации профиля пользователя SharePoint Server получает необходимые привилегии для правильной работы.

## <a name="enable-support-for-sharepoint-server-user-profile-sync"></a>Включить поддержку для синхронизации профиля пользователя SharePoint Server

Учетная запись службы SharePoint Server нуждается в адекватных привилегиях для воспроизведения изменений в каталоге и обеспечения правильной синхронизации профиля пользователя SharePoint Server. Чтобы предоставить эти привилегии, добавьте учетную запись службы, используемую для синхронизации профиля пользователя SharePoint, в группу **учетных записей службы AAD DC.**

Из вашего управления Azure AD DS VM выполните следующие шаги:

> [!NOTE]
> Чтобы отсеять членство в группе в домене Azure AD DS, необходимо ввести регистрацию в учетную запись пользователя, которая является членом *группы администраторов AAD DC.*

1. С начального экрана выберите **Административные инструменты.** Список доступных инструментов управления показано, что были установлены в учебнике для [создания управления VM][tutorial-create-management-vm].
1. Чтобы управлять членством в группе, выберите **Управляющий центр Active Directory** из списка административных инструментов.
1. В левой панели выберите идомет Azure AD DS, например *aaddscontoso.com.* Отображается список существующих ОУ и ресурсов.
1. Выберите **группу** безопасности службы обслуживания AAD DC, а затем выберите группу безопасности *службы AAD.*
1. Выберите **участников,** затем выберите **Добавить...**.
1. Введите имя учетной записи службы SharePoint, а затем выберите **OK**. В следующем примере учетная запись службы SharePoint называется *spadmin:*

    ![Добавление учетной записи службы SharePoint в группу безопасности службы AAD DC](./media/deploy-sp-profile-sync/add-member-to-aad-dc-service-accounts-group.png)

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации см. [Разрешения Grant Active Directory Domain Services для синхронизации профиля в сервере SharePoint](https://technet.microsoft.com/library/hh296982.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md

<!-- EXTERNAL LINKS -->
[sharepoint-service-account]: /sharepoint/security-for-sharepoint-server/plan-for-administrative-and-service-accounts
