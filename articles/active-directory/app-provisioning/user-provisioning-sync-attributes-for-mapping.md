---
title: Синхронизация атрибутов с Azure AD для отображения Документы Майкрософт
description: Узнайте, как синхронизировать атрибуты из вашего предприимчивого Active Directory в Azure AD. При настройке подготовки пользователей к приложениям SaaS используйте функцию расширения каталога для добавления исходных атрибутов, которые не синхронизированы по умолчанию.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: mimart
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09d1efaf54bee65bd3274987e68e643f887baade
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522277"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>Синхронизация атрибута из вашего предприимчивого Active Directory в Azure AD для подготовки к приложению

При настройке отображений атрибутов для подготовки пользователей может обнаружить, что атрибут, который вы хотите сопоставить, не отображается в списке **атрибутов Исхода.** В этой статье показано, как добавить недостающий атрибут, синхронизировав его с вашего предпосылок Active Directory (AD) в Active Directory Azure (Azure AD).

Azure AD должен содержать все данные, необходимые для создания профиля пользователя при подготовке учетных записей пользователей от Azure AD к приложению SaaS. В некоторых случаях для того, чтобы данные были доступны, может потребоваться синхронизация атрибутов от вашего собственного AD до Azure AD. Azure AD Connect автоматически синхронизирует определенные атрибуты с Azure AD, но не все атрибуты. Кроме того, некоторые атрибуты (например SAMAccountName), синхронизированные по умолчанию, могут не быть выставлены с помощью API Microsoft Graph. В этих случаях можно использовать функцию расширения каталога Azure AD Connect для синхронизации атрибута с Azure AD. Таким образом, атрибут будет виден API Microsoft Graphи и службе подготовки Azure AD.

Если данные, необходимые для подготовки, приведены в Active Directory, но не доступны для подготовки из-за причин, описанных выше, выполните следующие действия.
 
## <a name="sync-an-attribute"></a>Синхронизация атрибута 

1. Откройте мастер-изуборт юнита Azure AD Connect, выберите задачи, а затем выберите **параметры синхронизации customize.**

   ![Страница дополнительных задач Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Войти в систему в качестве глобального администратора Azure AD. 

3. На странице **Дополнительные функции** выберите **синхронизацию атрибута расширения каталога.**
 
   ![Страница функций Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Выберите атрибут (ы), который требуется распространить на Azure AD.
   > [!NOTE]
   > Поиск в рамках **доступных атрибутов** является конфиденциальным случаем.

   ![Страница выбора выделения мастер-каталогов Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Закончите мастер-разъем Azure AD Connect и позвольте запустить полный цикл синхронизации. Когда цикл завершен, схема расширяется и новые значения синхронизируются между вашим предприимченным AD и Azure AD.
 
6. На портале Azure при [редактировании отображений атрибутов пользователя](customize-application-attributes.md)список **атрибутов «Источник»** теперь будет содержать добавленный атрибут в формате. `<attributename> (extension_<appID>_<attributename>)` Выберите атрибут и нанесите его в целевое приложение для подготовки.

   ![Страница выбора выделения мастер-каталогов Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> Возможность предоставления эталонных атрибутов из находной АД, таких как **управляемый** или **DN/DistinguishedName**, не поддерживается сегодня. Вы можете запросить эту функцию на [голоспользователя](https://feedback.azure.com/forums/169401-azure-active-directory). 

## <a name="next-steps"></a>Дальнейшие действия

* [Определить, кто находится в сфере подготовки](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
