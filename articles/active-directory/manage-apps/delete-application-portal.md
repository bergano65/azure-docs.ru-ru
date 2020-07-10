---
title: Краткое руководство. Удаление приложения из арендатора Azure Active Directory (Azure AD)
description: В этом кратком руководстве показано, как с помощью портала Azure можно удалить приложение из арендатора Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02d12538fce5ccc3905ea1170fc1a8324309004f
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2020
ms.locfileid: "85956004"
---
# <a name="quickstart-delete-an-application-from-your-azure-active-directory-azure-ad-tenant"></a>Краткое руководство. Удаление приложения из арендатора Azure Active Directory (Azure AD)

В этом кратком руководстве показано, как с помощью портала Azure удалить приложение, добавленное в арендатор Azure AD.

## <a name="prerequisites"></a>Предварительные требования

Чтобы удалить приложение из арендатора Azure AD, вам потребуется следующее:

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно.
- Одна из следующих ролей: глобальный администратор, администратор облачных приложений, администратор приложений или владелец субъекта-службы.
- (Необязательно. Знакомство с инструкциями по [просмотру приложений](view-applications-portal.md).)
- (Необязательно. Знакомство с инструкциями по [добавлению приложения](add-application-portal.md).)
- (Необязательно. Знакомство с инструкциями по [настройке приложения](add-application-portal-configure.md).)
- (Необязательно. Знакомство со статьей [Настройка единого входа](add-application-portal-setup-sso.md).)

>[!IMPORTANT]
>Мы рекомендуем не использовать рабочую среду для тестового выполнения шагов из этого краткого руководства.

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>Удаление приложения из арендатора Azure AD

Чтобы удалить приложение из арендатора Azure AD, сделайте следующее:

1. На портале Azure AD выберите **Корпоративные приложения**, а затем найдите и выберите приложение, которое вы хотите удалить. Сейчас мы удаляем приложение GitHub_test, добавленное при работе с предыдущим кратким руководством.
2. В разделе "Управление" в области навигации выберите **Свойства**.
3. Щелкните "Удалить" и выберите "Да", чтобы подтвердить удаление приложения из арендатора Azure AD.


## <a name="next-steps"></a>Дальнейшие действия

- [Рекомендации по управлению приложениями](application-management-fundamentals.md)
- [Общие сценарии управления приложениями](common-scenarios.md)
- [Видимость и контроль управления приложениями](cloud-app-security.md)