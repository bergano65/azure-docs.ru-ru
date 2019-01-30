---
title: Доменные службы Azure Active Directory. Включение поддержки службы профилей пользователей SharePoint | Документация Майкрософт
description: Настройте управляемые домены доменных служб Azure Active Directory для поддержки синхронизации профилей для сервера SharePoint
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: bb831c2b3cb9aee5146a667841a97771ff0ff85a
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54843537"
---
# <a name="configure-a-managed-domain-to-support-profile-synchronization-for-sharepoint-server"></a>Настройка управляемого домена для поддержки синхронизации профилей для сервера SharePoint
Сервер SharePoint содержит службу профилей пользователей, которая используется для синхронизации профилей пользователей. Для настройки службы профилей пользователей необходимо иметь соответствующее разрешение домена Active Directory. Дополнительные сведения см. в статье [Предоставление доменным службам Active Directory разрешений для синхронизации профилей в SharePoint Server 2013](https://technet.microsoft.com/library/hh296982.aspx).

В этой статье описывается настройка управляемых доменов доменных служб Azure AD для развертывания службы синхронизации профилей пользователей для сервера SharePoint.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="the-aad-dc-service-accounts-group"></a>Группа "Учетные записи службы контроллера домена AAD"
Группа безопасности **Учетные записи службы контроллера домена AAD** доступна в подразделении "Пользователи" на управляемом домене. Эту группу можно найти в оснастке MMC **Пользователи и компьютеры Active Directory** на управляемом домене.

![Группа безопасности "Учетные записи службы контроллера домена AAD"](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts.png)

Участникам этой группы безопасности делегируются следующие привилегии доступа:
- Репликация изменений каталога в атрибуте Root DSE управляемого домена.
- Репликация изменений каталога в контексте именования конфигураций (cn = контейнер конфигурации) управляемого домена.

Эта группа безопасности также входит во встроенную группу **Пред-Windows 2000 доступ**.

![Группа безопасности "Учетные записи службы контроллера домена AAD"](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-properties.png)


## <a name="enable-your-managed-domain-to-support-sharepoint-server-user-profile-sync"></a>Включение управляемого домена для поддержки синхронизации профилей пользователей SharePoint Server
Учетную запись службы, используемую для синхронизации профилей пользователей SharePoint, можно добавить в группу **Учетные записи службы контроллера домена AAD**. В результате синхронизированная учетная запись получает достаточные привилегии доступа для репликации изменений в каталог. На этом этапе настраивается правильная работа синхронизации профилей пользователей сервера SharePoint.

![Добавление участников в группу "Учетные записи службы контроллера домена AAD"](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member.png)

![Добавление участников в группу "Учетные записи службы контроллера домена AAD"](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member2.png)

## <a name="related-content"></a>Похожий контент
* [Предоставление доменным службам Active Directory разрешений для синхронизации профилей в SharePoint Server 2013](https://technet.microsoft.com/library/hh296982.aspx)
