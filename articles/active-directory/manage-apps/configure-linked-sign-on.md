---
title: Связанные подписи для приложений Azure AD - идентификационная платформа Майкрософт
description: Настройка связанного единого ввоза (SSO) к корпоративным приложениям Azure AD в платформе идентификации Майкрософт (Azure AD)
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
ms.openlocfilehash: dfe4aeb17f482cc9d4126efc6d65d3f7d173536b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063549"
---
# <a name="configure-linked-sign-on"></a>Настройка связанного единого входа

При добавлении галереи или веб-приложения, не являющегося галереей, один из доступных вам вариантов входного знака [связан с ссылкой на.](what-is-single-sign-on.md) Выберите эту опцию, чтобы добавить ссылку на приложение на панели доступа Azure AD организации или на портале Office 365. Этот метод можно использовать для добавления ссылок на пользовательские веб-приложения, которые в настоящее время используют службы федерации active Directory (или другой службы федерации) вместо Azure AD для проверки подлинности. Еще он позволяет просто добавить глубокие ссылки на определенные страницы SharePoint или другие веб-сайты. Они появятся на панели доступа пользователя.

## <a name="before-you-begin"></a>Перед началом

Если приложение еще не добавлено в клиент Azure AD, ознакомьтесь со статьей о [добавлении приложения из коллекции](add-gallery-app.md) или [добавлении приложения не из коллекции](add-non-gallery-app.md).

### <a name="open-the-app-and-select-linked-sign-on"></a>Откройте приложение и выберите связанную регистрацию

1. Войдите на [портал Azure](https://portal.azure.com) в качестве администратора облачных приложений или администратора приложения для клиента Azure AD.

1. Перейдите к приложениям **Azure Active Directory** > **Enterprise.** Вы увидите случайную выборку приложений, размещенных в арендаторе Azure AD. 

1. В меню **Тип приложения** выберите **Все приложения** и щелкните **Применить**.

1. В поле поиска введите имя приложения, а затем выберите нужное приложение на панели результатов.

1. В разделе **Управление** щелкните **Единый вход**. 

1. Выберите **Связанные**.

1. Введите URL приложения для ссылки на. Введите URL и выберите **Сохранить**. 
 
1. Вы можете назначить пользователей и группы приложения, что приводит к тому, что приложение появляется в [пусковой установке приложения Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) или [панели доступа Azure AD](end-user-experiences.md) для этих пользователей.

1. Нажмите кнопку **Сохранить**.

## <a name="next-steps"></a>Дальнейшие действия

- [Assign users and groups to an application in Azure Active Directory](methods-for-assigning-users-and-groups.md) (Назначение пользователей и групп для приложения в Azure Active Directory)
- [Managing user account provisioning for enterprise apps in the Azure portal](../app-provisioning/configure-automatic-user-provisioning-portal.md) (Управление подготовкой учетных записей пользователей для корпоративных приложений на портале Azure)
