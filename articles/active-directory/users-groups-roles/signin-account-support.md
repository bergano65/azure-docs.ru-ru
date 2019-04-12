---
title: Как узнать, если страницы входа Azure AD принимает учетные записи Майкрософт | Документация Майкрософт
description: Как на экране обмена сообщениями отражает Уточняющий запрос имени пользователя при входе в систему
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/10/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d26ff0f9259e3531259673f94fe477444cc786b
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59491601"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Параметры входа для учетных записей Майкрософт в Azure Active Directory

Страница входа в систему Microsoft 365 для Azure Active Directory (Azure AD) поддерживает рабочих или учебных учетных записей и учетных записей Майкрософт, но в зависимости от ситуации пользователя, это может быть один или другой или оба. Например страницы входа Azure AD поддерживает:

* Приложения, которые принимают вход из обоих типов учетной записи
* Организаций, принимающих гостей

## <a name="identification"></a>Идентификация
Чтобы узнать, поддерживает ли страницы входа, которые ваша организация использует учетные записи Майкрософт, просмотрев текст подсказки в поле имени пользователя. Если текст подсказки говорит: «Адрес электронной почты, телефона или Скайп» страницы входа поддерживает учетные записи Майкрософт.

![Разница между страниц входа учетной записи](./media/signin-account-support/ui-prompt.png)

[Дополнительные параметры подходят только для личных учетных записей Майкрософт](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) , но не может использоваться для входа для рабочей или учебной учетной записи ресурсов.

## <a name="next-steps"></a>Дальнейшие действия

[Настройка вашего входа в систему фирменной символики](../fundamentals/add-custom-domain.md)