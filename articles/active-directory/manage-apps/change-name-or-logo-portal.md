---
title: Изменение имени или логотипа корпоративного приложения в Azure AD
description: Изменение имени или логотипа настраиваемого корпоративного приложения в Azure Active Directory
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
ms.openlocfilehash: d3dfe0f8788275dd4403b6c9cad99a8eb09e479b
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274720"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>Изменение имени или логотипа корпоративного приложения в Azure Active Directory

Вы можете легко изменить имя или логотип настраиваемого корпоративного приложения в Azure Active Directory (Azure AD). Необходимо иметь соответствующие разрешения для внесения этих изменений, и вы должны быть создателем пользовательского приложения.

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>Разделы справки изменить имя или логотип корпоративного приложения?

1. Войдите на [портал Azure Active Directory](https://aad.portal.azure.com/) с помощью учетной записи глобального администратора каталога. Откроется страница **центра администрирования Azure Active Directory** .
2. В области слева выберите **Корпоративные приложения**. Откроется список корпоративных приложений.
3. Выберите приложение. Откроется страница Обзор приложения.
4. В области Обзор приложения под заголовком **Управление** выберите **свойства**. Откроется страница **Свойства** .
5. Если вы хотите изменить имя, выберите поле **имя** , введите новое имя и нажмите клавишу **Ввод**.
6. Если вы хотите изменить эмблему, найдите поле **Logo** и щелкните значок папки рядом с полем **выберите файл** , которое находится под текущим изображением логотипа приложения.

   ![Выбор команды "Свойства"](./media/change-name-or-logo-portal/change-logo.png)

   В противном случае, если вы не изменяете эмблему, перейдите к шагу 8.
7. В средстве выбора файлов выберите нужный файл в качестве нового логотипа. Имя файла отображается в поле под текущим изображением логотипа.

   > [!NOTE]
   > Для Azure требуется, чтобы изображение логотипа было PNG-файлом и применялись ограничения ширины, высоты и размера файла.
8. Щелкните **Сохранить**. Если вы выбрали новый логотип, изображение поля **логотипа** изменится в соответствии с новым файлом логотипа.

## <a name="next-steps"></a>Дополнительная информация

* [Краткое руководство. Просмотр групп и участников Организации в Azure Active Directory](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Назначение корпоративному приложению пользователя или группы](assign-user-or-group-access-portal.md)
* [Удаление назначения пользователя или группы из корпоративного приложения](remove-user-or-group-access-portal.md)
* [Отключение входа пользователя в корпоративное приложение](disable-user-sign-in-portal.md)
