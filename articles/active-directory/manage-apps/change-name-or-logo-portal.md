---
title: Изменение имени или логотипа корпоративного приложения в Azure Active Directory | Документация Майкрософт
description: Как изменить имя или логотип настраиваемого корпоративного приложения в Azure Active Directory
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
ms.date: 04/05/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30da8d6843c27c42d4d99adef50b9ad98a131c95
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65780925"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>Изменение имени или логотипа корпоративного приложения в Azure Active Directory

Вы можете легко изменить имя или логотип настраиваемого корпоративного приложения в Azure Active Directory (Azure AD). Необходимо иметь соответствующие разрешения для внесения этих изменений, и должен быть создателем пользовательского приложения.

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>Изменение имени или логотипа корпоративного приложения?

1. Войдите в [портал Azure Active Directory](https://aad.portal.azure.com/) с помощью учетной записи глобального администратора для каталога. **Центр администрирования Azure Active Directory** появится страница.
2. В области слева выберите **Корпоративные приложения**. Появится список корпоративных приложений.
3. Выберите приложение. Откроется страница обзора приложения.
4. На панели обзора приложения в разделе **управление** щелкните **свойства**. **Свойства** появится страница.
5. Если вы хотите изменить имя, выберите **имя** поле, введите новое имя и нажмите клавишу **ввод**.
6. Если вы хотите изменить логотип, найти **логотип** и выберите значок папки рядом с полем **выберите файл** поле, которое меньше текущего изображения эмблемы приложения.

   ![Выбор команды "Свойства"](./media/change-name-or-logo-portal/change-logo.png)

   В противном случае если не заменить эмблему, перейдите к шагу 8.
7. В средстве выбора файлов выберите нужный файл нового логотипа. Имя файла отображается в поле ниже текущего изображения эмблемы.

   > [!NOTE]
   > Azure требует изображение логотипа, чтобы быть PNG-файл, и он применяет ограничения на ширину, высоту и размер файла.
8. Щелкните **Сохранить**. Если вы выбрали нового логотипа, **логотип** поля изображения изменяется, отражая новый файл логотипа.

## <a name="next-steps"></a>Дальнейшие действия

* [Краткое руководство Просмотр групп и членов вашей организации в Azure Active Directory](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Назначение корпоративному приложению пользователя или группы](assign-user-or-group-access-portal.md)
* [Удаление назначения пользователя или группы из корпоративного приложения](remove-user-or-group-access-portal.md)
* [Отключение входа пользователя в корпоративное приложение](disable-user-sign-in-portal.md)
