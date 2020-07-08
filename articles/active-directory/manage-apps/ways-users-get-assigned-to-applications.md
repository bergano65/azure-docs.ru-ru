---
title: Назначение пользователей приложениям | Документация Майкрософт
description: Общие сведения о назначении пользователей для приложения в клиенте
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45124862fffa3a1ef6f601733407fbbea4eb5e74
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84762978"
---
# <a name="how-to-assign-users-to-applications"></a>Назначение пользователей для приложений

В этой статье представлены общие сведения о назначении пользователей для приложения в клиенте.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Как пользователи назначаются для приложений в Azure AD?

Чтобы пользователь смог получить доступ к приложению, сначала его нужно любым образом назначить этому приложению. Это может сделать администратор, бизнес-делегат или, в некоторых случаях, сам пользователь. Ниже описаны способы назначения пользователя для приложения.

1.  Администратор [назначает пользователей](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) непосредственно для приложения.

2.  Администратор [назначает для приложения группу](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal), в которую входит пользователь. Ею может быть одна из следующих:

    * Группа, синхронизированная из локальной среды.

    * Статическая группа безопасности, созданная в облаке.

    * [Динамическая группа безопасности](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal), созданная в облаке.

    * Группа Office 365, созданная в облаке.

    * Группа ["Все пользователи"](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups).

3.  Администратор включает [самостоятельный доступ к приложениям](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) , позволяющий пользователю добавлять приложения с помощью [панели доступа к приложениям](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **Добавление функции приложения** **без бизнес-утверждения** .

4.  Администратор включает [самостоятельный доступ к приложениям](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) , позволяющий пользователю добавлять приложения с помощью [панели доступа к приложениям](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **Добавить функцию приложения** , но только до версии w**i до утверждения от выбранного набора утверждающих лиц** .

5.  Администратор включает [самостоятельное управление группами](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), чтобы позволить пользователям присоединяться к группам, которым назначено приложение, **без бизнес-утверждения**.

6.  Администратор включает [самостоятельное управление группами](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), чтобы позволить пользователям присоединяться к группам, которым назначено приложение, только **с предварительного разрешения выбранных утверждающих лиц**.

7.  Администратор назначает пользователю лицензии напрямую для приложений Майкрософт, например [Microsoft Office 365](https://products.office.com/).

8.  Администратор назначает группе, в которую входит пользователь, лицензию на приложение Майкрософт, например [Microsoft Office 365](https://products.office.com/).

9.  [Администратор дает свое согласие на использование приложения](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) всеми пользователями, а затем пользователь входит в это приложение.

10. Пользователь сам [дает согласие на использование приложения](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) при входе в него.

## <a name="next-steps"></a>Дальнейшие шаги
[Управление приложениями с помощью Azure Active Directory](what-is-application-management.md)
