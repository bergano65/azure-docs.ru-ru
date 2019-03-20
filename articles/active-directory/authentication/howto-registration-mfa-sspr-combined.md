---
title: Начало работы с объединенный регистрации для Azure AD SSPR и многофакторной проверки Подлинности (Предварительная версия)
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
ms.openlocfilehash: 2a6896e2b9633b8de679e8d14a7957dc0e3229e7
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226731"
---
# <a name="enable-combined-security-information-registration-preview"></a>Включение в сочетании безопасности сведения о регистрации (Предварительная версия)

Прежде чем включать новые возможности, ознакомьтесь со статьей [регистрации сведения безопасности (Предварительная версия) в сочетании](concept-registration-mfa-sspr-combined.md) для обеспечения того, вы понимаете влияние этой функции и функциональные возможности.

![Объединенные сведения регистрации усиленной опыта в области безопасности запрашивает Дополнительные сведения при входе в систему. Регистрация приложения Microsoft Authenticator в качестве первого метода показано в примере.](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Объединенный безопасности сведения о регистрации для многофакторной идентификации Azure и Azure AD самостоятельного сброса пароля — это функция предварительной версии из Azure Active Directory. См. дополнительные сведения о [дополнительных условиях использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="enable-combined-registration"></a>Включение объединенной регистрации

Выполните следующие действия, чтобы включить объединенный регистрации.

1. Войдите на портал Azure как глобальный администратор или администратор пользователей.
2. Перейдите в раздел **Azure Active Directory** > **Параметры пользователя** > **Управление параметрами для функций предварительной версии панели доступа**.
3. В разделе **пользователи могут использовать функции для регистрации и управления ими сведения о безопасности предварительной версии — обновление**, можно включить для **выбранные** группы пользователей или для **все** пользователей.

![Включить предварительную версию info объединенный безопасности для всех пользователей на портале Azure AD](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> Начиная с марта 2019 г параметры телефонного звонка будет недоступен для пользователей многофакторной проверки Подлинности и SSPR в клиентах бесплатного или пробного Azure AD. Это изменение не затрагивает SMS-сообщения. Телефонный звонок будет продолжать будет доступен пользователям в платных клиентов Azure AD. Это изменение влияет только на клиентов бесплатного или пробного Azure AD.

> [!NOTE]
> Один раз включить объединенный регистрации пользователей, которые регистрируют и убедитесь, что их номер телефона или мобильного приложения через новый интерфейс можно использовать их для многофакторной проверки Подлинности и SSPR, если эти методы включены в политиках многофакторной проверки Подлинности и SSPR. Если вы отключите этот опыт, пользователи, перейти к предыдущей регистрации SSPR странице в `https:/aka.ms/ssprsetup` нужно будет выполнить многофакторную проверку подлинности при доступе к странице.

Если вы настроили веб-сайте, список назначений зоны для в Internet Explorer следующие узлы должны находиться в той же зоне:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="next-steps"></a>Дальнейшие действия

[Доступные методы для многофакторной проверки Подлинности и SSPR](concept-authentication-methods.md)

[Настройка самостоятельного сброса пароля](howto-sspr-deployment.md)

[Настройка многофакторной идентификации Azure](howto-mfa-getstarted.md)

[Устранение неполадок в сочетании регистрации сведений о безопасности](howto-registration-mfa-sspr-combined-troubleshoot.md)