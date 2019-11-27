---
title: Учетные записи платформы Microsoft Identity и профили клиентов (Android) | Службы
description: Обзор учетных записей платформы удостоверений Майкрософт для Android
services: active-directory
documentationcenter: ''
author: shoatman
manager: nadima
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7beab6759524037f86c83429644c1bb1fffe4d07
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679845"
---
# <a name="accounts--tenant-profiles-android"></a>Учетные записи и профили клиентов (Android)

В этой статье приводятся общие сведения о `account` на платформе Microsoft Identity.

API библиотеки проверки подлинности Майкрософт (MSAL) заменяет термин *User* на термин *учетная запись*. Одна из причин заключается в том, что пользователь (человек или агент программного обеспечения) может иметь или может использовать несколько учетных записей. Эти учетные записи могут находиться в собственной организации пользователя и (или) в других организациях, членом которых является пользователь.

Учетная запись платформы Microsoft Identity состоит из следующих компонентов:

  - Уникальный идентификатор.
  - Один или несколько учетных данных, используемых для демонстрации владения и контроля учетной записи.
  - Один или несколько профилей, состоящих из атрибутов, например:
    - Изображение, имя, название семейства, название, расположение офиса
- Учетная запись имеет источник полномочий или системы записи. Это система, в которой создается учетная запись и в которой хранятся учетные данные, связанные с этой учетной записью. В системах с несколькими клиентами, таких как платформа Microsoft Identity, система записи — это `tenant`, где была создана учетная запись. Этот клиент также называется `home tenant`.
- Учетные записи на платформе Microsoft Identity имеют следующие системы записи:
  - Azure Active Directory, включая Azure Active Directory B2C.
  - Учетная запись Майкрософт (Live).
- Учетные записи из систем записи за пределами платформы Microsoft Identity представлены на платформе Microsoft Identity Platform, в том числе:
  - удостоверения из подключенных локальных каталогов (Windows Server Active Directory)
  - Внешние удостоверения из LinkedIn, GitHub и т. д.
  В таких случаях у учетной записи есть как исходная система записи, так и система записи на платформе Microsoft Identity.
- Платформа Microsoft Identity позволяет использовать одну учетную запись для доступа к ресурсам, принадлежащим нескольким организациям (Azure Active Directory клиентов).
  - Чтобы записать, что учетная запись из одной системы записи (клиент AAD а) имеет доступ к ресурсу в другой системе записи (клиент AAD б), учетная запись должна быть представлена в клиенте, где определен ресурс. Это делается путем создания локальной записи учетной записи из системы A в системе B.
  - Эта локальная запись, которая является представлением учетной записи, привязана к исходной учетной записи.
  - MSAL предоставляет эту локальную запись в качестве `Tenant Profile`.
  - Профиль клиента может иметь разные атрибуты, соответствующие локальному контексту, такие как должность, расположение офиса, Контактная информация и т. д.
 
- Так как учетная запись может присутствовать в одном или нескольких клиентах, учетная запись может иметь более одного профиля.

> [!NOTE]
> MSAL считает систему учетная запись Майкрософт (Live, MSA) в качестве другого клиента в платформе Microsoft Identity. Идентификатор клиента учетная запись Майкрософт: `9188040d-6c67-4c5b-b112-36a304b66dad`

## <a name="account-overview-diagram"></a>Схема обзора учетной записи

![Схема обзора учетной записи](./media/accounts-overview/accounts-overview.png)

Расшифровка схемы выше.

- Учетная запись `bob@contoso.com` создается в локальной Active Directory Windows Server (исходная локальная система записи).
- `tom@live.com` учетной записи создается в клиенте учетная запись Майкрософт.
- у `bob@contoso.com` есть доступ по крайней мере к одному ресурсу в следующих клиентах Azure Active Directory:
  - contoso.com (облачная система записи, связанная с локальной системой записи)
  - fabrikam.com
  - woodgrovebank.com
  - Профиль клиента для `bob@contoso.com` существует в каждом из этих клиентов.
- `tom@live.com` имеет доступ к ресурсам в следующих клиентах Майкрософт:
  - contoso.com
  - fabrikam.com
  - Профиль клиента для `tom@live.com` существует в каждом из этих клиентов.
- Сведения о Tom и Bob в других клиентах могут отличаться от данных в системе записи. Они могут различаться по атрибутам, таким как должность, расположение офиса и т. д. Они могут быть членами групп и (или) ролей в каждой организации (Azure Active Directory клиенте). Эти сведения относятся к bob@contoso.comному профилю клиента.

