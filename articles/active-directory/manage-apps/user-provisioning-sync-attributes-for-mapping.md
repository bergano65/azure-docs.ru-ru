---
title: Синхронизация атрибутов в Azure AD для сопоставления | Документация Майкрософт
description: Узнайте, как синхронизировать атрибуты из локальной службы Active Directory в Azure AD. При настройке подготовки пользователей для приложений SaaS, используйте функции расширения каталогов для добавления исходных атрибутов, которые не синхронизируются по умолчанию.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: mimart
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9460bc924ea662646360d1a3f5087949a39a03f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65806121"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>Синхронизировать атрибут из локальной службы Active Directory в Azure AD для подготовки для приложения

При настройке сопоставлений атрибутов для подготовки пользователей, может оказаться, что необходимо сопоставить атрибут не отображается в **исходный атрибут** списка. В этой статье показано, как добавить отсутствует атрибут с синхронизации его из вашей локальной Active Directory (AD) с Azure Active Directory (Azure AD).

Azure AD должен содержать все данные, необходимые для создания профиля пользователя при подготовке учетных записей пользователей Azure AD в приложение SaaS. В некоторых случаях, чтобы сделать данные доступными можно требуется синхронизировать атрибуты из вашей локальной AD с Azure AD. Azure AD Connect автоматически синхронизирует некоторые атрибуты в Azure AD, но не все атрибуты. Кроме того некоторые атрибуты (например, SAMAccountName), которые синхронизируются по умолчанию не может быть доступен через API Azure AD Graph. В таких случаях можно использовать функции расширения каталогов Azure AD Connect для синхронизации атрибута в Azure AD. Таким образом, атрибут будет видимым для API Azure AD Graph и службой подготовки Azure AD.

Если данные, необходимые для подготовки в Active Directory, но недоступен для подготовки из-за причин, описанных выше, выполните следующие действия.
 
## <a name="sync-an-attribute"></a>Синхронизировать атрибут 

1. Откройте мастер Azure AD Connect, Выбор задач и затем выберите **настроить параметры синхронизации**.

   ![Azure Active Directory Connect в дополнительные задачи мастера](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Войдите как глобальный администратор Azure AD. 

3. На **дополнительных функций** выберите **синхронизация атрибутов расширения каталога**.
 
   ![Azure Active Directory Connect в дополнительные возможности мастера](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Выберите атрибуты, которые вы хотите расширить в Azure AD.
   > [!NOTE]
   > Поиск в разделе **доступные атрибуты** чувствительно к регистру.

   ![Azure Active Directory Connect Directory расширения выбора страница мастера](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Завершите работу мастера Azure AD Connect и Разрешить полный цикл синхронизации для запуска. После завершения цикла расширена схема, и новые значения синхронизируются между локальной AD и Azure AD.
 
6. На портале Azure, находясь [изменение сопоставлений атрибутов пользователя](customize-application-attributes.md), **исходный атрибут** список теперь будет содержать добавленный атрибут в формате `<attributename> (extension_<appID>_<attributename>)`. Выберите атрибут и сопоставить его к целевому приложению для подготовки.

   ![Azure Active Directory Connect Directory расширения выбора страница мастера](media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> Позволяет назначить ссылочные атрибуты из локальной AD, такие как **managedby** или **DN/DistinguishedName**, сейчас не поддерживается. Вы можете запросить эту функцию на [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory). 

## <a name="next-steps"></a>Дальнейшие действия

* [Определить, кто находится в области для подготовки](define-conditional-rules-for-provisioning-user-accounts.md)
