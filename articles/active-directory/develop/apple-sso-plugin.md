---
title: Подключаемый модуль единого входа Microsoft Enterprise для устройств Apple
titleSuffix: Microsoft identity platform | Azure
description: Сведения о подключаемом модуле единого входа Microsoft Azure Active Directory для устройств iOS и macOS.
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
ms.openlocfilehash: a6af1a78582ebf62aed82bad8fa06cac37f511ea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80982588"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Подключаемый модуль единого входа Microsoft Enterprise для устройств Apple (Предварительная версия)

> [!NOTE]
> Эта функция предоставляется в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

*Подключаемый модуль единого входа Microsoft Enterprise для устройств Apple* обеспечивает единый вход (SSO) для учетных записей Azure Active Directory (Azure AD) во всех приложениях, поддерживающих функцию [единого входа Apple Enterprise](https://developer.apple.com/documentation/authenticationservices) . Корпорация Майкрософт тесно работала с компанией Apple для разработки этого подключаемого модуля, чтобы повысить удобство использования приложения и обеспечить лучшую защиту, которую могут предоставить компании Apple и Майкрософт.

В этой общедоступной предварительной версии подключаемый модуль единого входа предприятия доступен только для устройств iOS и распространяется в некоторых приложениях Майкрософт.

Первое использование подключаемого модуля единого входа предприятия — это новая функция [режима общего устройства](msal-ios-shared-devices.md) .

## <a name="features"></a>Функции

Подключаемый модуль единого входа Microsoft Enterprise для устройств Apple предоставляет следующие преимущества.

- Предоставляет единый вход для учетных записей Azure AD во всех приложениях, поддерживающих функцию единого входа Apple Enterprise.
- Автоматически доставляется в Microsoft Authenticator и может быть включено любым решением управления мобильными устройствами (MDM).

## <a name="requirements"></a>Requirements (Требования)

Чтобы использовать подключаемый модуль единого входа Microsoft Enterprise для устройств Apple, выполните следующие действия.

- на устройстве должен быть установлен iOS 13,0 или более поздней версии.
- На устройстве должно быть установлено приложение Майкрософт, которое предоставляет подключаемый модуль единого входа Microsoft Enterprise для устройств Apple. Для общедоступной предварительной версии эти приложения включают [Microsoft Authenticator приложение](../user-help/user-help-auth-app-overview.md).
- Устройство должно быть зарегистрировано в MDM (например, с Microsoft Intune).
- Чтобы включить подключаемый модуль единого входа Microsoft Enterprise для устройств Apple на устройстве, необходимо принудительно отправить конфигурацию на устройство. Это ограничение безопасности является обязательным для Apple.

## <a name="enable-the-sso-extension-with-mobile-device-management-mdm"></a>Включение расширения единого входа с помощью управления мобильными устройствами (MDM)

Чтобы включить подключаемый модуль единого входа Microsoft Enterprise для устройств Apple, устройства должны отправлять сигнал через службу MDM. Так как корпорация Майкрософт включает подключаемый модуль единого входа предприятия в [Microsoft Authenticator приложении](..//user-help/user-help-auth-app-overview.md), используйте MDM, чтобы настроить приложение для включения подключаемого модуля единого входа Microsoft Enterprise.

Используйте следующие параметры для настройки подключаемого модуля единого входа Microsoft Enterprise для устройств Apple.

- **Тип**: перенаправление
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

Microsoft Intune в качестве службы MDM можно использовать для упрощения настройки подключаемого модуля единого входа Microsoft Enterprise. Дополнительные сведения см. в [документации по конфигурации Intune](https://docs.microsoft.com/intune/configuration/ios-device-features-settings).

## <a name="using-the-sso-extension-in-your-application"></a>Использование расширения SSO в приложении

[Библиотека проверки подлинности Microsoft (MSAL) для устройств Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) версии 1.1.0 и выше поддерживает подключаемый модуль единого входа Microsoft Enterprise для устройств Apple.

Если вы хотите поддерживать режим общего устройства, предоставляемый подключаемым модулем Microsoft Enterprise SSO для устройств Apple, убедитесь, что приложения используют указанную минимальную требуемую версию MSAL.

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о режиме общего устройства в iOS см. в статье [режим общего устройства для устройств iOS](msal-ios-shared-devices.md).
