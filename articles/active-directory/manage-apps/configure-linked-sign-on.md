---
title: Связанный вход для приложений Azure AD — платформа Microsoft Identity
description: Настройка связанного единого входа (SSO) для корпоративных приложений Azure на платформе удостоверений Microsoft Identity (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 05/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c4547bddeea8b67bd3377124b1c299662fea3e6
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274167"
---
# <a name="configure-linked-sign-on"></a>Настройка связанного единого входа

При добавлении коллекции или веб-приложения не из коллекции можно использовать один из вариантов единого входа, [связанный с входом](what-is-single-sign-on.md). Выберите этот параметр, чтобы добавить ссылку на приложение на панели доступа Azure AD в вашей организации или на портале Office 365. Этот метод можно использовать для добавления ссылок на пользовательские веб-приложения, которые в настоящее время используют службы федерации Active Directory (AD FS) (или другую службу федерации) вместо Azure AD для проверки подлинности. Еще он позволяет просто добавить глубокие ссылки на определенные страницы SharePoint или другие веб-сайты. Они появятся на панели доступа пользователя.

## <a name="before-you-begin"></a>Перед началом работы

Если приложение еще не добавлено в клиент Azure AD, ознакомьтесь со статьей о [добавлении приложения из коллекции](add-gallery-app.md) или [добавлении приложения не из коллекции](add-non-gallery-app.md).

### <a name="open-the-app-and-select-linked-sign-on"></a>Откройте приложение и выберите пункт "связанный вход".

1. Войдите на [портал Azure](https://portal.azure.com) в качестве администратора облачных приложений или администратора приложения для клиента Azure AD.

1. Перейдите в раздел **Azure Active Directory** > **корпоративные приложения**. Вы увидите случайную выборку приложений, размещенных в арендаторе Azure AD. 

1. В меню **Тип приложения** выберите **Все приложения** и щелкните **Применить**.

1. В поле поиска введите имя приложения, а затем выберите нужное приложение на панели результатов.

1. В разделе **Управление** щелкните **Единый вход**. 

1. Выберите **связанный**.

1. Введите URL-адрес приложения для связи. Введите URL-адрес и нажмите кнопку **сохранить**. 
 
1. Приложение можно назначить пользователям и группам, что приведет к отображению приложения в средстве [запуска приложений Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) или на [панели доступа Azure AD](end-user-experiences.md) для этих пользователей.

1. Щелкните **Сохранить**.

## <a name="next-steps"></a>Дополнительная информация

- [Assign users and groups to an application in Azure Active Directory](methods-for-assigning-users-and-groups.md) (Назначение пользователей и групп для приложения в Azure Active Directory)
- [Managing user account provisioning for enterprise apps in the Azure portal](configure-automatic-user-provisioning-portal.md) (Управление подготовкой учетных записей пользователей для корпоративных приложений на портале Azure)