На схеме bob@contoso.com и tom@live.com имеют доступ к ресурсам в разных клиентах Azure Active Directory. Дополнительные сведения см. [в разделе добавление Azure Active Directory пользователей службы совместной работы B2B в портал Azure](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

## <a name="accounts-and-single-sign-on-sso"></a>Учетные записи и единый вход (SSO)

Кэш маркеров MSAL хранит *один маркер обновления* для каждой учетной записи. Этот маркер обновления можно использовать для автоматического запроса маркеров доступа из нескольких клиентов платформы удостоверений Майкрософт. Если брокер установлен на устройстве, учетная запись управляется посредником, а единый вход на уровне устройства становится возможным.

> [!IMPORTANT]
> Учетная запись "бизнес-потребитель" (B2C) и поведение маркера обновления отличаются от остальной платформы Microsoft Identity. Дополнительные сведения см. в статье [B2C Policies & Accounts](#b2c-policies--accounts).

## <a name="account-identifiers"></a>Идентификаторы учетной записи

Идентификатор учетной записи MSAL не является ИДЕНТИФИКАТОРом объекта учетной записи. Он не предназначен для анализа и (или) основан на том, чтобы передать все, кроме уникальности в платформе Microsoft Identity.

Чтобы обеспечить совместимость с библиотекой аутентификация Azure AD (ADAL) и упростить миграцию из ADAL в MSAL, MSAL может искать учетные записи, используя любой допустимый идентификатор для учетной записи, доступной в кэше MSAL.  Например, следующий объект учетной записи всегда будет извлекаться для tom@live.com, так как каждый из идентификаторов является допустимым:

```java
// The following would always retrieve the same account object for tom@live.com because each identifier is valid

IAccount account = app.getAccount("<tome@live.com msal account id>");
IAccount account = app.getAccount("<tom@live.com contoso user object id>");
IAccount account = app.getAccount("<tom@live.com woodgrovebank user object id>");
```

## <a name="accessing-claims-about-an-account"></a>Доступ к утверждениям учетной записи

Помимо запроса маркера доступа, MSAL также всегда запрашивает маркер идентификатора каждого клиента. Для этого всегда запрашиваются следующие области:

- OpenId
- profile

Маркер идентификации содержит список утверждений. `Claims` — это пары "имя-значение", связанные с учетной записью, и используются для выполнения запроса.

Как упоминалось ранее, каждый клиент, где существует учетная запись, может хранить разные сведения об учетной записи, в том числе не ограничиваясь такими атрибутами, как должность, расположение офиса и т. д.

Хотя учетная запись может быть членом или гостевой Организацией в нескольких организациях, MSAL не выполняет запрос к службе для получения списка клиентов, членом которых является эта учетная запись. Вместо этого MSAL создает список клиентов, в которых находится учетная запись, в результате запросов маркеров, которые были выполнены.

Утверждения, предоставляемые для объекта Account, всегда являются утверждениями от/{аусорити} "домашний клиент" для учетной записи. Если эта учетная запись не использовалась для запроса маркера для своего домашнего клиента, MSAL не может предоставить утверждения через объект Account.  Пример:

```java
// Psuedo Code
IAccount account = getAccount("accountid");

String username = account.getClaims().get("preferred_username");
String tenantId = account.getClaims().get("tid"); // tenant id
String objectId = account.getClaims().get("oid"); // object id
String issuer = account.getClaims().get("iss"); // The tenant specific authority that issued the id_token
```

> [!TIP]
> Чтобы просмотреть список утверждений, доступных в объекте Account, обратитесь к [утверждениям в id_token](https://docs.microsoft.com/azure/active-directory/develop/id-tokens#claims-in-an-id_token)

> [!TIP]
> Чтобы включить дополнительные утверждения в id_token, см. документацию по дополнительным утверждениям в разделе [как предоставить дополнительные утверждения для приложения Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)

### <a name="access-tenant-profile-claims"></a>Доступ к утверждениям профиля клиента

Чтобы получить доступ к утверждениям учетной записи, которые отображаются в других клиентах, сначала необходимо привести объект учетной записи к `IMultiTenantAccount`. Все учетные записи могут быть несколькими клиентами, но количество профилей клиентов, доступных через MSAL, зависит от того, какие клиенты запросили маркеры, используя текущую учетную запись.  Пример:

```java
// Psuedo Code
IAccount account = getAccount("accountid");
IMultiTenantAccount multiTenantAccount = (IMultiTenantAccount)account;

multiTenantAccount.getTenantProfiles().get("tenantid for fabrikam").getClaims().get("family_name");
multiTenantAccount.getTenantProfiles().get("tenantid for contoso").getClaims().get("family_name");
```

## <a name="b2c-policies--accounts"></a>Политики B2C & учетные записи

Маркеры обновления для учетной записи не являются общими для всех политик B2C. В результате единый вход с использованием маркеров невозможен. Это не значит, что единый вход невозможен. Это означает, что единый вход должен использовать интерактивный интерфейс, в котором доступен файл cookie для включения единого входа.

Это также означает, что в случае MSAL при получении маркеров с помощью разных политик B2C они рассматриваются как отдельные учетные записи — каждый с собственным идентификатором. Если вы хотите использовать учетную запись для запроса маркера с помощью `acquireTokenSilent`, необходимо выбрать учетную запись из списка учетных записей, которые соответствуют политике, используемой с запросом маркера. Пример:

```java
// Get Account For Policy

String policyId = "SignIn";
IAccount signInPolicyAccount = getAccountForPolicyId(app, policyId);

private IAccount getAccountForPolicy(IPublicClientApplication app, String policyId)
{
    List<IAccount> accounts = app.getAccounts();

    foreach(IAccount account : accounts)
   {
        if (account.getClaims().get("tfp").equals(policyId))
        {
            return account;
        }
    }

    return null;
}
```
