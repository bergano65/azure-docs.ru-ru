---
title: Принимает ли моя страница регистрации Azure AD учетные записи Майкрософт Документы Майкрософт
description: Как на экране сообщения отражают поиск имени пользователя во время регистрации
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024281"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Параметры входиных учетных записей Майкрософт в Active Directory Azure

Страница microsoft 365 для каталога Активных Azure (Azure AD) поддерживает рабочие или школьные учетные записи и учетные записи Майкрософт, но в зависимости от ситуации пользователя это может быть один или другой или и то, и другое. Например, страница регистрации Azure AD поддерживает:

* Приложения, принимающие ввоза из обоих типов учетных записей
* Организации, принимающие гостей

## <a name="identification"></a>Идентификация
Вы можете сказать, поддерживает ли страница входной системы, которую использует ваша организация, учетные записи Майкрософт, посмотрев текст подсказок в поле имени пользователя. Если в тексте подсказки говорится: "Электронная почта, телефон или Skype", страница ввне поддерживает учетные записи Майкрософт.

![Разница между страницами входного ввески учетной записи](./media/signin-account-support/ui-prompt.png)

[Дополнительные параметры входной связи работают только для личных учетных записей Майкрософт,](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) но не могут быть использованы для ввоза на работу или ресурсы школьных учетных записей.

## <a name="next-steps"></a>Дальнейшие действия

[Настройте свой брендинг для входини](../fundamentals/add-custom-domain.md)