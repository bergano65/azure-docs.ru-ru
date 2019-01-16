---
title: Лицензия самостоятельного сброса пароля в Azure Active Directory
description: Требования к лицензированию самостоятельного сброса пароля в Azure AD
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 6da0bddc3f6c90d0ecd3a554988f510e1063caac
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54043045"
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
   * Данная функция включена в такие выпуски Azure AD, как "Базовый", "Премиум P1" и "Премиум P2".

* Самостоятельный сброс, изменение или разблокировка пароля **при помощи локальной обратной записи**
   * Я использую **гибридную** локальную учетную запись Active Directory, которая с помощью Azure AD Connect синхронизируется с моей учетной записью Azure AD. Я хочу изменить свой пароль, забыл свой пароль, или он был заблокирован.
      * Я хочу изменить свой пароль или сбросить его, или разблокировать учетную запись **и** синхронизировать эти изменения с локальной службой Active Directory.
   * Данная функция включена в такие выпуски Azure AD, как "Премиум P1" и "Премиум P2".

> [!WARNING]
> Автономные планы лицензирования Office 365 *не поддерживают функцию Self-Service Password Reset/Change/Unlock with on-premises writeback* (Самостоятельный сброс пароля / изменение / разблокировка при помощи локальной обратной записи). Для работы этой функции необходим план с выпусками Azure AD Premium P1 или Premium P2.
>

Дополнительные сведения о лицензировании, включая расходы, можно найти на следующих страницах:

* [Сайт с ценами на Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
* [Функции и возможности Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security).
* [Microsoft 365 корпоративный](https://www.microsoft.com/microsoft-365/enterprise).

## <a name="enable-group-or-user-based-licensing"></a>Включение группового и пользовательского лицензирования

Azure AD теперь поддерживает групповое лицензирование. Это позволяет администраторам назначать лицензии сразу группе пользователей, вместо назначения их по одной. Дополнительные сведения см. в разделе [Назначение лицензий группе пользователей в Azure Active Directory](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Некоторые службы Майкрософт недоступны во всех расположениях. Прежде чем назначать лицензию, администратор должен указать для пользователя свойство **Место использования**. Назначить лицензию можно на портале Azure в разделе **Пользователь** > **Профиль** > **Параметры**. *Если лицензии назначаются группам, все пользователи, для которых не указано расположение, наследуют расположение каталога.*

## <a name="next-steps"></a>Дополнительная информация

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
