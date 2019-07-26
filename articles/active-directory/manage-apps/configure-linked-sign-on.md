---
title: Связанный вход для приложений Azure AD — платформа Microsoft Identity | Документация Майкрософт
description: Настройка связанного единого входа (SSO) для корпоративных приложений Azure на платформе удостоверений Microsoft Identity (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 05/08/2019
ms.author: msmimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2d9035adb70ab4a5877f3e55dc4115a5ec1f7f5
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68426568"
---
# <a name="configure-linked-sign-on"></a>Настройка связанного единого входа

При добавлении коллекции или веб-приложения не из коллекции можно использовать один из вариантов единого входа, [связанный](what-is-single-sign-on.md)с входом. Выберите этот параметр, чтобы добавить ссылку на приложение на панели доступа Azure AD в вашей организации или на портале Office 365. Этот метод можно использовать для добавления ссылок на пользовательские веб-приложения, которые в настоящее время используют службы федерации Active Directory (AD FS) (или другую службу федерации) вместо Azure AD для проверки подлинности. Еще он позволяет просто добавить глубокие ссылки на определенные страницы SharePoint или другие веб-сайты. Они появятся на панели доступа пользователя.

## <a name="before-you-begin"></a>Перед началом работы

Если приложение не было добавлено в клиент Azure AD, см. статью [Добавление приложения коллекции](add-gallery-app.md) или Добавление приложения, [не относящегося к коллекции](add-non-gallery-app.md).

### <a name="open-the-app-and-select-linked-sign-on"></a>Откройте приложение и выберите пункт "связанный вход".

1. Войдите на [портал Azure](https://portal.azure.com) в качестве администратора облачных приложений или администратора приложения для клиента Azure AD.

1. Перейдите в раздел **Azure Active Directory** > **корпоративные приложения**. Вы увидите случайную выборку приложений, размещенных в арендаторе Azure AD. 

1. В меню **Тип приложения** выберите **Все приложения** и щелкните **Применить**.

1. Введите имя приложения в поле поиска, а затем выберите приложение из результатов.

1. В разделе **Управление** щелкните **Единый вход**. 

1. Выберите **связанный**.

1. Введите URL-адрес приложения для связи. Введите URL-адрес и нажмите кнопку **сохранить**. 
 
1. Приложение можно назначить пользователям и группам, что приведет к отображению приложения в средстве [запуска приложений Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) или на [панели доступа Azure AD](end-user-experiences.md) для этих пользователей.

1. Щелкните **Сохранить**.

## <a name="next-steps"></a>Следующие шаги

- [Назначение пользователям или группам приложения](methods-for-assigning-users-and-groups.md)
- [Настройка автоматической подготовки учетных записей пользователей](configure-automatic-user-provisioning-portal.md)
