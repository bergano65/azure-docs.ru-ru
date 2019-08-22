---
title: Новые возможности Заметки о выпуске Azure Active Directory | Документация Майкрософт
description: Узнайте о новых возможностях Azure Active Directory, включая последние заметки о выпуске, известные проблемы, исправления ошибок, нерекомендуемые функции и предстоящие изменения.
services: active-directory
author: eross-msft
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50f2f1f7f5d8509a28557e9704bc178be465b1ee
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648655"
---
# <a name="whats-new-in-azure-active-directory"></a>Новые возможности Azure Active Directory

>Получите уведомления о том, когда следует повторно посетить эту страницу для получения обновлений, скопировав и вставляя этот `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` URL- ![адрес в значок](./media/whats-new/feed-icon-16x16.png) модуля чтения RSS — средство чтения канала.

Azure AD усовершенствуется на постоянной основе. Чтобы вы оставались в курсе последних разработок, в этой статье предоставлены такие сведения:

- Последние выпуски.
- Известные проблемы
- Исправления ошибок
- Нерекомендуемые функции.
- Планы по изменениям.

Эта страница обновляется ежемесячно, поэтому регулярно пересматривайте ее. Если вам понадобятся материалы, которым более шести месяцев, их можно найти в статье [Архив сведений о новых возможностях в Azure Active Directory](whats-new-archive.md).

---

## <a name="august-2019"></a>Август 2019 г.

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET игнорирует общий кэш MSAL.NET для сценариев от имени.

**Тип.** исправление  
**Категория службы:** аутентификация (вход)  
**Возможности продукта:** Проверка подлинности пользователя

Начиная с библиотеки проверки подлинности Azure AD (ADAL.NET) версии 5.0.0-Preview, разработчики приложений должны [сериализовать один кэш на учетную запись для веб-приложений и веб-API](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). В противном случае некоторые сценарии использования [потока «от имени](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow)», а также некоторые конкретные варианты `UserAssertion`использования могут привести к повышению привилегий. Чтобы избежать этой уязвимости, ADAL.NET теперь игнорирует общий кэш библиотеки проверки подлинности Майкрософт для DotNet (MSAL.NET) для сценариев от имени.

Дополнительные сведения об этой неполадке см. в разделе [Azure Active Directory уязвимость повышения привилегий в библиотеке проверки](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258)подлинности.

---

## <a name="july-2019"></a>2019 июля

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Планирование изменений: Обновление службы прокси приложения для поддержки только TLS 1,2

**Тип:** план изменений  
**Категория службы:** Прокси приложения  
**Возможности продукта:** Контроль доступа

Чтобы обеспечить наиболее надежное шифрование, мы начнем ограничить доступ службы прокси приложения только к протоколам TLS 1,2. Это ограничение изначально будет выводиться для клиентов, которые уже используют протоколы TLS 1,2, поэтому вы не сможете увидеть их. Полная нерекомендуемость протоколов TLS 1,0 и TLS 1,1 будет завершена до 31 августа 2019. Клиенты, которые по-прежнему используют TLS 1,0 и TLS 1,1, получат расширенное уведомление о подготовке к этому изменению.

Для поддержания подключения к службе прокси приложения в рамках этого изменения рекомендуется убедиться, что комбинации "клиент-сервер" и "браузер-сервер" обновлены для использования TLS 1,2. Также рекомендуется включить все клиентские системы, используемые сотрудниками, для доступа к приложениям, опубликованным через службу прокси приложения.

