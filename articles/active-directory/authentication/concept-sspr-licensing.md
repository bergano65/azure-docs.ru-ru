---
title: 'Azure Active Directory: лицензирование самостоятельного сброса пароля'
description: Требования к лицензированию самостоятельного сброса пароля в Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e4e32aef97d406fa47a2ccfe8cddb12b97ff088
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60414993"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Требования к лицензированию самостоятельного сброса пароля в Azure AD

Azure Active Directory (Azure AD) предлагается в четырех выпусках: Free, Basic, Premium P1 и Premium P2. Существует несколько различных функций, которые включают в себя самостоятельный сброс пароля. К ним можно отнести изменение, сброс, разблокировку и обратную запись, которые доступны в разных выпусках Azure AD. В статье описывается разница между выпусками. Дополнительные сведения о функциях, предоставляемых в каждом из выпусков Azure AD, можно найти на странице [Цены на Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Сравнение выпусков и функций

Самостоятельный сброс пароля в Azure AD лицензируется для каждого пользователя. Это позволяет отвечать требованиям организаций по назначению соответствующих лицензий для пользователей.

* Самостоятельное изменение паролей для пользователей облака
   * Я являюсь **пользователем облака** и знаю свой пароль.
      * Я хочу **изменить** пароль.
   * Данная функция включена во все выпуски Azure AD.

* Самостоятельный сброс паролей для пользователей облака
   * Я являюсь **пользователем облака** и забыл свой пароль.
      * Я хочу **сбросить** пароль.
   * Эта функция включена в такие выпуски Azure AD Basic, Azure AD Premium P1 или P2 и Microsoft 365 бизнес.

* Самостоятельный сброс, изменение или разблокировка пароля **при помощи локальной обратной записи**
   * Я использую **гибридную** локальную учетную запись Active Directory, которая с помощью Azure AD Connect синхронизируется с моей учетной записью Azure AD. Я хочу изменить свой пароль, забыл свой пароль, или он был заблокирован.
      * Я хочу изменить свой пароль или сбросить его, или разблокировать учетную запись **и** синхронизировать эти изменения с локальной службой Active Directory.
   * Эта функция включена в такие выпуски Azure AD Premium P1 или P2 и Microsoft 365 бизнес.

> [!WARNING]
> Автономные планы лицензирования Office 365 *не поддерживают самостоятельный сброс, изменение и разблокировку пароля с помощью обратная записи в локальной среде*. Для работы этой функции требуется план с выпусками Azure AD Premium P1, Premium P2 или Microsoft 365 бизнес.
>

Дополнительные сведения о лицензировании, включая расходы, можно найти на следующих страницах:

* [Сайт с ценами на Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
* [Функции и возможности Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security).
* [Microsoft 365 корпоративный](https://www.microsoft.com/microsoft-365/enterprise).
* [Описание службы Microsoft 365 бизнес](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Включение группового и пользовательского лицензирования

Azure AD теперь поддерживает групповое лицензирование. Это позволяет администраторам назначать лицензии сразу группе пользователей, вместо назначения их по одной. Дополнительные сведения см. в разделе [Назначение лицензий группе пользователей в Azure Active Directory](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Некоторые службы Майкрософт недоступны во всех расположениях. Прежде чем назначать лицензию, администратор должен указать для пользователя свойство **Место использования**. Назначить лицензию можно на портале Azure в разделе **Пользователь** > **Профиль** > **Параметры**. *Если лицензии назначаются группам, все пользователи, для которых не указано расположение, наследуют расположение каталога.*

## <a name="next-steps"></a>Дальнейшие действия

* [Как развернуть самостоятельный сброс пароля?](howto-sspr-deployment.md)
* [Сброс или изменение пароля](../user-help/active-directory-passwords-update-your-own-password.md)
* [Регистрация для самостоятельного сброса пароля](../user-help/active-directory-passwords-reset-register.md)
* [Какие данные используются для SSPR и какие сведения нужно указывать для пользователей](howto-sspr-authenticationdata.md)
* [Доступные пользователям методы проверки подлинности](concept-sspr-howitworks.md#authentication-methods)
* [Параметры политики для SSPR](concept-sspr-policy.md)
* [Что такое обратная запись паролей и каково ее назначение](howto-sspr-writeback.md)
* [Как сообщать о действиях в SSPR](howto-sspr-reporting.md)
* [Обзор всех параметров SSPR и их значение](concept-sspr-howitworks.md)
* [Как устранить неполадки самостоятельного сброса пароля](active-directory-passwords-troubleshoot.md)
* [Вопросы, не вошедшие в другие статьи](active-directory-passwords-faq.md)
