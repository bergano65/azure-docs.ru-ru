---
title: Принимает ли моя страница входа Azure AD учетные записи Майкрософт | Документация Майкрософт
description: Как при входе в систему отражается Поиск имени пользователя во время входа
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 221ab7c50a84650f1b2adf3fdb2b284365795f42
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024281"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Параметры входа для учетных записей Майкрософт в Azure Active Directory

Страница входа Microsoft 365 для Azure Active Directory (Azure AD) поддерживает рабочие или учебные учетные записи и учетные записи Майкрософт, но в зависимости от ситуации пользователя это может быть один или оба. Например, страница входа в Azure AD поддерживает:

* Приложения, принимающие операции входа из обоих типов учетных записей
* Организации, которые принимают гостей

## <a name="identification"></a>Идентификация
Чтобы определить, поддерживает ли страница входа, используемая организацией, учетные записи Майкрософт, просмотрите текст подсказки в поле Username (имя пользователя). Если в тексте подсказки указано "E-mail, Phone или Skype", то страница входа поддерживает учетные записи Майкрософт.

![Различие между страницами входа учетной записи](./media/signin-account-support/ui-prompt.png)

[Дополнительные параметры входа работают только с личными учетными записями Майкрософт](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) , но не могут использоваться для входа в ресурсы рабочей или учебной учетной записи.

## <a name="next-steps"></a>Дополнительная информация

[Настройка фирменной символики для входа](../fundamentals/add-custom-domain.md)