Дополнительные сведения см. [в статье Добавление локального приложения для удаленного доступа через прокси приложения в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Планирование изменений: Обновления проекта поступают в коллекцию приложений

**Тип:** план изменений  
**Категория службы:** корпоративные приложения  
**Возможности продукта:** Единый вход

Новые изменения пользовательского интерфейса поступают в структуру **добавления из области коллекции** в колонке **Добавление приложения** . Эти изменения облегчают поиск приложений, поддерживающих автоматическую подготовку, OpenID Connect Connect, язык разметки зявлений системы безопасности (SAML) (SAML) и единый вход по паролю (SSO).

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Планирование изменений: Удаление IP-адреса сервера MFA с IP-адреса Office 365

**Тип:** план изменений  
**Категория службы:** MFA  
**Возможности продукта:** Защита удостоверений

Мы удалим IP-адрес сервера MFA из [IP-адреса и с URL Office 365](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Если вы в настоящее время используете эти страницы для обновления параметров брандмауэра, необходимо убедиться, что вы также включаете список IP-адресов, описанных в разделе **требования к брандмауэру сервера многофакторной идентификации Azure** в статье Приступая к [работе. ](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements)в статье о сервере многофакторной идентификации Azure.

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Для токенов только для приложений теперь требуется, чтобы клиентское приложение существовало в клиенте ресурса.

**Тип:** исправление  
**Категория службы:** аутентификация (вход)  
**Возможности продукта:** Проверка подлинности пользователя

26 июля 2019 г. Мы изменили, как мы предоставляем маркеры только для приложений через [предоставление учетных данных клиента](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow). Ранее приложения могли получить токены для вызова других приложений, независимо от того, находилось ли клиентское приложение у клиента. Мы обновили это поведение, так что ресурсы с одним клиентом, иногда называемые веб-API, могут вызываться только клиентскими приложениями, которые существуют в клиенте ресурса.

Если ваше приложение не находится в клиенте ресурсов, вы получите сообщение об ошибке, которое говорит `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` о том, что для устранения этой проблемы необходимо создать субъект-службу клиентского приложения в клиенте с помощью [конечной точки согласия администратора](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) или с [помощью PowerShell. ](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell), что гарантирует, что клиент предоставил приложению разрешение на работу в клиенте.

Дополнительные сведения см. в разделе [новые возможности проверки](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant)подлинности.

> [!NOTE]
> Существующее согласие между клиентом и API-интерфейсом остается необязательным. Приложения по-прежнему должны выполнять свои собственные проверки авторизации.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Новый безпарольный вход в Azure AD с помощью ключей безопасности FIDO2

**Тип:** Новая функция  
**Категория службы.** аутентификация (вход)  
**Возможности продукта:** Проверка подлинности пользователя

Теперь клиенты Azure AD могут задавать политики для управления ключами безопасности FIDO2 для пользователей и групп Организации. Конечные пользователи также могут самостоятельно регистрировать свои ключи безопасности, использовать ключи для входа в учетные записи Майкрософт на веб-сайтах, пока на устройствах с поддержкой FIDO, а также входить на устройства Windows 10, присоединенные к Azure AD.

Дополнительные сведения см. [в статье Включение входа без пароля для Azure AD (Предварительная версия)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable) для сведений, относящихся к администратору, и Настройка сведений о [безопасности для использования ключа безопасности (Предварительная версия)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) для сведений, относящихся к конечным пользователям.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Новые Федеративные приложения, доступные в коллекции Azure AD App-Июль 2019

**Тип:** Новая функция  
**Категория службы:** корпоративные приложения  
**Возможности продукта:** Интеграция сторонних продуктов

В июле 2019 мы добавили 18 новых приложений с поддержкой Федерации в коллекцию приложений:

[Унжербоекк Software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [яркий шаблон омничаннел Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), [Нелли](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [Аккуиреио](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [Луоп](https://www.looop.co/schedule-a-demo/), [продуктбоард](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), Microsoft [Azure SSO Access для есидекс соответствует Office™](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [устанавливать ориентиры](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [ Abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [хождение](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [флипснакк](https://www.flipsnack.com/accounts/sign-in-sso.html), [Вандера](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [твинесоЦиал](https://twinesocial.com/), [каллидус](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [хиперанна](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [фармид вастевитнесс](https://www.pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog артефакты](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Автоматизация подготовки учетных записей пользователей для новых поддерживаемых приложений SaaS

**Тип:** Новая функция  
**Категория службы:** корпоративные приложения  
**Возможности продукта:** Мониторинг и создание отчетов

Теперь вы можете автоматизировать создание, обновление и удаление учетных записей пользователей для новых интегрированных приложений:

- [Поднимая](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Федеративный каталог](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [пеакон](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Дополнительные сведения о том, как лучше защитить организацию с помощью автоматизированной подготовки учетных записей пользователей, см. в статье [Автоматизация подготовки пользователей в приложениях SaaS с помощью Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) .

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Новый тег службы доменных служб Azure AD для группы безопасности сети

**Тип:** Новая функция  
**Категория службы:** Доменные службы Azure AD  
**Возможности продукта:** Доменные службы Azure AD

Если вы устали управлять длинными списками IP-адресов и диапазонов, вы можете использовать новый тег **азуреактиведиректоридомаинсервицес** Network Service в группе безопасности сети Azure, чтобы защитить входящий трафик к виртуальным службам домена Azure AD. Сетевая подсеть.

Дополнительные сведения об этом новом теге службы см. в статье [группы безопасности сети для доменных служб Azure AD](https://docs.microsoft.com/azure/active-directory-domain-services/network-considerations#default-network-service-group).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Новые Аудиты безопасности для доменных служб Azure AD (общедоступная Предварительная версия)

**Тип:** Новая функция  
**Категория службы:** Доменные службы Azure AD  
**Возможности продукта:** Доменные службы Azure AD

Мы рады сообщить о выпуске аудита безопасности службы домена Azure AD до общедоступной предварительной версии. Аудит безопасности позволяет получить важные сведения о службах проверки подлинности путем потоковой передачи событий аудита безопасности в целевые ресурсы, включая службу хранилища Azure, рабочие области Azure Log Analytics и концентратор событий Azure, с помощью службы домена Azure AD. портала.

Дополнительные сведения см. в статье [Включение аудита безопасности для доменных служб Azure AD (Предварительная версия)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Новые методы проверки подлинности использование & Insights (общедоступная Предварительная версия)

**Тип:** Новая функция  
**Категория службы:** Самостоятельный сброс пароля  
**Возможности продукта:** Мониторинг и создание отчетов

Новые методы проверки подлинности & отчеты об аналитике позволяют понять, как в организации регистрируются такие функции, как многофакторная идентификация Azure и самостоятельный сброс пароля, включая число зарегистрированных. Пользователи для каждого компонента, частота использования самостоятельного сброса пароля для сброса паролей и метод сброса.

Дополнительные сведения см. в статье [использование методов проверки Подлинности & Insights (Предварительная версия)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Новые отчеты о безопасности доступны для всех администраторов Azure AD (общедоступная Предварительная версия)

**Тип:** Новая функция  
**Категория службы:** Identity Protection  
**Возможности продукта.** Защита удостоверений

Все администраторы Azure AD теперь могут выбрать баннер в верхней части существующих отчетов о безопасности, например отчет " **Пользователи, помеченные для риска** ", чтобы приступить к использованию новой системы безопасности, как показано в отчетах о **рискованных пользователях** и событиях входа в **систему** . . Со временем все отчеты о безопасности будут перенесены из старых версий в новые, а новые отчеты предоставляют следующие дополнительные возможности:

- Расширенная фильтрация и сортировка

- Групповые действия, такие как отклонение пользователя

- Подтверждение скомпрометированных или защищенных сущностей

- Состояние риска, охватывающее: Под угрозой, отклонено, исправлено и подтверждено скомпрометировано

Дополнительные сведения см. в разделе Отчет [о рискованных пользователях](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risky-users-signins#risky-users-report) и отчет о событиях [входа](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risky-users-signins#risky-sign-ins-report)с рисками.

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Новые Аудиты безопасности для доменных служб Azure AD (общедоступная Предварительная версия)

**Тип:** Новая функция  
**Категория службы:** Доменные службы Azure AD  
**Возможности продукта:** Доменные службы Azure AD

Мы рады сообщить о выпуске аудита безопасности службы домена Azure AD до общедоступной предварительной версии. Аудит безопасности позволяет получить важные сведения о службах проверки подлинности путем потоковой передачи событий аудита безопасности в целевые ресурсы, включая службу хранилища Azure, рабочие области Azure Log Analytics и концентратор событий Azure, с помощью службы домена Azure AD. портала.

Дополнительные сведения см. в статье [Включение аудита безопасности для доменных служб Azure AD (Предварительная версия)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Новая прямая Федерация B2B с использованием SAML/WS-подача (общедоступная Предварительная версия)

**Тип:** Новая функция  
**Категория службы:** B2B  
**Возможности продукта:** B2B/B2C

Прямая Федерация позволяет упростить работу с партнерами, для которых управляемое ИТ-решение не является Azure AD, работая с системами идентификации, поддерживающими стандарты SAML или WS-подач. После настройки прямой связи Федерации с партнером любой новый гостевой пользователь, который вы пригласили от этого домена, сможет совместно работать с вами с помощью существующей учетной записи организации, что делает работу пользователя с вашими гостями более удобной.

Дополнительные сведения см. [в статье прямая Федерация с AD FS и сторонними поставщиками для гостевых пользователей (Предварительная версия)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Автоматизация подготовки учетных записей пользователей для новых поддерживаемых приложений SaaS

**Тип:** Новая функция  
**Категория службы:** корпоративные приложения  
**Возможности продукта:** Мониторинг и создание отчетов

Теперь вы можете автоматизировать создание, обновление и удаление учетных записей пользователей для новых интегрированных приложений:

- [Поднимая](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Федеративный каталог](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [пеакон](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Дополнительные сведения о том, как лучше защитить организацию с помощью автоматизированной подготовки учетных записей пользователей, см. в статье [Автоматизация подготовки пользователей в приложениях SaaS с помощью Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Новая проверка для повторяющихся имен групп на портале Azure AD

**Тип:** Новая функция  
**Категория службы.** Управление группами  
**Возможности продукта:** Взаимодействие

Теперь при создании или обновлении имени группы на портале Azure AD мы продолжим проверку на дублирование существующего имени группы в ресурсе. Если мы определили, что имя уже используется другой группой, вам будет предложено изменить имя.

Дополнительные сведения см. [в статье Управление группами на портале Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD теперь поддерживает статические параметры запроса в URI ответа (Redirect)

**Тип:** Новая функция  
**Категория службы.** аутентификация (вход)  
**Возможности продукта:** Проверка подлинности пользователя

Приложения Azure AD теперь могут регистрировать и использовать URI-идентификаторы ответа (перенаправления) со статическими параметрами запроса `https://contoso.com/oauth2?idp=microsoft`(например,) для запросов OAuth 2,0. Параметр статического запроса подчиняется строковому совпадению для URI ответа, как и любая другая часть URI ответа. Если нет ни одной зарегистрированной строки, соответствующей URL-адресу, декодированному, то запрос отклоняется. Если URI ответа найден, то для перенаправления пользователя используется вся строка, включая параметр статического запроса.

Идентификаторы URI динамических ответов по-прежнему запрещены, так как они представляют угрозу безопасности и не могут использоваться для сохранения сведений о состоянии в запросе проверки подлинности. Для этой цели используйте `state` параметр.

В настоящее время экраны регистрации приложений портал Azure все еще блокируют параметры запроса. Однако можно вручную изменить манифест приложения, чтобы добавить и проверить параметры запроса в приложении. Дополнительные сведения см. в разделе [новые возможности проверки](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters)подлинности.

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Журналы действий (API-интерфейсы MS Graph) для Azure AD теперь доступны с помощью командлетов PowerShell.

**Тип:** Новая функция  
**Категория службы:** Отчеты  
**Возможности продукта:** Мониторинг и создание отчетов

Мы рады сообщить, что журналы действий Azure AD (отчеты для аудита и входа) теперь доступны в модуле Azure AD PowerShell. Ранее можно было создавать собственные сценарии с помощью конечных точек MS API Graph, и теперь мы расширили эту возможность для командлетов PowerShell.

Дополнительные сведения об использовании этих командлетов см. в разделе [командлеты Azure AD PowerShell для создания отчетов](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Обновлены элементы управления фильтра для журналов аудита и входа в Azure AD.

**Тип:** Измененная функция  
**Категория службы:** Отчеты  
**Возможности продукта:** Мониторинг и создание отчетов

Отчеты журнала аудита и входа обновлены, поэтому теперь можно применять различные фильтры, не добавляя их в качестве столбцов на экранах отчета. Кроме того, теперь можно решить, сколько фильтров нужно отображать на экране. Все эти обновления работают вместе, чтобы сделать отчеты более удобными для чтения и в большей области с учетом ваших потребностей.

Дополнительные сведения об этих обновлениях см. в разделе [Фильтрация журналов аудита](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) и [Фильтрация действий входа](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities).

---

## <a name="june-2019"></a>Июнь 2019 г.

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Новый API Рискдетектионс для Microsoft Graph (общедоступная Предварительная версия)

**Тип:** Новая функция  
**Категория службы:** Identity Protection  
**Возможности продукта.** Защита удостоверений

Мы рады сообщить о новом API Рискдетектионс для Microsoft Graph теперь в общедоступной предварительной версии. Вы можете использовать этот новый API для просмотра списка обнаруженных в Организации рисков, связанных с защитой удостоверений и пользователями. Этот API также можно использовать для более эффективного выполнения запросов к обнаружению рисков, включая сведения о типе обнаружения, состоянии, уровне и т. д.

Дополнительные сведения см. в [справочной документации по API обнаружения рисков](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Новые Федеративные приложения, доступные в коллекции приложений Azure AD — Июнь 2019

**Тип:** Новая функция  
**Категория службы:** корпоративные приложения  
**Возможности продукта:** Интеграция сторонних продуктов

В июне 2019 мы добавили 22 новых приложения с поддержкой Федерации в коллекцию приложений:

[Azure AD SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Отсука шокаи (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [анакуа](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [VPN-клиент Azure](https://portal.azure.com/), [расход](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [вспомогательный вспомогательный модуль](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial), [костпоинт](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [глобалоне](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Мерцедес-бенз в автомобиле офисе](https://me.secure.mercedes-benz.com/), [скоре](https://app.justskore.it/), [Консоль инфраструктуры облака Oracle](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [Аутентификация циберарк SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [скрибле edu](https://www.scrible.com/sign-in/#/create-account), [пандадок](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [перцептикс](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Проптимисе OS](https://proptimise.co.uk/software/), [втижер CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager для Oracle Товары розничной торговли, диспетчер доступа Oracle для Oracle E-бизнес Suite, Oracle ИДКС для E-Business Suite, Oracle ИДКС для PeopleSoft, Oracle ИДКС для JD Edwards

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Автоматизация подготовки учетных записей пользователей для новых поддерживаемых приложений SaaS

**Тип:** Новая функция  
**Категория службы:** корпоративные приложения  
**Возможности продукта:** Мониторинг и создание отчетов

Теперь вы можете автоматизировать создание, обновление и удаление учетных записей пользователей для новых интегрированных приложений:

- [Zoom](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Envoy](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Дополнительные сведения о том, как лучше защитить организацию с помощью автоматизированной подготовки учетных записей пользователей, см. в статье [Автоматизация подготовки пользователей в приложениях SaaS с помощью Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) .

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Просмотр хода выполнения в режиме реального времени для службы подготовки Azure AD

**Тип:** Измененная функция  
**Категория службы:** подготовка приложений  
**Возможности продукта:** Управление жизненным циклом удостоверений

Мы обновили интерфейс подготовки Azure AD, включив в него новый индикатор выполнения, который показывает, насколько вы находились в процессе подготовки пользователей. Этот обновленный интерфейс также предоставляет сведения о количестве пользователей, подготовленных в ходе текущего цикла, а также о количестве пользователей, которые были подготовлены к работе.

Дополнительные сведения см. [в разделе Проверка состояния подготовки пользователей](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Теперь на экранах выхода и ошибок отображается фирменная символика компании

**Тип:** Измененная функция  
**Категория службы:** аутентификация (вход)  
**Возможности продукта:** Проверка подлинности пользователя

Мы обновили Azure AD, чтобы фирменная символика компании появилась на экранах выхода и ошибок, а также на странице входа. Чтобы включить эту функцию, Azure AD просто использует уже настроенные ресурсы в области **фирменной символики компании** портал Azure.

Дополнительные сведения о настройке фирменной символики компании см. [в статье Добавление фирменной символики на Azure Active Directory страницы вашей организации](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Сервер многофакторной идентификации Azure (MFA) больше не доступен для новых развертываний

**Тип:** Нерекомендуемые  
**Категория службы:** MFA  
**Возможности продукта:** Защита удостоверений

По состоянию на 1 июля 2019 Корпорация Майкрософт больше не будет предлагать сервер MFA для новых развертываний. Новые клиенты, желающие требовать многофакторную проверку подлинности в Организации, теперь должны использовать службу многофакторной идентификации Azure на основе облака. Клиенты, которые активировали сервер MFA до 1 июля, не увидят изменений. Вы по-прежнему сможете скачать последнюю версию, получить будущие обновления и создать учетные данные активации.

Дополнительные сведения см. в статье Приступая к [работе с сервером многофакторной идентификации Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy). Дополнительные сведения об облачной многофакторной идентификации Azure см. в статье [Планирование развертывания многофакторной идентификации Azure на основе облачных служб](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="may-2019"></a>Май 2019 г.

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Изменение службы: Поддержка будущих протоколов TLS 1,2 в службе прокси приложения

**Тип:** план изменений  
**Категория службы:** Прокси приложения  
**Возможности продукта:** Контроль доступа

Чтобы обеспечить лучшее в своем классе шифрование для наших клиентов, мы ограничив доступ только к протоколам TLS 1,2 в службе прокси приложения. Это изменение постепенно рассчитывается для клиентов, которые уже используют протоколы TLS 1,2, поэтому вы не должны видеть никаких изменений.

Устаревшие протоколы TLS 1,0 и TLS 1,1 происходят 31 августа 2019, но мы предоставим дополнительное расширенное уведомление, так что у вас будет достаточно времени для подготовки к этому изменению. Чтобы подготовиться к этому изменению, убедитесь, что комбинации "клиент-сервер" и "браузер-сервер", включая все клиенты, используемые пользователями для доступа к приложениям, опубликованным через прокси приложения, обновлены для использования протокола TLS 1,2 для поддержания подключения к приложению. Прокси-служба. Дополнительные сведения см. [в статье Добавление локального приложения для удаленного доступа через прокси приложения в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Использование отчета об использовании и Insights для просмотра данных входа, связанных с приложением

**Тип:** Новая функция  
**Категория службы:** корпоративные приложения  
**Возможности продукта:** Мониторинг и создание отчетов

Теперь можно использовать отчет об использовании и Insights, расположенный в области **корпоративные приложения** портал Azure, чтобы получить представление данных входа, ориентированное на приложения, включая следующие сведения:

- Популярные приложения, используемые в Организации

- Приложения с наиболее неудачными входами

- Основные ошибки входа для каждого приложения

Дополнительные сведения об этой функции см. [в разделе Отчет об использовании и Insights на портале Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report) .

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Автоматизация подготовки пользователей в облачных приложениях с помощью Azure AD

**Тип:** Новая функция  
**Категория службы:** корпоративные приложения  
**Возможности продукта:** Мониторинг и создание отчетов

Следуйте этим новым руководствам, чтобы использовать службу подготовки Azure AD для автоматизации создания, удаления и обновления учетных записей пользователей для следующих облачных приложений:

- [Сопоставление](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [динамиксигнал](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [киперсекурити](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

Вы также можете следовать этому новому [учебнику по Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), в котором содержатся сведения о подготовке объектов группы.

Дополнительные сведения о том, как лучше защитить организацию с помощью автоматической подготовки учетных записей пользователей, см. в статье [Автоматизация подготовки пользователей в приложениях SaaS с помощью Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Оценка безопасности удостоверений теперь доступна в Azure AD (общая доступность)

**Тип:** Новая функция  
**Категория службы:** Н/Д  
**Возможности продукта:** Защита удостоверений

Теперь вы можете отслеживать и улучшать безопасность идентификации с помощью функции "Оценка безопасности удостоверений" в Azure AD. Функция оценки безопасности идентификации использует одну панель мониторинга, которая поможет вам:

- Пообъектно измерять уровень безопасности идентификации на основе оценки от 1 до 223.

- Планирование улучшений безопасности удостоверений

- Изучите успешность улучшения безопасности

Дополнительные сведения о функции оценки безопасности удостоверений см. [в разделе что такое идентификатор защиты идентификации в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Теперь доступна новая возможность Регистрация приложений (общая доступность)

**Тип:** Новая функция  
**Категория службы.** аутентификация (вход)  
**Возможности продукта:** Интерфейс разработчика

Новая работа [Регистрация приложений](https://aka.ms/appregistrations) теперь доступна в общедоступной области. Этот новый интерфейс включает все основные функции, с которыми вы знакомы, с помощью портал Azure и портала регистрации приложений, и они улучшаются с помощью следующих средств:

- **Улучшенное управление приложениями.** Вместо того чтобы просматривать приложения на разных порталах, теперь можно просматривать все приложения в одном месте.

- **Упрощенная регистрация приложения.** С улучшенной навигацией по усовершенствованному интерфейсу выбора разрешений теперь стало проще регистрировать приложения и управлять ими.

- **Более подробные сведения.** Вы можете найти дополнительные сведения о приложении, включая краткие руководства и многое другое.

Дополнительные сведения см. в [статье платформа Microsoft Identity](https://docs.microsoft.com/azure/active-directory/develop/) и [Регистрация приложений теперь общедоступна.](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) объявление в блоге.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Новые возможности, доступные в API рискованных пользователей для защиты идентификации

**Тип:** Новая функция  
**Категория службы:** Identity Protection  
**Возможности продукта.** Защита удостоверений

Мы рады сообщить о том, что теперь вы можете использовать API рискованных пользователей для получения журнала рисков пользователей, закрытия рискованных пользователей и подтверждения пользователей как скомпрометированных. Это изменение позволяет более эффективно обновлять состояние риска для пользователей и анализировать историю рисков.

Дополнительные сведения см. в [справочной документации по API рискованных пользователей](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Новые Федеративные приложения, доступные в коллекции приложений Azure AD — Май 2019

**Тип:** Новая функция  
**Категория службы:** корпоративные приложения  
**Возможности продукта:** Интеграция сторонних продуктов

В 2019 мая мы добавили в коллекцию приложений 21 новые приложения с поддержкой федерации:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [Real Links](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Sign](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [Displayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo Sales Engage](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [OutSystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [Meta4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [Quantum Workplace](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [Cobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [webMethods API Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Улучшенные возможности создания групп и управления ими на портале Azure AD

**Тип:** Новая функция  
**Категория службы.** Управление группами  
**Возможности продукта:** Взаимодействие

Мы внесли улучшения в возможности, связанные с группами, на портале Azure AD. Эти улучшения позволяют администраторам лучше управлять списками групп, списками членов и создавать дополнительные параметры создания.

Добавлены некоторые улучшения, среди которых:

- Базовая фильтрация по типу членства и типу группы.

- Добавление новых столбцов, таких как источник и адрес электронной почты.

- Возможность простого удаления нескольких групп, членов и списков владельцев.

- Возможность выбора адреса электронной почты и добавления владельцев во время создания группы.

Дополнительные сведения см. в статьях [Создание базовой группы и добавление членов с помощью Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Настройка политики именования для групп Office 365 на портале Azure AD (общая доступность)

**Тип:** Измененная функция  
**Категория службы:** Управление группами  
**Возможности продукта:** Взаимодействие

Теперь администраторы могут настроить политику именования для групп Office 365 с помощью портала Azure AD. Это изменение помогает обеспечить соответствие соглашений об именовании для групп Office 365, созданных или измененных пользователями в Организации.

Политику именования для групп Office 365 можно настроить двумя разными способами.

- Определите префиксы или суффиксы, которые автоматически добавляются в имя группы.

- Отправка настроенного набора заблокированных слов для Организации, которые не разрешены в именах групп (например, "Исполнительный директор, зарплата, HR").

Дополнительные сведения см. [в разделе принудительная политика именования для групп Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Конечные точки API Microsoft Graph теперь доступны для журналов действий Azure AD (общая доступность).

**Тип:** Измененная функция  
**Категория службы:** Отчеты  
**Возможности продукта:** Мониторинг и создание отчетов

Мы рады сообщить о выходе общедоступной версии Microsoft Graph поддержки конечных точек API для журналов действий Azure AD. В этом выпуске теперь можно использовать версии 1,0 журналов аудита Azure AD, а также интерфейсы API для входа в систему.

Дополнительные сведения см. в статье [Общие сведения об API журнала аудита Azure AD](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Теперь администраторы могут использовать условный доступ для Объединенного процесса регистрации (общедоступная Предварительная версия)

**Тип:** Новая функция  
**Категория службы.** Условный доступ  
**Возможности продукта.** Защита удостоверений  

Теперь администраторы могут создавать политики условного доступа для использования в общей странице регистрации. Сюда входит применение политик для разрешения регистрации, если:

- Пользователи находятся в доверенной сети.

- Пользователи являются низким риском для входа.

- Пользователи находятся на управляемом устройстве.

- Пользователи согласны с условиями использования (условий использования) Организации.

Дополнительные сведения о условном доступе и сбросе пароля см. в [блоге о условном доступе для совместной работы по регистрации MFA и сброса пароля в Azure AD](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Дополнительные сведения о политиках условного доступа для Объединенного процесса регистрации см. в разделе [политики условного доступа для Объединенной регистрации](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration). Дополнительные сведения о функции "условия использования Azure AD" см. в разделе [Azure Active Directory условия использования](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

## <a name="april-2019"></a>Апрель 2019 г.

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Новое обнаружение аналитики угроз Azure AD теперь доступно в составе защита идентификации Azure AD

**Тип.** Новая функция  
**Категория службы:** Azure AD Identity Protection  
**Возможности продукта:** Безопасность и защита удостоверений

Обнаружение аналитики угроз Azure AD теперь доступно в составе обновленной защита идентификации Azure AD функции. Эта новая функция позволяет указать необычные действия пользователя для конкретного пользователя или действия, которые соответствуют известным шаблонам атак, основанным на внутренних и внешних источниках анализа угроз Майкрософт.

Дополнительные сведения о обновленной версии защита идентификации Azure AD см. в блоге, посвященном [четырем усовершенствованиям Защита идентификации Azure AD](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) , а также о том, [что защита идентификации Azure Active Directory (Обновлено)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) рассмотрен. Дополнительные сведения об обнаружении аналитики угроз в Azure AD см. в статье [Защита идентификации Azure Active Directory события риска](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence) .

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Теперь доступно управление назначениями Azure AD (общедоступная Предварительная версия)

**Тип:** Новая функция  
**Категория службы:** Identity Governance  
**Возможности продукта:** Identity Governance

Управление назначением Azure AD теперь в общедоступной предварительной версии помогает клиентам делегировать управление пакетами доступа, которые определяют, как сотрудники и бизнес-партнеры могут запрашивать доступ, кто должен утверждать и как долго они имеют доступ. Пакеты доступа могут управлять членством в группах Azure AD и Office 365, назначениями ролей в корпоративных приложениях и назначениями ролей для сайтов SharePoint Online. Дополнительные сведения об управлении обслуживанием см. в статье Обзор управления назначением [Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Дополнительные сведения о Azure AD Identity Governanceных функциях, в том числе управление привилегированными пользователями, проверках доступа и условиях использования, см. в статье [что такое Azure AD Identity Governance?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Настройка политики именования для групп Office 365 на портале Azure AD (общедоступная Предварительная версия)

**Тип:** Новая функция  
**Категория службы.** Управление группами  
**Возможности продукта:** Взаимодействие

Теперь администраторы могут настроить политику именования для групп Office 365 с помощью портала Azure AD. Это изменение помогает обеспечить соответствие соглашений об именовании для групп Office 365, созданных или измененных пользователями в Организации.

Политику именования для групп Office 365 можно настроить двумя разными способами.

- Определите префиксы или суффиксы, которые автоматически добавляются в имя группы.

- Отправка настроенного набора заблокированных слов для Организации, которые не разрешены в именах групп (например, "Исполнительный директор, зарплата, HR").

Дополнительные сведения см. [в разделе принудительная политика именования для групп Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Журналы действий Azure AD теперь доступны в Azure Monitor (общая доступность)

**Тип:** Новая функция  
**Категория службы:** Отчеты  
**Возможности продукта:** Мониторинг и создание отчетов

Чтобы помочь устранить ваши отзывы о визуализациях с помощью журналов действий Azure AD, мы представляем новую функцию Insights в Log Analytics. Эта функция позволяет получить представление о ресурсах Azure AD с помощью наших интерактивных шаблонов, называемых книгами. Эти предварительно созданные книги могут предоставлять сведения для приложений и пользователей, а также включать:

- **Входы в систему.** Содержит сведения о приложениях и пользователях, включая расположение для входа, используемую версию операционной системы или клиента браузера, а также количество успешных или неудачных входов.

- **Устаревшая проверка подлинности и условный доступ.** Предоставляет сведения для приложений и пользователей, использующих устаревшую проверку подлинности, включая использование многофакторной идентификации, активируемое политиками условного доступа, приложениями, использующими политики условного доступа, и т. д.

- **Анализ сбоев при входе.** Помогает определить, возникают ли ошибки при входе в систему из-за действий пользователя, проблем с политиками или инфраструктуры.

- **Пользовательские отчеты.** Вы можете создать или изменить существующие книги, чтобы настроить функцию Insights для вашей организации.

Дополнительные сведения см. [в разделе Использование книг Azure Monitor для Azure Active Directory отчетов](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Новые Федеративные приложения доступны в коллекции приложений Azure AD — Апрель 2019

**Тип:** Новая функция  
**Категория службы:** корпоративные приложения  
**Возможности продукта:** Интеграция сторонних продуктов

В апреле 2019 мы добавили 21 новые приложения с поддержкой Федерации в коллекцию приложений:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [хрворкс Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [Перколате](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [мобиконтрол](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [Шибуми](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [бенчлинг](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [милеик](https://mileiq.onelink.me/991934284/7e980085), [пажедна](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [едубрите LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio Connect ](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMM](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Мител Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Алибаба Cloud (единый вход на основе ролей)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Управление капиталом цертент](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [сектиго Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [гринорбит](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [воркгрид](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [Monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [индигго](https://indiggolead.com/)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Новый параметр частоты проверок доступа и выбор нескольких ролей

**Тип:** Новая функция  
**Категория службы.** Проверки доступа  
**Возможности продукта:** Identity Governance

Новые обновления в проверках доступа Azure AD позволяют:

- Изменяйте частоту проверок доступа в **два раза в год**, помимо ранее существующих параметров еженедельно, ежемесячно, ежеквартально и ежегодно.

- При создании одной проверки доступа выберите несколько ролей ресурсов Azure AD и Azure. В этом случае все роли настроены с одинаковыми параметрами, и все рецензенты будут уведомлены одновременно.

Дополнительные сведения о создании проверки доступа см. [в статье Создание проверки доступа для групп или приложений в проверках доступа Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Системы оповещений по электронной почте Azure AD Connect переносятся, отправляя новые сведения об отправителе по электронной почте для некоторых клиентов.

**Тип:** Измененная функция  
**Категория службы:** AD Sync  
**Возможности продукта.** Платформа

Azure AD Connect находится в процессе переноса системы предупреждений электронной почты, потенциально показывая некоторым клиентам новый отправитель электронной почты. Для решения этой проблемы необходимо добавить `azure-noreply@microsoft.com` в список разрешений вашей организации или продолжить получение важных оповещений из Office 365, Azure или ваших служб синхронизации.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Изменения суффикса имени участника-пользователя теперь успешно выполнены между федеративными доменами в Azure AD Connect

**Тип:** исправление  
**Категория службы:** AD Sync  
**Возможности продукта.** Платформа

Теперь вы можете успешно изменить суффикс имени участника-пользователя из одного федеративного домена на другой федеративный домен в Azure AD Connect. Это исправление означает, что в течение цикла синхронизации больше не будет появляться сообщение об ошибке FederatedDomainChangeError или вы получите уведомление по электронной почте "не удалось обновить этот объект в Azure Active Directory, поскольку атрибут [ Федератедусер. UserPrincipalName], является недопустимым. Обновите значение в локальных службах каталогов.

Дополнительные сведения см. в разделе [Устранение ошибок во время синхронизации](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Повышенная безопасность с помощью политики условного доступа на основе защиты приложений в Azure AD (общедоступная Предварительная версия)

**Тип:** Новая функция  
**Категория службы.** Условный доступ  
**Возможности продукта.** Защита удостоверений

Условный доступ на основе защиты приложений теперь доступен с помощью политики " **требуется политика защиты приложений** ". Эта новая политика позволяет повысить безопасность вашей организации, помогая предотвратить следующее:

- Пользователи получают доступ к приложениям без лицензии Microsoft Intune.

- Пользователи не могут получить политику защиты приложений Microsoft Intune.

- Пользователи получают доступ к приложениям без настроенной политики защиты приложений Microsoft Intune.

Дополнительные сведения см. [в разделе как требовать политику защиты приложений для облачного доступа к приложениям с помощью условного доступа](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Новая поддержка единого входа и условного доступа Azure AD в Microsoft ребр (общедоступная Предварительная версия)

**Тип:** Новая функция  
**Категория службы.** Условный доступ  
**Возможности продукта.** Защита удостоверений

Мы улучшили поддержку Azure AD для Microsoft ребра, включая предоставление новой поддержки единого входа и условного доступа Azure AD. Если вы ранее использовали Microsoft Intune Managed Browser, теперь можно использовать Microsoft ребро.

Дополнительные сведения о настройке устройств и приложений, а также об управлении ими с помощью условного доступа см. в статьях использование [управляемых устройств для доступа к облачным приложениям с](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) помощью условного доступа и [требование утвержденных клиентских приложений для доступа к облачным приложениям с помощью условного доступа ](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Дополнительные сведения об управлении доступом с помощью Microsoft ребра с Microsoft Intune политиками см. [в разделе Управление доступом в Интернет с помощью Microsoft Intune браузера, защищенного политикой](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Март 2019 г.

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Инфраструктура процедур идентификации и поддержка настраиваемых политик в Azure Active Directory B2C теперь доступны (GA)

**Тип:** Новая функция  
**Категория службы.** B2C — управление удостоверениями потребителей  
**Возможности продукта.** B2B/B2C

Теперь можно создавать пользовательские политики в Azure AD B2C, включая следующие задачи, которые поддерживаются в масштабе и в рамках соглашения об уровне обслуживания Azure.

- Создайте и отправьте пользовательские пути проверки подлинности с помощью настраиваемых политик.

- Поэтапно описывать пути взаимодействия пользователя для обмена данными между поставщиками удостоверений.

- Определять условные ветвления путей взаимодействия пользователей.

- Преобразуйте и сопоставьте утверждения для использования в решениях и обмене данными в режиме реального времени.

- Используйте службы с поддержкой REST API в пользовательских путях проверки подлинности. Например, с поставщиками электронной почты, Крмс и собственными системами авторизации.

- Федерацию с поставщиками удостоверений, которые соответствуют протоколу OpenIDConnect. Например, с несколькими клиентами Azure AD, поставщиками учетных записей социальных сетей или поставщиками двухфакторной проверки.

Дополнительные сведения о создании настраиваемых политик см. [в статье заметки разработчика для пользовательских политик в Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) и прочитайте [запись блога о Simonах Алекс, включая примеры использования](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Новые Федеративные приложения, доступные в коллекции приложений Azure AD — 2019 марта

**Тип:** Новая функция  
**Категория службы:** корпоративные приложения  
**Возможности продукта:** Интеграция сторонних продуктов

В марте 2019 мы добавили 14 новых приложений с поддержкой Федерации в коллекцию приложений:

[Делегат ISEC7 Mobile Exchange](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [система аудита на основе объяснений](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [экономичный](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [Powerschool Performance Matters](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [интрасети Iris](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit Horizons](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [TAS](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Новые соединители подготовки Zscaler и Atlassian в коллекции Azure AD — Март 2019

**Тип:** Новая функция  
**Категория службы:** подготовка приложений  
**Возможности продукта:** Интеграция сторонних продуктов

Автоматизируйте создание, обновление и удаление учетных записей пользователей для следующих приложений:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler 1](https://aka.ms/ZscalerOneProvisioning), [Zscaler 2](https://aka.ms/ZscalerTwoProvisioning), [Zscaler 3](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

Дополнительные сведения о том, как лучше защитить организацию с помощью автоматической подготовки учетных записей пользователей, см. в статье [Автоматизация подготовки пользователей в приложениях SaaS с помощью Azure AD](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Восстановление удаленных групп Office 365 и управление ими на портале Azure AD

**Тип:** Новая функция  
**Категория службы.** Управление группами  
**Возможности продукта:** Взаимодействие

Теперь вы можете просматривать удаленные группы Office 365 и управлять ими с помощью портала Azure AD. Это изменение позволяет узнать, какие группы доступны для восстановления, а также как окончательно удалить все группы, которые не нужны в Организации.

Дополнительные сведения см. в разделе [Восстановление просроченных или удаленных групп](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Единый вход теперь доступен для локальных приложений Azure AD, защищенных с помощью SAML, через прокси приложения (общедоступная Предварительная версия)

**Тип:** Новая функция  
**Категория службы:** Прокси приложения  
**Возможности продукта:** Контроль доступа

Теперь вы можете предоставить возможность единого входа (SSO) для локальных приложений, прошедших проверку подлинности с помощью SAML, а также удаленный доступ к этим приложениям через прокси приложения. Дополнительные сведения о настройке единого входа SAML с помощью локальных приложений см. в статье [единый вход SAML для локальных приложений с помощью прокси приложения (Предварительная версия)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Клиентские приложения в циклах запросов будут прерываться для повышения надежности и удобства работы пользователей.

**Тип:** Новая функция  
**Категория службы.** аутентификация (вход)  
**Возможности продукта:** Проверка подлинности пользователя

Клиентские приложения могут неправильно выдавать сотни одних и тех же запросов на вход в течение короткого периода времени. Эти запросы, будь то успех или нет, все способствуют снижению пользовательского интерфейса и повышенной рабочей нагрузке для IDP, увеличению задержки для всех пользователей и уменьшению доступности IDP.

Это обновление отправляет `invalid_grant` ошибку: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` для клиентских приложений, которые многократно выдают дублирующиеся запросы за короткий период времени, за пределами области обычной работы. Клиентские приложения, которые сталкиваются с этой проблемой, должны показывать интерактивный запрос, что требует от пользователя повторного входа. Дополнительные сведения об этом изменении и о том, как исправить приложение при возникновении этой ошибки, см. в разделе [новые возможности проверки](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted)подлинности.

---

### <a name="new-audit-logs-user-experience-now-available"></a>Теперь доступны новые журналы аудита взаимодействие с пользователем

**Тип:** Измененная функция  
**Категория службы:** Отчеты  
**Возможности продукта:** Мониторинг и создание отчетов

Мы создали новую страницу **журналов аудита** Azure AD, которая поможет улучшить удобочитаемость и способ поиска информации. Чтобы открыть страницу "новые **журналы аудита** ", выберите **журналы аудита** в разделе **действие** в Azure AD.

![Страница «Создание журналов аудита» с примерами сведений](media/whats-new/audit-logs-page.png)

Дополнительные сведения о новых журналах **аудита** см. в разделе [отчеты о действиях аудита на портале Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Новые предупреждения и рекомендации по предотвращению случайной блокировки администратора от неправильно настроенных политик условного доступа

**Тип:** Измененная функция  
**Категория службы.** Условный доступ  
**Возможности продукта.** Защита удостоверений

Чтобы предотвратить случайную блокирование пользователями собственных клиентов через неправильно настроенные политики условного доступа, мы создали новые предупреждения и обновленные инструкции в портал Azure. Дополнительные сведения о новых руководствах см. [в разделе что такое зависимости служб в Azure Active Directory условном доступе](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Улучшенные условия использования функций для конечных пользователей на мобильных устройствах

**Тип:** Измененная функция  
**Категория службы:** Условия использования  
**Возможности продукта:** Система управления

Мы обновили существующие условия использования, чтобы помочь улучшить способы проверки и согласия на использование на мобильном устройстве. Теперь можно увеличить и уменьшить масштаб, вернуться назад, загрузить сведения и выбрать гиперссылки. Дополнительные сведения об обновленных условиях использования см. в разделе [Azure Active Directory условия использования](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Новые журналы действий Azure AD, доступные для загрузки

**Тип:** Измененная функция  
**Категория службы:** Отчеты  
**Возможности продукта:** Мониторинг и создание отчетов

Теперь вы можете загрузить большие объемы журналов действий непосредственно из портал Azure. Это обновление позволяет:

- Загрузите до 250 000 строк.

- Получение уведомлений после завершения скачивания.

- Настройте имя файла.

- Определите формат выходных данных: JSON или CSV.

Дополнительные сведения об этой функции см. в [разделе Краткое руководство. Скачайте отчет аудита с помощью портал Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Критическое изменение. Обновления для оценки условий с помощью Exchange ActiveSync (EAS)

**Тип:** план изменений  
**Категория службы:** Условный доступ  
**Возможности продукта:** Контроль доступа

Мы собираемся обновить, как Exchange ActiveSync (EAS) оценивает следующие условия:

- Расположение пользователя на основе страны, региона или IP-адреса

- Риск при входе

- Платформа устройства

Если вы ранее использовали эти условия в политиках условного доступа, имейте в виду, что поведение условия может измениться. Например, если ранее в политике было использовано условие расположения пользователя, политика будет пропущена в зависимости от расположения пользователя.

---

## <a name="february-2019"></a>Февраль 2019 г.

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Настраиваемое шифрование маркеров SAML Azure AD (общедоступная Предварительная версия) 

**Тип:** Новая функция  
**Категория службы:** корпоративные приложения  
**Возможности продукта:** Единый вход

Теперь вы можете настроить любое поддерживаемое приложение SAML для получения зашифрованных токенов SAML. При настройке и использовании с приложением Azure AD шифрует выпущенные утверждения SAML с помощью открытого ключа, полученного из сертификата, хранящегося в Azure AD.

Дополнительные сведения о настройке шифрования маркеров SAML см. в статье [Настройка шифрования маркеров SAML в Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Создание проверки доступа для групп или приложений с помощью проверок доступа Azure AD

**Тип:** Новая функция  
**Категория службы.** Проверки доступа  
**Возможности продукта.** Система управления

Теперь можно включить несколько групп или приложений в одну проверку доступа Azure AD для назначения членства в группах или приложений. Проверки доступа с несколькими группами или приложениями настроены с использованием одних и тех же параметров, и все входящие рецензенты будут уведомлены одновременно.

Дополнительные сведения о создании проверки доступа с помощью проверок доступа Azure AD см. в статье [Создание проверки доступа для групп или приложений в проверках доступа Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) .

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Новые Федеративные приложения, доступные в коллекции приложений Azure AD — Февраль 2019

**Тип:** Новая функция  
**Категория службы:** корпоративные приложения  
**Возможности продукта:** Интеграция сторонних продуктов
 
В феврале 2019 мы добавили 27 новых приложений с поддержкой Федерации в коллекцию приложений:

[Еуромонитор Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [миндтиккле](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [finger FAT](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [аирстакк](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [Идриве](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [скивард кмлатив](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [бригхтидеа](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [алертопс](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [солоинсигхт-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Разрешение Click, [брандфолдер](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [сторегатесмартфиле](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [пексип](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [стормбоард](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [пластового](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [Поделитесь с мечты](https://www.shareadream.org/how-it-works), [бугснаг](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [облаком интеграции](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial)с помощью WebMethod, [База знаний В любой точке LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [кампусе подразделения](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [Periscope данных](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [портале Нетоп](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [Smartvid.IO](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [пуреклауд по женесис](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [платформе производительности кликкуп](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Улучшенная общая регистрация MFA/SSPR

**Тип:** Измененная функция  
**Категория службы:** Самостоятельный сброс пароля  
**Возможности продукта:** Проверка подлинности пользователя

В ответ на Отзывы клиентов мы улучшили общий предварительный просмотр регистрации MFA/SSPR, помогая пользователям быстрее регистрировать свои сведения о безопасности для MFA и SSPR. 

**Чтобы включить улучшенный интерфейс для пользователей, выполните следующие действия.**

1. В качестве глобального администратора или администратора пользователей войдите в портал Azure и перейдите в раздел **Azure Active Directory > Параметры пользователя > Управление параметрами для функций предварительной версии панели доступа**. 

2. В области **Пользователи, которые могут использовать функции предварительной версии для регистрации и управления сведениями о безопасности — обновление** выберите Включение компонентов для **выбранной группы пользователей** или для **всех пользователей**.

В течение следующих нескольких недель мы удалим возможность включить старый общий просмотр регистрации MFA/SSPR для клиентов, которые еще не включены.

**Чтобы узнать, будет ли элемент управления удален для клиента, выполните следующие действия.**

1. В качестве глобального администратора или администратора пользователей войдите в портал Azure и перейдите в раздел **Azure Active Directory > Параметры пользователя > Управление параметрами для функций предварительной версии панели доступа**.  

2. Если **пользователям, которые могут использовать функции предварительной версии для регистрации и управления сведениями о безопасности** , не задано значение **нет**, этот параметр будет удален из клиента.

Независимо от того, был ли ранее включен старый общий просмотр регистрации MFA/SSPR для пользователей или нет, старый интерфейс будет отключен в будущем. Поэтому настоятельно рекомендуется как можно быстрее перейти на новый, расширенный опыт.

Дополнительные сведения о расширенных возможностях регистрации см. [в этой статье](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Обновленные возможности управления политиками для потоков пользователей

**Тип:** Измененная функция  
**Категория службы:** B2C — управление удостоверениями потребителей  
**Возможности продукта.** B2B/B2C

Мы обновили процесс создания и управления политикой для потоков пользователей (ранее назывались встроенными политиками). Этот новый интерфейс теперь используется по умолчанию для всех клиентов Azure AD.

Вы можете предоставить дополнительные отзывы и предложения с помощью значков "смайлик" или "нахмуренный смайлик" в области **Отправить отзыв** в верхней части экрана портала.

Дополнительные сведения о новых возможностях управления политиками см. в разделе [Azure AD B2C теперь есть настройка JavaScript, а](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) также блог о многих новых функциях.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Выбор конкретных версий элементов страницы, предоставляемых Azure AD B2C

**Тип:** Новая функция  
**Категория службы.** B2C — управление удостоверениями потребителей  
**Возможности продукта.** B2B/B2C

Теперь можно выбрать конкретную версию элементов страницы, предоставляемых Azure AD B2C. Выбрав конкретную версию, вы сможете проверить обновления, прежде чем они появятся на странице, и получить предсказуемое поведение. Кроме того, теперь можно принять участие в принудительном применении конкретных версий страниц, чтобы разрешить настройку JavaScript. Чтобы включить эту функцию, перейдите на страницу **свойств** в потоке пользователя.

Дополнительные сведения о выборе конкретных версий элементов страницы см. в разделе [Azure AD B2C теперь есть настройка JavaScript и еще несколько новых функций](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) .

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Настраиваемые требования к паролю для конечных пользователей для B2C (GA)

**Тип:** Новая функция  
**Категория службы.** B2C — управление удостоверениями потребителей  
**Возможности продукта.** B2B/B2C

Теперь вы можете настроить сложность пароля организации для конечных пользователей, а не использовать собственную политику паролей Azure AD. В колонке **свойств** потоков пользователя (ранее известные как встроенные политики) можно выбрать сложность пароля ( **простой** или **высокий**) или создать **Пользовательский** набор требований.

Дополнительные сведения о конфигурации требования к сложности пароля см. [в разделе Настройка требований к сложности паролей в Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Новые шаблоны по умолчанию для пользовательских интерфейсов проверки подлинности

**Тип:** Новая функция  
**Категория службы.** B2C — управление удостоверениями потребителей  
**Возможности продукта.** B2B/B2C

Вы можете использовать наши новые шаблоны по умолчанию, расположенные в колонке **макеты страниц** пользовательских потоков (ранее известные как встроенные политики), чтобы создать пользовательскую процедуру проверки подлинности для пользователей.

Дополнительные сведения об использовании шаблонов см. в разделе [Azure AD B2C теперь содержит настройки JavaScript и множество новых функций](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---
