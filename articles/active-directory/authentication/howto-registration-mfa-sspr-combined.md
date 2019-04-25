---
title: Начало работы с объединенный регистрации для Azure AD SSPR и многофакторной проверки подлинности (Предварительная версия) — Azure Active Directory
description: Включение в сочетании многофакторную проверку подлинности Azure AD и самостоятельный сброс пароля регистрации (Предварительная версия)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3baf2690ae07b87bb4d5dba30fcd20f62a1a4506
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60358095"
---
# <a name="enable-combined-security-information-registration-preview"></a>Включение в сочетании безопасности сведения о регистрации (Предварительная версия)

Прежде чем включать новые возможности, ознакомьтесь со статьей [регистрации сведения безопасности (Предварительная версия) в сочетании](concept-registration-mfa-sspr-combined.md) для обеспечения того, вы понимаете последствия этой функции и функциональные возможности.

![Объединенный безопасности сведения о регистрации расширенный интерфейс](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Объединенный безопасности сведения о регистрации для многофакторной идентификации Azure и Azure Active Directory (Azure AD) самостоятельного сброса пароля — это функция предварительной версии Azure AD. См. дополнительные сведения о [дополнительных условиях использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="enable-combined-registration"></a>Включение объединенной регистрации

Выполните следующие шаги для включения в объединенный регистрации.

1. Войдите на портал Azure как глобальный администратор или администратор пользователей.
2. Перейдите к **Azure Active Directory** > **параметры пользователя** > **Управление параметрами для компонентов предварительной версии панель доступа**.
3. В разделе **пользователи могут использовать функции для регистрации и управления ими сведения о безопасности предварительной версии — обновление**, можно включить для **выбранные** группы пользователей или для **все** пользователей.

   ![Включить предварительную версию info объединенный безопасности для всех пользователей](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> Начиная с марта 2019 г параметры телефонного звонка не будут доступны для многофакторной проверки подлинности и пользователей SSPR в клиентах бесплатного или пробного Azure AD. Это изменение не затрагивает SMS-сообщения. Параметры телефонного звонка по-прежнему будут доступны пользователям в платных клиентов Azure AD.

> [!NOTE]
> После включения объединенного регистрации, пользователи, которые регистрируют и подтвердить свой номер телефона или мобильного приложения через новый интерфейс их можно использовать для многофакторной проверки подлинности и SSPR, если эти методы включены в многофакторной проверки подлинности и SSPR политики. Если вы отключите этот опыт, пользователи, перейти к предыдущей регистрации SSPR странице в `https:/aka.ms/ssprsetup` нужно будет выполнить многофакторную проверку подлинности при доступе к странице.

Если вы настроили веб-сайте список назначений зоны для в Internet Explorer, следующие сайты должны быть в той же зоне:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="next-steps"></a>Дальнейшие действия

[Доступные методы для многофакторной проверки подлинности и SSPR](concept-authentication-methods.md)

[Настройка самостоятельного сброса пароля](howto-sspr-deployment.md)

[Настройка многофакторной идентификации Azure](howto-mfa-getstarted.md)

[Устранение неполадок в сочетании регистрации сведений о безопасности](howto-registration-mfa-sspr-combined-troubleshoot.md)