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
ms.date: 04/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a62c4d56fbfca34ff6291863149b078f7ddc6680
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288596"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Параметры входа для учетных записей Майкрософт в Azure Active Directory

Страница входа в систему Microsoft 365 для Azure Active Directory (Azure AD) поддерживает один или оба рабочих или учебных учетных записей и учетных записей Майкрософт, в зависимости от ситуации, для поддержки:

* Приложения, которые принимают вход из обоих типов учетной записи
* Организаций, принимающих гостей

## <a name="identification"></a>Идентификация
Чтобы узнать, поддерживает ли страницы входа, которые ваша организация использует учетные записи Майкрософт, просмотрев текст подсказки в поле имени пользователя. Если текст подсказки говорит: «Адрес электронной почты, телефона или Скайп» страницы входа поддерживает учетные записи Майкрософт.

![Разница между страниц входа учетной записи](./media/signin-account-support/ui-prompt.png)

[Дополнительные параметры подходят только для личных учетных записей Майкрософт](index.yml) , но не может использоваться для входа для рабочей или учебной учетной записи ресурсов.

## <a name="next-steps"></a>Дальнейшие действия

[Настройка вашего входа в систему фирменной символики](../fundamentals/add-custom-domain.md)