---
title: Azure Active Directory вход без пароля (Предварительная версия)
description: Вход без пароля в Azure AD с помощью ключей безопасности FIDO2 или приложения Microsoft Authenticator (Предварительная версия)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28d4dd3f0d4432930d62bb499fe72533b79d2a08
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848737"
---
# <a name="passwordless-authentication-options"></a>Параметры проверки подлинности, не имеющие пароля

Многофакторная идентификация (MFA) — это отличный способ защитить организацию, но пользователи прибегают к работе с дополнительным уровнем поверх необходимости запоминать пароли. Методы проверки подлинности без пароля более удобны, так как пароль удаляется и заменяется чем-то, что вы или что вы понимаете.

|   | Что-то у вас | Что-то, что вы или вы знакомы |
| --- | --- | --- |
| Вход без пароля | Устройство, Телефон или ключ безопасности Windows 10 | Биометрия или ПИН-код |

При проверке подлинности у каждой организации есть свои потребности. Корпорация Майкрософт предлагает три варианта проверки подлинности с паролем:

- Windows Hello для бизнеса
- Приложение Microsoft Authenticator
- Ключи безопасности FIDO2

![Проверка подлинности: безопасность и удобство](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello для бизнеса

Windows Hello для бизнеса идеально подходит для информационных работников, имеющих свой назначенный компьютер под управлением Windows. Биометрические метрики и ПИН-код напрямую привязаны к компьютеру пользователя, что предотвращает доступ других пользователей, кроме владельца. Благодаря интеграции PKI и встроенной поддержке единого входа (SSO) Windows Hello для бизнеса предоставляет простой и удобный способ легкого доступа к корпоративным ресурсам в локальной среде и в облаке.

С помощью этого [средства](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) можно принять решение о типе развертывания Windows Hello для бизнеса и возможностях, которые необходимо учитывать.

## <a name="microsoft-authenticator-app"></a>Приложение Microsoft Authenticator

Разрешить телефону сотрудника использовать метод проверки подлинности, не имеющий пароля. Возможно, вы уже используете приложение Microsoft Authenticator в качестве удобного параметра многофакторной проверки подлинности в дополнение к паролю. Но теперь он доступен в качестве параметра без пароля.

![Войдите в Microsoft ребр с помощью приложения Microsoft Authenticator](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

Она превращает любой телефон iOS или Android в надежные учетные данные без пароля, позволяя пользователям входить на любую платформу или браузер, получая уведомление на свой телефон, сопоставляя номер, отображаемый на экране, на телефоне, а затем используя биометрическую метрику ( касание или лицо) или ПИН-код для подтверждения.

## <a name="fido2-security-keys"></a>Ключи безопасности FIDO2

Ключи безопасности FIDO2 — это метод проверки подлинности без фишинга на основе паролей, который может приходиться на любой форм-фактор. Быстрое удостоверение Online (FIDO) — это открытый стандарт для проверки подлинности с паролем. Она позволяет пользователям и организациям использовать стандарт для входа в свои ресурсы без имени пользователя или пароля, используя внешний ключ безопасности или ключ платформы, встроенный в устройство.

Для общедоступной предварительной версии сотрудники могут использовать ключи безопасности для входа на устройства Windows 10, присоединенные к Azure AD, и единого входа в облачные и локальные ресурсы. Они также могут входить в Поддерживаемые браузеры.

![Вход в Microsoft ребр с помощью ключа безопасности](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

Несмотря на множество ключей, которые FIDO2ся сертификатом FIDO Alliance, корпорация Майкрософт требует некоторых дополнительных расширений спецификации FIDO2 для протокола Client-Authenticator (CTAP), которые должны быть реализованы поставщиком, чтобы обеспечить максимальную безопасность и лучшее удобной.

Для обеспечения совместимости с Майкрософт ключ безопасности **должен** реализовывать следующие функции и расширения из протокола FIDO2 CTAP:

| # | Доверие к компонентам и расширениям | Почему требуется эта функция или расширение? |
| --- | --- | --- |
| 1 | Резидентный ключ | Эта функция обеспечивает переносимость ключа безопасности, где учетные данные хранятся в ключе безопасности. |
| 2 | ПИН-код клиента | Эта функция позволяет защитить учетные данные с помощью второго фактора и применять к ключам безопасности, не имеющим пользовательского интерфейса. |
| 3 | HMAC-Secret | Это расширение обеспечивает возможность входа на устройство, если оно отключено или находится в режиме "в самолете". |
| 4 | Несколько учетных записей на RP | Эта функция позволяет использовать один и тот же ключ безопасности в нескольких службах, таких как учетная запись Майкрософт и Azure Active Directory. |

Следующие поставщики предлагают FIDO2 ключи безопасности различных форм-факторов, которые известны как совместимые с паролем. Корпорация Майкрософт рекомендует клиентам оценивать свойства безопасности этих ключей, обратившись к поставщику, а также к FIDO Alliance.

| Поставщик | Связь |
| --- | --- |
| юбико | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| феитиан | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| енсурити | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| евбм | [https://www.ewbm.com/support](https://www.ewbm.com/support) |
| аусентренд | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |

> [!NOTE]
> При покупке и планировании использования ключей безопасности на основе NFC вам потребуется поддерживаемый модуль чтения NFC.

Если вы являетесь поставщиком и хотите получить устройство в этом списке, обратитесь в службу [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com).

Ключи безопасности FIDO2 являются отличным вариантом для предприятий, которые имеют очень высокий уровень безопасности или имеют сценарии или сотрудники, которые не могут использовать свой телефон в качестве второго фактора.

## <a name="what-scenarios-work-with-the-preview"></a>Какие сценарии работают с предварительной версией?

- Администраторы могут включить методы проверки подлинности, не имеющие пароля, для своего клиента
- Администраторы могут ориентироваться на всех пользователей или выбирать пользователей или группы в своем клиенте для каждого метода.
- Конечные пользователи могут регистрировать эти методы проверки подлинности, не имеющие пароля, на портале учетных записей и управлять ими
- Конечные пользователи могут выполнять вход с использованием этих методов проверки подлинности без пароля
   - Microsoft Authenticator приложение: будет работать в сценариях, где используется проверка подлинности Azure AD, включая все браузеры, во время установки Windows 10 из Box (OOBE) и интегрированные мобильные приложения в любой операционной системе.
   - Ключи безопасности: будут работать на экране блокировки для Windows 10 и в поддерживаемых браузерах, таких как Microsoft ребро.

## <a name="next-steps"></a>Дальнейшие действия

[Включение параметров пассвордлессс ключа безопасности FIDO2 в Организации](howto-authentication-passwordless-security-key.md)

[Включить в организации параметры, поддерживающие пароль на телефоне](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Внешние ссылки

[FIDO Alliance](https://fidoalliance.org/)

[Спецификация FIDO2 CTAP](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
