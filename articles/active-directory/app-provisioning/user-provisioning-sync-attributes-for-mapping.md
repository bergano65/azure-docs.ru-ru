---
title: Синхронизация атрибутов в Azure AD для сопоставления
description: Узнайте, как синхронизировать атрибуты из локального Active Directory с Azure AD. При настройке подготовки пользователей для приложений SaaS используйте функцию расширения каталога, чтобы добавить исходные атрибуты, которые не синхронизированы по умолчанию.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/13/2019
ms.author: kenwith
ms.openlocfilehash: 00c4dec329456409bc8d5b77dca72f25daf9f5c7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84781079"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>Синхронизация атрибута из локального Active Directory с Azure AD для подготовки приложения

При настройке сопоставлений атрибутов для подготовки пользователей может оказаться, что атрибут, который нужно сопоставить, не отображается в списке **исходных атрибутов** . В этой статье показано, как добавить недостающий атрибут, синхронизируя его из локальной Active Directory (AD) с Azure Active Directory (Azure AD).

В Azure AD должны содержаться все данные, необходимые для создания профиля пользователя при подготовке учетных записей пользователей из Azure AD в приложение SaaS. В некоторых случаях для обеспечения доступности данных может потребоваться синхронизация атрибутов из локальной службы AD в Azure AD. Azure AD Connect автоматически синхронизирует определенные атрибуты с Azure AD, но не со всеми атрибутами. Кроме того, некоторые атрибуты (например, SAMAccountName), синхронизированные по умолчанию, могут быть недоступны с помощью Microsoft Graph API. В таких случаях можно использовать функцию расширения каталога Azure AD Connect, чтобы синхронизировать атрибут с Azure AD. Таким образом, атрибут будет видим для Microsoft Graph API и службы подготовки Azure AD.

Если данные, необходимые для подготовки, находятся в Active Directory но недоступны для подготовки из-за описанных выше причин, выполните следующие действия.
 
## <a name="sync-an-attribute"></a>Синхронизация атрибута 

1. Откройте мастер Azure AD Connect, выберите задачи, а затем выберите **настроить параметры синхронизации**.

   ![Страница "дополнительные задачи" мастера Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Войдите в систему как глобальный администратор Azure AD. 

3. На странице **Дополнительные компоненты** выберите **синхронизировать атрибут расширения каталога**.
 
   ![Страница дополнительных компонентов мастера Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Выберите атрибуты, которые требуется расширить в Azure AD.
   > [!NOTE]
   > При поиске в списке **Доступные атрибуты** учитывается регистр.

   ![Страница выбора расширений каталога мастера Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Завершите работу мастера Azure AD Connect и дождитесь выполнения цикла полной синхронизации. По завершении цикла схема расширяется, а новые значения синхронизируются между локальной службой AD и Azure AD.
 
6. В портал Azure при [редактировании сопоставлений атрибутов пользователя](customize-application-attributes.md)список **исходных атрибутов** теперь будет содержать добавленный атрибут в формате `<attributename> (extension_<appID>_<attributename>)` . Выберите атрибут и сопоставьте его целевому приложению для подготовки.

   ![Страница выбора расширений каталога мастера Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> Возможность подготавливать ссылочные атрибуты из локальной службы AD, например **ManagedBy** или **DN/distinguishedName**, сейчас не поддерживается. Эту функцию можно запросить на [голоса пользователя](https://feedback.azure.com/forums/169401-azure-active-directory). 

## <a name="next-steps"></a>Дальнейшие шаги

* [Определение пользователей, которые находятся в области подготовки](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
