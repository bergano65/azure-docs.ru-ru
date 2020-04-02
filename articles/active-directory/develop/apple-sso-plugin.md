---
title: Подключаемый модуль Microsoft Enterprise SSO для устройств Apple
titleSuffix: Microsoft identity platform | Azure
description: Узнайте о s-подключаемом ключе SSO от Microsoft Azure Active Directory для устройств iOS и macOS.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 7233a0bba5cf30eab018bfa744a7322303300604
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550322"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Подключаемый модуль Microsoft Enterprise SSO для устройств Apple (Предварительный просмотр)

> [!NOTE]
> Эта функция предоставляется в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

*Плагин Microsoft Enterprise SSO для устройств Apple* обеспечивает единый входе (SSO) для учетных записей Active Directory во всех приложениях, поддерживающих функцию [корпоративного входного ввоза](https://developer.apple.com/documentation/authenticationservices) От Apple. Корпорация Майкрософт тесно сотрудничала с Apple в разработке этого плагина для повышения удобства использования приложения, обеспечивая при этом лучшую защиту, которую могут предоставить Apple и Microsoft.

В этом публичном выпуске предварительного просмотра плагин Enterprise SSO доступен только для устройств iOS и распространяется в некоторых приложениях Майкрософт.

Наше первое использование плагина Enterprise SSO с нашей новой общей функцией [режима устройства.](msal-ios-shared-devices.md)

## <a name="features"></a>Компоненты

Плагин Microsoft Enterprise SSO для устройств Apple предлагает следующие преимущества:

- Предоставляет SSO для учетных записей Active Directory во всех приложениях, поддерживающих функцию корпоративного единого знака от Apple.
- Поставляется автоматически в Microsoft Authenticator и может быть включено любым решением для управления мобильными устройствами (MDM).

## <a name="requirements"></a>Требования

Для использования плагина Microsoft Enterprise SSO для устройств Apple:

- iOS 13.0 или выше должна быть установлена на устройстве.
- На устройстве должно быть установлено приложение Microsoft, предоставляющее плагин Microsoft Enterprise SSO для устройств Apple. Для публичного предварительного просмотра, эти приложения включают [приложение Microsoft Authenticator](../user-help/user-help-auth-app-overview.md).
- Устройство должно быть зарегистрировано mdM (например, с Microsoft Intune).
- Конфигурация должна быть перенесена на устройство, чтобы включить плагин Microsoft Enterprise SSO для устройств Apple на устройстве. Это ограничение безопасности требуется Apple.

## <a name="enable-the-sso-extension-with-mobile-device-management-mdm"></a>Включить расширение SSO с помощью управления мобильными устройствами (MDM)

Чтобы включить плагин Microsoft Enterprise SSO для устройств Apple, ваши устройства должны быть отправлены сигнал через службу MDM. Поскольку Microsoft включает плагин Enterprise SSO в [приложении Microsoft Authenticator,](..//user-help/user-help-auth-app-overview.md)используйте MDM для настройки приложения для включения плагина Microsoft Enterprise SSO.

Используйте следующие параметры для настройки плагина Microsoft Enterprise SSO для устройств Apple:

- **Тип**: Перенаправление
- **Идентификатор расширения**:`com.microsoft.azureauthenticator.ssoextension`
- **Идентификатор команды**:`SGGM6D27TK`
- **URL-адреса**:
  - `https://login.microsoftonline.com`
  - `https://login.windows.net`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`

Вы можете использовать Microsoft Intune в качестве службы MDM для облегчения конфигурации плагина Microsoft Enterprise SSO. Для получения дополнительной информации ознакомьтесь с [документацией конфигурации Intune.](https://docs.microsoft.com/intune/configuration/ios-device-features-settings)

## <a name="using-the-sso-extension-in-your-application"></a>Использование расширения SSO в приложении

[Библиотека аутентификации Microsoft (MSAL) для устройств Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) версии 1.1.0 и выше поддерживает плагин Microsoft Enterprise SSO для устройств Apple.

Если вы хотите поддерживать режим общего устройства, предоставляемый плагином Microsoft Enterprise SSO для устройств Apple, убедитесь, что ваши приложения используют указанную минимально требуемую версию MSAL.

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации о режиме совместного устройства на iOS [см.](msal-ios-shared-devices.md)
