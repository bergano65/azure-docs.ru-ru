---
title: Новые возможности Заметки о выпуске Azure Active Directory | Документация Майкрософт
description: Узнайте о новых возможностях Azure Active Directory, включая последние заметки о выпуске, известные проблемы, исправления ошибок, нерекомендуемые функции и предстоящие изменения.
services: active-directory
author: msaburnley
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f196c21869f4a2f12e28bc4d6c42b8c945ddde71
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85603678"
---
# <a name="whats-new-in-azure-active-directory"></a>Новые возможности Azure Active Directory

>Получите уведомления о том, когда следует повторно посетить эту страницу для получения обновлений, скопировав и вставляя этот URL-адрес `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` в ![ значок модуля чтения RSS — ](./media/whats-new/feed-icon-16x16.png) средство чтения канала.

Azure AD усовершенствуется на постоянной основе. Чтобы вы оставались в курсе последних разработок, в этой статье предоставлены такие сведения:

- Последние выпуски.
- Известные проблемы
- Исправления ошибок
- Нерекомендуемые функции.
- Планы по изменениям.

Эта страница обновляется ежемесячно, поэтому регулярно пересматривайте ее. Если вам понадобятся материалы, которым более шести месяцев, их можно найти в статье [Архив сведений о новых возможностях в Azure Active Directory](whats-new-archive.md).

---

## <a name="june-2020"></a>июнь 2020 г. 

### <a name="user-risk-condition-in-conditional-access-policy"></a>Условие риска пользователя в политике условного доступа

**Тип.** Планирование изменений.  
**Категория службы:** Условный доступ  
**Возможности продукта.** Безопасность и защита удостоверений.
 

Поддержка пользовательских рисков в политике условного доступа Azure AD позволяет создавать несколько политик на основе рисков. Для разных пользователей и приложений могут потребоваться разные уровни риска для пользователей. В зависимости от рисков пользователей можно создавать политики для блокирования доступа, требовать многофакторную проверку подлинности, защищенную смену пароля или перенаправлять Microsoft Cloud App Security для принудительного применения политики сеанса, например для дополнительного аудита.

Условие риска пользователя требует Azure AD Premium P2, так как в нем используется защита идентификации Azure, которая является предложением P2. Дополнительные сведения об условном доступе см. в [документации по условному доступу Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/).

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>Единый вход SAML теперь поддерживает приложения, требующие установки Спнамекуалифиер при запросе

**Тип.** Исправление.  
**Категория службы:** Корпоративные приложения  
**Возможности продукта.** Единый вход.
 
Для некоторых приложений SAML требуется, чтобы Спнамекуалифиер возвращался в субъекте утверждения по запросу. Теперь Azure AD правильно реагирует на запрос Спнамекуалифиер в политике NameID запросов. Это также работает для входа, инициированного поставщиком услуг, и IdP выполняет процедуру входа.  Дополнительные сведения о протоколе SAML в Azure Active Directory см. в разделе [протокол SAML единого входа](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol).

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Служба совместной работы Azure AD B2B поддерживает приглашение пользователей MSA и Google в клиентах Azure для государственных организаций.

**Тип.** Новая функция.  
**Категория службы.** B2B.  
**Возможности продукта.** B2B и B2C.
 

Клиенты Azure для государственных организаций, использующие функции совместной работы B2B, теперь могут приглашать пользователей с учетной записью Microsoft или Google. Чтобы узнать, может ли клиент использовать эти возможности, следуйте инструкциям на странице [как узнать, доступна ли служба совместной работы B2B в моем клиенте Azure для государственных организаций США?](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>Объект User в MS Graph v1 теперь включает свойства Екстерналусерстате и Екстерналусерстатечанжеддатетиме

**Тип.** Новая функция.  
**Категория службы.** B2B.  
**Возможности продукта.** B2B и B2C.
 

Свойства Екстерналусерстате и Екстерналусерстатечанжеддатетиме можно использовать для поиска приглашенных гостей B2B, которые еще не приняли приглашения, а также автоматизации сборки, например удаления пользователей, которые не приняли приглашения через некоторое количество дней. Теперь эти свойства доступны в MS Graph v1. Рекомендации по использованию этих свойств см. в разделе [тип ресурса пользователя](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0).
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Управление сеансами аутентификации в условном доступе Azure AD теперь общедоступно

**Тип.** Новая функция.  
**Категория службы:** Условный доступ  
**Возможности продукта.** Безопасность и защита удостоверений.
 
Возможности управления сеансами проверки подлинности позволяют настроить, как часто пользователи должны предоставлять учетные данные для входа и должны ли они предоставлять учетные данные после закрытия и повторного открытия браузеров, чтобы обеспечить более высокую безопасность и гибкость в среде.
 
Кроме того, управление сеансами проверки подлинности применяется только к первой факторной проверке подлинности при присоединении к Azure AD, присоединенной гибридной службе Azure AD и зарегистрированным устройствам Azure AD. Теперь управление сеансами проверки подлинности будет применяться и к MFA. Дополнительные сведения см. [в статье Настройка управления сеансами проверки подлинности с помощью условного доступа](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Новые Федеративные приложения, доступные в коллекции приложений Azure AD — Июнь 2020

**Тип.** Новая функция.  
**Категория службы:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
В июне 2020 мы добавили в коллекцию приложений следующие 29 новых приложений с поддержкой федерации:

[Shopify Plus](https://docs.microsoft.com/azure/active-directory/saas-apps/shopify-plus-tutorial), [екарда](https://docs.microsoft.com/azure/active-directory/saas-apps/ekarda-tutorial), [маилгатес](https://docs.microsoft.com/azure/active-directory/saas-apps/mailgates-tutorial), [буллсэйетдп](https://docs.microsoft.com/azure/active-directory/saas-apps/bullseyetdp-tutorial), [ракета](https://docs.microsoft.com/azure/active-directory/saas-apps/raketa-tutorial), [сегмент](https://docs.microsoft.com/azure/active-directory/saas-apps/segment-tutorial), [аудитор искусственного интеллекта](https://www.mindbridge.ai/products/ai-auditor/), [побука Connect](https://app.pobu.ca/), [proto.IO](https://docs.microsoft.com/azure/active-directory/saas-apps/proto.io-tutorial), [привратник](https://www.gatekeeperhq.com/), [планировщик концентратора](https://docs.microsoft.com/azure/active-directory/saas-apps/hub-planner-tutorial), [ансира — панель элементов «переход на рынке](https://ansira.com/technology/channel-engagement)», [Автоматизация цифрового бизнеса в облаке](https://docs.microsoft.com/azure/active-directory/saas-apps/ibm-digital-business-automation-on-cloud-tutorial), [Киси физическая безопасность](https://docs.microsoft.com/azure/active-directory/saas-apps/kisi-physical-security-tutorial), [виевпоинтоне](https://team.viewpoint.com/), [интеллиженцебанк](https://docs.microsoft.com/azure/active-directory/saas-apps/intelligencebank-tutorial), [Pymetrics](https://docs.microsoft.com/azure/active-directory/saas-apps/pymetrics-tutorial), [ноль](https://www.teamzero.com/), [станция](https://instation.invillia.com/), [edX для интеграции SAML 2,0](https://docs.microsoft.com/azure/active-directory/saas-apps/edx-for-business-saml-integration-tutorial), [mooc Office 365](https://mooc.office365-training.com/en/), SmartKargo, [PKIsigning Platform](https://platform.pkisigning.nl/), [SiteIntel](https://docs.microsoft.com/azure/active-directory/saas-apps/siteintel-tutorial), [идентификатор поля](https://docs.microsoft.com/azure/active-directory/saas-apps/field-id-tutorial), учебные программы [SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/curricula-saml-tutorial) [, Perforce](https://docs.microsoft.com/azure/active-directory/saas-apps/smartkargo-tutorial) [Helix Core-Helix служба проверки подлинности](https://docs.microsoft.com/azure/active-directory/saas-apps/perforce-helix-core-tutorial), [MyCompliance](https://cloud.metacompliance.com/)- [SSH](https://smallstep.com/sso-ssh/)  

Документацию по всем приложениям можно также найти здесь https://aka.ms/AppsTutorial . Чтобы получить список приложений в коллекции приложений Azure AD, ознакомьтесь со сведениями в этой статье: https://aka.ms/AzureADAppRequest .

---

### <a name="api-connectors-for-external-identities-self-service-sign-up-are-now-in-public-preview"></a>Соединители API для внешних удостоверений самостоятельная регистрация в общедоступной предварительной версии

**Тип.** Новая функция.  
**Категория службы.** B2B.  
**Возможности продукта.** B2B и B2C.
 
Соединители API внешних удостоверений позволяют использовать веб-API для интеграции самостоятельной регистрации с внешними облачными системами. Это означает, что теперь вы можете вызывать веб-API как определенные шаги в потоке регистрации для активации настраиваемых рабочих процессов на основе облака. Например, соединители API можно использовать для:

- Интеграция с настраиваемыми рабочими процессами утверждения.
- Проверка идентификации
- Проверка входных данных пользователя
- Перезаписать атрибуты пользователя
- Выполнение настраиваемой бизнес-логики

Дополнительные сведения обо всех возможностях соединителей API см. в [статьях Использование соединителей API для настройки и расширения самостоятельной регистрации](https://docs.microsoft.com/azure/active-directory/b2b/api-connectors-overview)или [Настройка внешних удостоверений самостоятельная регистрация с помощью интеграции веб-API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin).
 
---

### <a name="provision-on-demand-and-get-users-into-your-apps-in-seconds"></a>Подготавливайте приложения по запросу и получайте пользователей в приложениях за считаные секунды

**Тип.** Новая функция.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта:** Управление жизненным циклом удостоверений
 
Служба подготовки Azure AD в настоящее время работает на циклической основе. Служба выполняется каждые 40 мин. [Возможность подготовки по требованию](https://aka.ms/provisionondemand) позволяет выбрать пользователя и подготовить его в течение нескольких секунд. Эта возможность позволяет быстро устранять проблемы, связанные с подготовкой, без необходимости перезапуска, чтобы принудительно запустить цикл подготовки. 
 
---

### <a name="new-permission-for-using-azure-ad-entitlement-management-in-graph"></a>Новое разрешение для использования управления назначениями Azure AD в Graph

**Тип.** Новая функция.  
**Категория службы.** Другая.  
**Возможности продукта:** Управление назначениями
 
Новое делегированное разрешение Ентитлементманажемент. Read. все теперь доступно для использования с API управления назначением в бета-версии Microsoft Graph. Дополнительные сведения о доступных API см. в статье [Работа с API управления назначением Azure AD](https://docs.microsoft.com/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta).

---

### <a name="identity-protection-apis-available-in-v10"></a>API-интерфейсы защиты идентификации, доступные в версии 1.0

**Тип.** Новая функция.  
**Категория службы:** Защита идентификации  
**Возможности продукта.** Безопасность и защита удостоверений.
 
Интерфейсы API Microsoft Graph Рискюсерс и Рискдетектионс теперь общедоступны. Теперь, когда они доступны в конечной точке версии 1.0, мы приглашаем вас использовать их в рабочей среде. Дополнительные сведения см. в документации по [Microsoft Graph](https://docs.microsoft.com/graph/api/resources/identityprotectionroot?view=graph-rest-1.0).
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Метки чувствительности к применению политик к Microsoft 365 группам теперь являются общедоступными

**Тип.** Новая функция.  
**Категория службы.** Управление группами.  
**Возможности продукта.** Совместная работа.
 

Теперь можно создавать метки чувствительности и использовать параметры меток для применения политик к Microsoft 365 группам, включая политику конфиденциальности (общедоступные или частные) и внешние пользователи. Можно создать метку с политикой конфиденциальности "частный", а для политики "внешний доступ пользователей" — запретить добавление гостевых пользователей. Когда пользователь применяет эту метку к группе, группа будет закрыта, а гостевые пользователи не смогут добавляться в эту группу. 

Метки чувствительности важны для защиты критически важных для бизнеса данных и позволяют управлять группами в нужном масштабе в соответствии с требованиями и безопасным способом. Рекомендации по использованию меток конфиденциальности см. в разделе [Назначение меток чувствительности группам Office 365 в Azure Active Directory (Предварительная версия)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-assign-sensitivity-labels).
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>Обновления для поддержки Microsoft Identity Manager для клиентов Azure AD Premium

**Тип.** Измененная функция.  
**Категория службы:** Microsoft Identity Manager  
**Возможности продукта:** Управление жизненным циклом удостоверений
 
Поддержка Azure теперь доступна для компонентов интеграции Azure AD Microsoft Identity Manager 2016 до окончания расширенной поддержки Microsoft Identity Manager 2016. Дополнительные сведения см. в статье [Поддержка обновлений для Azure AD Premium клиентов с помощью Microsoft Identity Manager](https://docs.microsoft.com/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers).

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>Увеличивается использование условий членства в группах в конфигурации утверждений единого входа.

**Тип.** Измененная функция.  
**Категория службы:** Корпоративные приложения  
**Возможности продукта.** Единый вход.
 
Ранее количество групп, которые можно было использовать при условии изменения утверждений на основе членства в группе в пределах одной конфигурации приложения, было ограничено 10. Использование условий членства в группах в конфигурации утверждений SSO теперь увеличилось до 50 групп. Дополнительные сведения о настройке утверждений см. в статье [Настройка утверждений единого входа для корпоративных приложений](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#emitting-claims-based-on-conditions). 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>Включение базового форматирования для компонента текста страницы входа в фирменной символике компании.

**Тип.** Измененная функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Проверка подлинности пользователя
 
Функция фирменной символики компании в процессе входа в Azure AD или Microsoft 365 была обновлена, чтобы позволить клиенту добавлять гиперссылки и простое форматирование, включая полужирный шрифт, подчеркивание и курсив. Рекомендации по использованию этой функции см. в статье [Добавление фирменной символики на страницу входа Azure Active Directory вашей организации](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="provisioning-performance-improvements"></a>Повышение производительности при подготовке

**Тип.** Измененная функция.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта:** Управление жизненным циклом удостоверений
 
Служба подготовки была обновлена, чтобы сократить время выполнения [добавочного цикла](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#incremental-cycles) . Это означает, что пользователи и группы будут подготавливаться к приложениям быстрее, чем раньше. Все новые задания подготовки, созданные после 6/10/2020, будут автоматически использовать преимущества повышения производительности. Все приложения, настроенные для подготовки до 6/10/2020, потребуется перезапустить один раз после 6/10/2020, чтобы воспользоваться преимуществами повышения производительности. 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>Объявление о нерекомендуемой четности ADAL и MS Graph

**Тип.** Прекращение поддержки.  
**Категория службы:** недоступно  
**Возможности продукта:** Управление жизненным циклом устройств

Теперь, когда доступны библиотеки проверки подлинности Майкрософт (MSAL), мы больше не будем добавлять новые функции в библиотеки Azure Active Directory проверки подлинности (ADAL) и завершать исправления безопасности до 30 июня 2022. Дополнительные сведения о миграции в MSAL см. в статье [Миграция приложений в библиотеку проверки подлинности Майкрософт (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-migration).

Кроме того, мы завершили работу, чтобы сделать все функции Graph Azure AD доступными через MS Graph. Таким образом, API Graph Azure AD получат только исправления бугфикс и безопасности до 30 июня 2022 г. Дополнительные сведения см. [в статье обновление приложений для использования библиотеки проверки подлинности Майкрософт и Microsoft Graph API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363) .
 
---
 
## <a name="may-2020"></a>Май 2020 г.

### <a name="retirement-of-properties-in-signins-riskyusers-and-riskdetections-apis"></a>Выбытие свойств в API-интерфейсах обнаружено, Рискюсерс и Рискдетектионс

**Тип.** Планирование изменений.  
**Категория службы:** Защита идентификации  
**Возможности продукта.** Безопасность и защита удостоверений.

В настоящее время перечислимые типы используются для представления свойства Рисктипе в API Рискдетектионс и Рискюсерхисторитем (в предварительной версии). Перечислимые типы также используются для свойства Рискевенттипес в API обнаружено. Перейдем, мы представляем эти свойства в виде строк. 

Клиенты должны перейти к свойству Рискевенттипе в API бета-версии Рискдетектионс и Рискюсерхисторитем, а также riskEventTypes_v2 свойству в API бета-версии обнаружено до 9 сентября 2020. В этот день мы будем исснятся с учета текущих свойств Рисктипе и Рискевенттипес. Дополнительные сведения см. в разделе [изменения свойств событий риска и API-интерфейсов защиты идентификации на Microsoft Graph](https://developer.microsoft.com/graph/blogs/changes-to-risk-event-properties-and-identity-protection-apis-on-microsoft-graph/).

--- 

### <a name="deprecation-of-riskeventtypes-property-in-signins-v10-api-on-microsoft-graph"></a>Нерекомендуемое свойство Рискевенттипес в API обнаружено v 1.0 на Microsoft Graph

**Тип.** Планирование изменений.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Безопасность и защита удостоверений.

Перечисленные типы переключаются в строковые типы, когда представляют свойства событий риска в Microsoft Graph 2020 сентября. В дополнение к влиянию на API предварительного просмотра это изменение также повлияет на обнаружено API в рабочей среде.

Мы предоставили новое свойство riskEventsTypes_v2 (String) для API обнаружено v 1.0. Мы будем снимать текущее свойство Рискевенттипес (enum) с 11 июня 2022 в соответствии с политикой устаревания Microsoft Graph. Клиенты должны перейти к свойству riskEventTypes_v2 в API v 1.0 обнаружено до 11 июня 2022 г. Дополнительные сведения см. в разделе [устаревшее свойство рискевенттипес в API обнаружено v 1.0 на Microsoft Graph](https://developer.microsoft.com/graph/blogs/deprecation-of-riskeventtypes-property-in-signins-v1-0-api-on-microsoft-graph//).

--- 

### <a name="upcoming-changes-to-mfa-email-notifications"></a>Предстоящие изменения в уведомлениях по электронной почте MFA

**Тип.** Планирование изменений.  
**Категория службы:** MFA  
**Возможности продукта.** Безопасность и защита удостоверений.
 

Мы делаем следующие изменения в уведомлениях по электронной почте MFA для Cloud MFA и сервера MFA.

Уведомления по электронной почте будут отправляться со следующего адреса: azure-noreply@microsoft.com .
Мы обновляем содержимое сообщений электронной почты с оповещениями о мошенничестве, чтобы лучше указать необходимые действия для разблокирования использования.

---

### <a name="new-self-service-sign-up-for-users-in-federated-domains-who-cant-access-microsoft-teams-because-they-arent-synced-to-azure-active-directory"></a>Новая самостоятельная регистрация для пользователей в федеративных доменах, которые не могут получить доступ к Microsoft Teams, так как они не синхронизированы с Azure Active Directory.

**Тип.** Планирование изменений.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Проверка подлинности пользователя
 

В настоящее время пользователи, которые находятся в доменах, Федеративных в Azure AD, но не синхронизированы с клиентом, не могут получить доступ к командам. Начиная с конца июня, эта новая возможность позволит им сделать это путем расширения существующей функции регистрации электронной почты с проверкой подлинности. Это позволит пользователям, которые могут войти в федеративный IdP, но у кого еще нет объекта пользователя в Azure ID, автоматически создать пользовательский объект и пройти проверку подлинности для команд. Их объект «пользователь» будет помечен как «самостоятельная регистрация». Это расширение существующей возможности для проверки электронной почты с возможностью самостоятельной регистрации, которое могут выполнять пользователи в управляемых доменах и которыми можно управлять с помощью одного и того же флага. Это изменение будет выполнено в течение следующих двух месяцев. Ознакомьтесь с обновлениями документации [здесь](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup).
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>Предстоящее исправление. документ обнаружения OIDC для облака Azure для государственных организаций обновляется для ссылки на правильные конечные точки Graph.

**Тип.** Планирование изменений.  
**Категория службы.** Национальные облака.  
**Возможности продукта:** Проверка подлинности пользователя
 
Начиная с июня, документ OIDC Discovery, [а также протокол OpenID Connect Connect](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc) в [облачной конечной точке Azure для государственных организаций](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) (login.microsoftonline.US) начинает возвращать корректную конечную точку [национального облака](https://docs.microsoft.com/graph/deployments) ( https://graph.microsoft.us или, в зависимости от https://dod-graph.microsoft.us) предоставленного клиента).  В настоящее время он предоставляет неправильное поле конечной точки графа (graph.microsoft.com) "msgraph_host".  

Это исправление ошибки будет сведено постепенно в течение приблизительно 2 месяцев.  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Пользователи Azure для государственных организаций больше не смогут входить в login.microsoftonline.com

**Тип:** Планирование изменений  
**Категория службы.** Национальные облака.  
**Возможности продукта:** Проверка подлинности пользователя
 
В 1 июня 2018 г. Официальный центр Azure Active Directory (AAD) для государственных организаций Azure изменился с https://login-us.microsoftonline.com на https://login.microsoftonline.us . Если вы владеете приложением в клиенте Azure для государственных организаций, необходимо обновить приложение для входа пользователей в систему в конечной точке. US.

Начиная с 5 мая, Azure AD начнет принудительно изменять конечную точку, блокируя пользователям Azure для государственных организаций вход в приложения, размещенные в клиентах Azure для государственных организаций, с помощью общедоступной конечной точки (microsoftonline.com). Затронутые приложения будут видеть ошибку AADSTS900439-Усгклиентнотсуппортедонпублицендпоинт. 

Это изменение будет постепенно постепенным, и предполагается, что ожидается завершение всех приложений за июнь 2020. Дополнительные сведения см. в записи [блога Azure для государственных организаций](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/).

---

### <a name="saml-single-logout-request-now-sends-nameid-in-the-correct-format"></a>Теперь запрос единого выхода SAML отправляет NameID в правильном формате

**Тип.** Исправление.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Проверка подлинности пользователя
 
Когда пользователь щелкает выход (например, на портале MyApps), Azure AD отправляет сообщение SAML Single Logout в каждое приложение, которое активно в сеансе пользователя и имеет настроенный URL-адрес выхода. Эти сообщения содержат NameID в постоянном формате.

Если исходный маркер входа SAML использует другой формат для NameID (например, адрес электронной почты или имя участника-пользователя), приложение SAML не может сопоставить NameID в сообщении выхода с существующим сеансом (так как Намеидс используется в обоих сообщениях), что привело к тому, что сообщение о выходе из системы отклоняется приложением SAML и пользователь остается в системе. Это исправление делает сообщение о выходе в соответствии с NameID, настроенным для приложения.

---

### <a name="hybrid-identity-administrator-role-is-now-available-with-cloud-provisioning"></a>Роль администратора гибридной идентификации теперь доступна при подготовке облака.

**Тип.** Новая функция.  
**Категория службы:** Подготовка облака Azure AD  
**Возможности продукта:** Управление жизненным циклом удостоверений
 
ИТ – администраторы могут начать использовать новую роль гибридного администратора в качестве наименее привилегированной роли для настройки подготовки облака Azure Адконнект. С этой новой ролью вам больше не нужно использовать роль глобального администратора для настройки и настройки подготовки облака. [Подробнее.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-delegate-by-task#connect)
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Новые Федеративные приложения, доступные в коллекции приложений Azure AD — 2020 мая

**Тип.** Новая функция.  
**Категория службы:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
В мае 2020 мы добавили следующие 36 новых приложений в коллекцию приложений с поддержкой федерации:

[Маула](https://moula.com.au/pay/merchants), [сурвэйпал](https://www.surveypal.com/app), [Kbot365](https://www.konverso.ai/virtual-assistant-for-digital-workplace/), [Такклебокс](http://www.tacklebox.app/), [Пауэл Teams](https://powell-software.com/en/powell-teams-en/), [талентсофт Assistant](https://msteams.talent-soft.com/), [ASC Record Insights](https://teams.asc-recording.app/product), [GO1](https://www.go1.com/), [B-](https://b-engaged.se/)on, [компетелла Contact Center Workgroup](http://www.competella.com/), [Асите](http://www.asite.com/), [удостоверение имажесофт](https://identity.imagesoftinc.com/), [Моя ибисворлд](https://identity.imagesoftinc.com/), [Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/insuite-tutorial), [Управление процессами](https://docs.microsoft.com/azure/active-directory/saas-apps/change-process-management-tutorial), [циара CX Assurance](https://docs.microsoft.com/azure/active-directory/saas-apps/cyara-cx-assurance-platform-tutorial), [интеллектуальное глобальное управление](https://docs.microsoft.com/azure/active-directory/saas-apps/smart-global-governance-tutorial), [прези](https://docs.microsoft.com/azure/active-directory/saas-apps/prezi-tutorial), [MAPBOX](https://docs.microsoft.com/azure/active-directory/saas-apps/mapbox-tutorial), [Корпоративная платформа служб Datava](https://docs.microsoft.com/azure/active-directory/saas-apps/datava-enterprise-service-platform-tutorial), [замысловатое](https://docs.microsoft.com/azure/active-directory/saas-apps/whimsical-tutorial), [Trelica](https://docs.microsoft.com/azure/active-directory/saas-apps/trelica-tutorial), [EasySSO для Confluence](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-confluence-tutorial), [EasySSO для BitBucket](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-bitbucket-tutorial), [EasySSO для Bamboo](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-bamboo-tutorial) [, torii, Axiad](https://docs.microsoft.com/azure/active-directory/saas-apps/torii-tutorial)Cloud [,, ColorTokens](https://docs.microsoft.com/azure/active-directory/saas-apps/humanage-tutorial), [ZTNA,](https://docs.microsoft.com/azure/active-directory/saas-apps/colortokens-ztna-tutorial) [все домашние CRM](https://docs.microsoft.com/azure/active-directory/saas-apps/anyone-home-crm-tutorial) [ice Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/ice-contact-center-tutorial) , [CCH](https://docs.microsoft.com/azure/active-directory/saas-apps/askspoke-tutorial) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/sharevault-tutorial) [Tagetik](https://docs.microsoft.com/azure/active-directory/saas-apps/vyond-tutorial) [, ShareVault](https://docs.microsoft.com/azure/active-directory/saas-apps/textexpander-tutorial). [Axiad Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/axiad-cloud-tutorial) [CCH Tagetik](https://docs.microsoft.com/azure/active-directory/saas-apps/cch-tagetik-tutorial)

Документацию по всем приложениям можно также найти здесь https://aka.ms/AppsTutorial .

Чтобы получить список приложений в коллекции приложений Azure AD, ознакомьтесь с подробными сведениями https://aka.ms/AzureADAppRequest .

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>Режим "только отчет" для условного доступа теперь является общедоступным

**Тип.** Новая функция.  
**Категория службы:** Условный доступ  
**Возможности продукта.** Безопасность и защита удостоверений.

[Режим "только отчет" для условного доступа Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only) позволяет оценить результат политики без применения элементов управления доступом. Вы можете протестировать политики только для отчетов в Организации и понять их влияние, прежде чем включать их, делая развертывание более безопасным и легким. В течение последних нескольких месяцев мы видели строгое внедрение режима только для отчетов — пользователи 26M уже находятся в области политики только для отчетов. С объявлением сейчас новые политики условного доступа Azure AD будут создаваться в режиме «только отчет» по умолчанию. Это означает, что вы можете отслеживать влияние политик с момента их создания. А для тех, кто использует API-интерфейсы MS Graph, также можно [управлять политиками только для отчетов программным способом](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta) . 

---

### <a name="self-service-sign-up-for-guest-users"></a>Самостоятельная регистрация для гостевых пользователей

**Тип.** Новая функция.  
**Категория службы.** B2B.  
**Возможности продукта.** B2B и B2C.
 
С помощью внешних удостоверений в Azure AD можно разрешить пользователям за пределами организации получать доступ к вашим приложениям и ресурсам, одновременно обеспечивая им возможность входа с использованием любого предпочтительного идентификатора. При совместном использовании приложения с внешними пользователями вы не всегда можете знать заранее, кому потребуется к нему доступ. С помощью [самостоятельной регистрации](https://docs.microsoft.com/azure/active-directory/b2b/self-service-sign-up-overview)вы можете включить гостевых пользователей для регистрации и получения гостевой учетной записи для бизнес-приложений. Последовательность регистрации может быть создана и настроена для поддержки удостоверений Azure AD и социальных сетей. Вы также можете получить дополнительные сведения о пользователе во время регистрации.

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Книга "аналитика и отчеты" условного доступа является общедоступной

**Тип.** Новая функция.  
**Категория службы:** Условный доступ  
**Возможности продукта.** Безопасность и защита удостоверений.

[Книга Insights и Reporting](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting) предоставляет администраторам сводное представление условного доступа Azure AD в своем клиенте. Возможность выбора отдельной политики позволяет администраторам лучше понять, что делает каждая политика, и отслеживать любые изменения в режиме реального времени. Книга отправляет данные, хранящиеся в Azure Monitor, которые можно настроить через несколько минут, [следуя этим инструкциям](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics). Чтобы сделать панель мониторинга более обнаруживаемой, мы перенесли ее на новую вкладку Insights and Reports в меню условного доступа Azure AD.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Колонка сведений о политике для условного доступа в общедоступной предварительной версии

**Тип.** Новая функция.  
**Категория службы:** Условный доступ  
**Возможности продукта.** Безопасность и защита удостоверений.

В [колонке новая информация о политике](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access) отображаются назначения, условия и элементы управления, удовлетворяющие условиям оценки политики условного доступа. Вы можете получить доступ к колонке, выбрав строку на вкладках условный доступ или только отчет на вкладке сведения о входе.

---

### <a name="new-query-capabilities-for-directory-objects-in-microsoft-graph-are-in-public-preview"></a>Новые возможности запросов для объектов каталога в Microsoft Graph доступны в общедоступной предварительной версии

**Тип.** Новая функция.  
**Категория службы:** **Возможности продукта** MS Graph: взаимодействие с разработчиками

Добавлены новые возможности для API Microsoft Graph объектов каталога, позволяющие выполнять операции подсчета, поиска, фильтрации и сортировки. Это дает разработчикам возможность быстро выполнять запросы к объектам каталога без таких решений, как фильтрация в памяти и сортировка. Дополнительные сведения см. в этой [записи блога](https://aka.ms/CountFilterMSGraphAAD).

Сейчас в общедоступной предварительной версии вы ищете отзывы. Отправьте свои комментарии с помощью этого [краткого опроса](https://aka.ms/MsGraphAADSurveyDocs).

---

### <a name="configure-saml-based-single-sign-on-using-microsoft-graph-api-beta"></a>Настройка единого входа на основе SAML с помощью Microsoft Graph API (бета-версия)

**Тип.** Новая функция.  
**Категория службы:** Корпоративные приложения  
**Возможности продукта.** Единый вход.
 
Теперь доступна поддержка создания и настройки приложения из коллекции Azure AD с помощью API-интерфейсов MS Graph. Если необходимо настроить единый вход на основе SAML для нескольких экземпляров приложения, сэкономьте время с помощью Microsoft Graph API-интерфейсов для [автоматизации настройки единого входа на основе SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/application-saml-sso-configure-api).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Новые соединители подготовки в коллекции приложений Azure AD — Май 2020

**Тип.** Новая функция.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
Теперь вы можете автоматизировать процессы создания, обновление и удаление учетных записей пользователей для следующих новых интегрированных приложений:

* [8x8](https://docs.microsoft.com/azure/active-directory/saas-apps/8x8-provisioning-tutorial)
* [Juno Journey](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-provisioning-tutorial)
* [MediusFlow](https://docs.microsoft.com/azure/active-directory/saas-apps/mediusflow-provisioning-tutorial)
* [Использование New Relic с разбивкой по организацией](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-by-organization-provisioning-tutorial)
* [Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-infratstructure-console-provisioning-tutorial)

Дополнительные сведения об усилении защиты организации с помощью автоматизированной подготовки учетных записей пользователей см. в статье [Автоматическая подготовка пользователей для приложений SaaS в Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="saml-token-encryption-is-generally-available"></a>Шифрование токена SAML является общедоступным

**Тип.** Новая функция.  
**Категория службы:** Корпоративные приложения  
**Возможности продукта.** Единый вход.
 
[Шифрование токенов SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption) позволяет настроить приложения для получения зашифрованных утверждений SAML. Теперь эта функция доступна во всех облаках.
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>Утверждения имени группы в маркерах приложений являются общедоступными

**Тип.** Новая функция.  
**Категория службы:** Корпоративные приложения  
**Возможности продукта.** Единый вход.
 
Утверждения группы, выданные в маркере, теперь можно ограничить только теми группами, которые назначены приложению.  Это особенно важно, когда пользователи являются членами большого количества групп, и существует риск превышения предельного размера маркера. Благодаря этой новой возможности возможность [добавлять имена групп в токены](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims) общедоступна.
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>Обратная запись Workday теперь поддерживает задание атрибутов номера рабочего телефона

**Тип.** Новая функция.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта:** Управление жизненным циклом удостоверений
 
Мы улучшили приложение подготовки обратной записи Workday, чтобы теперь поддерживали обратную запись номера рабочего телефона и атрибутов мобильных номеров. Помимо адреса электронной почты и имени пользователя, теперь можно настроить приложение подготовки обратной записи Workday для передачи телефонных номеров из Azure AD в Workday. Дополнительные сведения о настройке обратной записи телефонных номеров см. в руководстве по приложению [обратной записи Workday](https://aka.ms/WorkdayWriteback) . 

---

### <a name="publisher-verification-preview"></a>Проверка издателя (Предварительная версия)

**Тип.** Новая функция.  
**Категория службы.** Другая.  
**Возможности продукта.** Возможности для разработчиков
 
Проверка издателя (Предварительная версия) помогает администраторам и конечным пользователям понять подлинность разработчиков приложений, которые интегрируются с платформой идентификации Майкрософт. Дополнительные сведения см. в статье [Проверка издателя (Предварительная версия)](https://docs.microsoft.com/azure/active-directory/develop/publisher-verification-overview).
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>Поток кода авторизации для одностраничных приложений

**Тип:** Измененная **Категория службы компонентов:** возможность проверки подлинности **продукта:** взаимодействие с разработчиками

Из-за современных [сторонних ограничений для файлов cookie браузера, таких как Safari ITP](https://docs.microsoft.com/azure/active-directory/develop/reference-third-party-cookies-spas), одностраничные приложения придется использовать поток кода авторизации, а не неявный поток для поддержки единого входа. MSAL.js версии 2. x теперь будет поддерживать поток кода авторизации. В этом случае соответствующие обновления портал Azure, поэтому вы можете обновить SPA, чтобы он имел тип "Spa", и использовать поток кода проверки подлинности. Инструкции см. в разделе [Краткое руководство. вход пользователей и получение маркера доступа в JavaScript Spa с помощью потока кода проверки подлинности](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript-auth-code).

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>Улучшенная фильтрация для устройств в общедоступной предварительной версии

**Тип:** Измененная функция   
**Категория службы:** **Возможности продукта** для управления устройствами: Управление жизненным циклом устройств
 
Ранее можно было использовать только фильтры "включено" и "Дата действия". Теперь можно [отфильтровать список устройств по большему числу свойств](https://docs.microsoft.com/azure/active-directory/devices/device-management-azure-portal#device-list-filtering-preview), включая тип ОС, тип объединения, соответствие и многое другое. Эти дополнения должны упростить поиск конкретного устройства.

---

### <a name="the-new-app-registrations-experience-for-azure-ad-b2c-is-now-generally-available"></a>Новый интерфейс Регистрация приложений для Azure AD B2C теперь является общедоступным.

**Тип:** Измененная функция   
**Категория службы.** B2C — управление удостоверениями клиентов.  
**Возможности продукта:** Управление жизненным циклом удостоверений
 
Новый интерфейс Регистрация приложений для Azure AD B2C теперь является общедоступным. 

Ранее вам пришлось управлять приложениями B2C, ориентированными на потребителей, отдельно от остальных приложений, используя прежний опыт работы с приложениями. Это означало различные возможности создания приложений в разных местах в Azure.

Новый интерфейс показывает все регистрации приложений B2C и регистрации приложений Azure AD в одном месте и обеспечивает единообразный способ управления ими. Если вам нужно управлять приложением, ориентированным на клиента, или с приложением, которое имеет доступ к Microsoft Graph для программного управления Azure AD B2Cными ресурсами, необходимо только изучить один из способов сделать это.

Вы можете перейти к новому интерфейсу, перейдя в службу Azure AD B2C и выбрав колонку Регистрация приложений. Интерфейс также доступен из службы Azure Active Directory.

Azure AD B2C Регистрация приложений возможности основаны на общем [интерфейсе регистрации приложений](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) для клиентов Azure AD, но адаптированы для Azure AD B2C. Устаревший интерфейс "приложения" в будущем будет считаться устаревшим.

Дополнительные сведения см. [в новом интерфейсе регистрации приложений для Azure AD B2C](https://aka.ms/b2cappregtraining).

---

## <a name="april-2020"></a>Апрель 2020 г.

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>Объединенный интерфейс регистрации сведений о безопасности теперь является общедоступным

**Тип.** Новая функция.

**Категория службы.** Проверки подлинности (имена входа).

**Возможности продукта.** Безопасность и защита удостоверений.

Общий интерфейс регистрации для многофакторной идентификации (MFA) и самостоятельного сброса пароля (SSPR) теперь является общедоступным. Эта новая процедура регистрации позволяет пользователям регистрироваться на MFA и SSPR в одном пошаговом процессе. При развертывании нового интерфейса для вашей организации пользователи могут зарегистрироваться в течение меньшего времени и с меньшим числом трудностей. Ознакомьтесь с этой записью в [блоге.](https://bit.ly/3etiRyQ)

---

### <a name="continuous-access-evaluation"></a>Оценка непрерывного доступа

**Тип.** Новая функция.

**Категория службы.** Проверки подлинности (имена входа).

**Возможности продукта.** Безопасность и защита удостоверений.

Оценка непрерывного доступа — это новая функция безопасности, которая обеспечивает принудительное применение политик в режиме реального времени к проверяющим сторонам, которые потребляют маркеры доступа Azure AD при возникновении событий в Azure AD (например, удаление учетной записи пользователя). Мы разносим эту функцию в первую очередь для команд и клиентов Outlook. Дополнительные сведения см. в нашем [блоге](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) и [документации](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-continuous-access-evaluation).

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>Вход в SMS: работники Фирстлине могут входить в приложения, поддерживающие Azure AD, с помощью номера телефона и пароля.

**Тип.** Новая функция.

**Категория службы.** Проверки подлинности (имена входа).

**Возможности продукта:** Проверка подлинности пользователя

Office запускает серию мобильных бизнес-приложений, предназначенных для нетрадиционных организаций, и сотрудников в крупных организациях, которые не используют электронную почту в качестве основного метода связи. Эти приложения предназначены для сотрудников оказался, сотрудников без службы, агентов полей или сотрудников розничной торговли, которые могут не получить адрес электронной почты от своего работодателя, получить доступ к компьютеру или к нему. Этот проект позволит этим сотрудникам входить в бизнес-приложения, вводя номер телефона и переносе код. Дополнительные сведения см. в документации [администратора](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-sms-signin) и в [документации для конечных пользователей](https://docs.microsoft.com/azure/active-directory/user-help/sms-sign-in-explainer).

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>Пригласите внутренних пользователей использовать службу совместной работы B2B

**Тип.** Новая функция.

**Категория службы.** B2B.

**Возможности продукта:**

Мы расширяем возможности приглашения B2B, чтобы разрешить приглашать существующие внутренние учетные записи для использования учетных данных службы совместной работы B2B. Это делается путем передачи объекта пользователя в API приглашения, а также стандартных параметров, таких как приглашенный адрес электронной почты. Идентификатор объекта пользователя, имя участника-пользователя, членство в группе, назначение приложения и т. п. остаются неизменными, но в дальнейшем они будут использовать B2B для проверки подлинности с учетными данными домашнего клиента вместо внутренних учетных данных, которые они использовали перед приглашением. Дополнительные сведения см. в [документации](https://docs.microsoft.com/azure/active-directory/b2b/invite-internal-users).

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>Режим "только отчет" для условного доступа теперь является общедоступным

**Тип.** Новая функция.

**Категория службы:** Условный доступ

**Возможности продукта.** Безопасность и защита удостоверений.

[Режим "только отчет" для условного доступа Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only) позволяет оценить результат политики без применения элементов управления доступом. Вы можете протестировать политики только для отчетов в Организации и понять их влияние, прежде чем включать их, делая развертывание более безопасным и легким. За последние несколько месяцев мы наблюдали строгое внедрение режима только для отчетов, 26M пользователей уже в области политики только для отчетов. Благодаря этому объявлению новые политики условного доступа Azure AD будут создаваться в режиме «только отчет» по умолчанию. Это означает, что вы можете отслеживать влияние политик с момента их создания. И для тех, кто использует API-интерфейсы MS Graph, можно также [управлять политиками только для отчетов программным способом](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta). 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Книга "аналитика и отчеты" условного доступа является общедоступной

**Тип.** Новая функция.

**Категория службы:** Условный доступ

**Возможности продукта.** Безопасность и защита удостоверений.

[Книга "аналитика и отчеты](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting) " условного доступа предоставляет администраторам сводное представление условного доступа Azure AD в своем клиенте. Возможность выбора отдельной политики позволяет администраторам лучше понять, что делает каждая политика, и отслеживать любые изменения в режиме реального времени. Книга отправляет данные, хранящиеся в Azure Monitor, которые можно настроить через несколько минут, [следуя этим инструкциям](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics). Чтобы сделать панель мониторинга более обнаруживаемой, мы перенесли ее на новую вкладку Insights and Reports в меню условного доступа Azure AD.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Колонка сведений о политике для условного доступа в общедоступной предварительной версии

**Тип.** Новая функция.

**Категория службы:** Условный доступ

**Возможности продукта.** Безопасность и защита удостоверений.

В [колонке новая информация о политике](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access) отображаются назначения, условия и элементы управления, которые были удовлетворены во время оценки политики условного доступа. Вы можете получить доступ к колонке, выбрав строку на вкладках **Условный доступ** или **только отчет** на вкладке сведения о входе.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Новые Федеративные приложения, доступные в Azure AD App Gallery — Апрель 2020

**Тип.** Новая функция.

**Категория службы:** Корпоративные приложения

**Возможности продукта.** Интеграция с решениями сторонних производителей.

В апреле 2020 мы добавили в коллекцию приложений следующие 31 новые приложения с поддержкой федерации: 

[Синкропул Apps](https://www.sincropool.com/), [смартдб](https://hibiki.dreamarts.co.jp/smartdb/trial/), [float](https://docs.microsoft.com/azure/active-directory/saas-apps/float-tutorial), [LMS365](https://lms.365.systems/), [IWT Purchase Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/iwt-procurement-suite-tutorial), [Лунни](https://lunni.fi/), [еасиссо для JIRA](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-jira-tutorial), [виртуальное обучение Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft), [панель мониторинга Meraki](https://docs.microsoft.com/azure/active-directory/saas-apps/meraki-dashboard-tutorial), [Office 365](https://app.mover.io/login), [общение](https://speakerengage.com/login.php), [честно говоря](https://docs.microsoft.com/azure/active-directory/saas-apps/honestly-tutorial), [Ally](https://docs.microsoft.com/azure/active-directory/saas-apps/ally-tutorial), [дутифлов](https://app.dutyflow.nl/), [алертмедиа](https://docs.microsoft.com/azure/active-directory/saas-apps/alertmedia-tutorial), [gr8 люди](https://docs.microsoft.com/azure/active-directory/saas-apps/gr8-people-tutorial), [пендо](https://docs.microsoft.com/azure/active-directory/saas-apps/pendo-tutorial), [хигхграунд](https://docs.microsoft.com/azure/active-directory/saas-apps/highground-tutorial), [гармонии](https://docs.microsoft.com/azure/active-directory/saas-apps/harmony-tutorial), [тиметаблинг Solutions](https://docs.microsoft.com/azure/active-directory/saas-apps/timetabling-solutions-tutorial), [синчронет Click](https://docs.microsoft.com/azure/active-directory/saas-apps/synchronet-click-tutorial), [Empower](https://www.made-in-office.com/en/), [Trend Micro Web Security(TMWS)](https://review.docs.microsoft.com/azure/active-directory/saas-apps/trend-micro-tutorial) [Fortes изменить облако](https://docs.microsoft.com/azure/active-directory/saas-apps/fortes-change-cloud-tutorial), [litmus](https://docs.microsoft.com/azure/active-directory/saas-apps/litmus-tutorial), [GroupTalk](https://recorder.grouptalk.com/) [, Frontify,](https://docs.microsoft.com/azure/active-directory/saas-apps/frontify-tutorial) [MongoDB Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/mongodb-cloud-tutorial) [, TickitLMS](https://docs.microsoft.com/azure/active-directory/saas-apps/nitro-productivity-suite-tutorial) [учиться](https://docs.microsoft.com/azure/active-directory/saas-apps/tickitlms-learn-tutorial) [COCO](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>Microsoft Graph поддержка разностных запросов для oAuth2PermissionGrant, доступной для общедоступной предварительной версии

**Тип.** Новая функция.

**Категория службы:** MS Graph

**Возможности продукта.** Возможности для разработчиков

Разностный запрос для oAuth2PermissionGrant доступен для общедоступной предварительной версии. Теперь можно отслеживаниь изменений без постоянного опроса Microsoft Graph. [Подробнее.](https://docs.microsoft.com/graph/api/oAuth2PermissionGrant-delta?view=graph-rest-beta&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>Microsoft Graph поддержка разностных запросов для контакта Организации в общем доступе

**Тип.** Новая функция.

**Категория службы:** MS Graph

**Возможности продукта.** Возможности для разработчиков

Разностный запрос для контактов Организации общедоступен! Теперь вы можете отслеживание изменений в рабочих приложениях без постоянного опроса Microsoft Graph. Замените существующий код, который постоянно опрашивает Оргконтакт данные по разностному запросу для значительного повышения производительности. [Подробнее.](https://docs.microsoft.com/graph/api/orgcontact-delta?view=graph-rest-1.0&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>Microsoft Graph поддержка разностных запросов для приложения в общем доступе

**Тип.** Новая функция.

**Категория службы:** MS Graph

**Возможности продукта.** Возможности для разработчиков

Разностный запрос для приложений общедоступен! Теперь вы можете отслеживание изменений в рабочих приложениях без постоянного опроса Microsoft Graph. Замените существующий код, который постоянно опрашивает данные приложения по разностному запросу, чтобы значительно повысить производительность. [Подробнее.](https://docs.microsoft.com/graph/api/application-delta?view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>Microsoft Graph поддержка разностных запросов для административных единиц, доступных для общедоступной предварительной версии

**Тип.** Новая функция.

**Категория службы:** MS Graph

**Возможности продукта:** Для общедоступной предварительной версии доступен разностный запрос разработчиков для административных единиц. Теперь можно отслеживаниь изменений без постоянного опроса Microsoft Graph. [Подробнее.](https://docs.microsoft.com/graph/api/administrativeunit-delta?view=graph-rest-beta&tabs=http)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>Управление номерами телефонов для проверки подлинности и многое другое в новых Microsoft Graph бета-версиях API

**Тип.** Новая функция.

**Категория службы:** MS Graph

**Возможности продукта.** Возможности для разработчиков

Эти API являются ключевым средством для управления методами проверки подлинности пользователей. Теперь вы можете программными средствами предварительно регистрировать и администрировать средства проверки подлинности, используемые для MFA и самостоятельного сброса пароля (SSPR). Это одна из наиболее востребованных функций в Azure MFA, SSPR и Microsoft Graphных пробелах. Новые интерфейсы API, которые мы выпустили в этой волне, дают вам возможность:

- Чтение, добавление, обновление и удаление телефонов для проверки подлинности пользователя
- Сброс пароля пользователя
- Включение и отключение сервера SMS-Вход

Дополнительные сведения см. в статье [Общие сведения об API методов проверки подлинности Azure AD](https://docs.microsoft.com/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

---

### <a name="administrative-units-public-preview"></a>Общедоступная Предварительная версия административных единиц

**Тип.** Новая функция.

**Категория службы:** RBAC

**Возможность продукта.** Контроль доступа.

Административные единицы позволяют предоставлять разрешения администратора только в пределах определенного подразделения, региона или другого определенного вами сегмента вашей организации. Административные единицы можно использовать для делегирования разрешений региональным администраторам или детализированной настройки политики. Например, администратор учетных записей пользователей может обновлять данные профиля, сбрасывать пароли и назначать лицензии только для пользователей из своей административной единицы.

С помощью административных единиц администратор может:

- Создание административной единицы для децентрализованного управления ресурсами
- Назначение роли с административными разрешениями только для пользователей Azure AD в административной единице
- Заполнение административными единиц пользователями и группами по мере необходимости

Дополнительные сведения см. [в разделе Администрирование единиц управления в Azure Active Directory (Предварительная версия)](https://aka.ms/AdminUnitsDocs).

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>Встроенные роли администратора принтеров и специалистов по принтерам

**Тип.** Новая функция.

**Категория службы:** RBAC

**Возможность продукта.** Контроль доступа.

**Администратор принтера**. пользователи с этой ролью могут регистрировать принтеры и управлять всеми аспектами всех конфигураций принтеров в универсальном решении печати (Майкрософт), включая параметры соединителя универсального принтера. Они могут предоставлять согласие на все запросы делегирования разрешений на печать. Администраторы принтеров также имеют доступ к отчетам о печати. 

**Специалист по печати**. пользователи с этой ролью могут регистрировать принтеры и управлять состоянием принтера в универсальном решении печати (Майкрософт). Им также доступно чтение всей информации о соединителях. Основные задачи. не удается выполнить типографию. устанавливаются разрешения пользователей на принтерах и совместное использование принтеров. [Подробнее.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Встроенная роль администратора гибридной идентификации

**Тип.** Новая функция.

**Категория службы:** RBAC

**Возможность продукта.** Контроль доступа.

Пользователи с этой ролью могут включать, настраивать и контролировать службы и параметры, связанные с использованием гибридных удостоверений в Azure AD. Эта роль предоставляет возможность настроить Azure AD для использования одного из трех поддерживаемых методов проверки подлинности&#8212;синхронизации хэшей паролей (PHS), сквозной проверки подлинности (PTA) или Федерации (AD FS или стороннего поставщика Федерации) &#8212;и развертывания связанной локальной инфраструктуры для их включения. Локальная инфраструктура включает в себя подготовку и агенты PTA. Эта роль дает возможность использовать простой единый вход (S-SSO), обеспечивающий беспроблемную проверку подлинности на устройствах, работающих не под управлением Windows 10, или на компьютерах не с ОС Windows Server 2016. Кроме того, эта роль предоставляет возможность просматривать журналы входа и получать доступ к службе работоспособности и аналитики для целей мониторинга и устранения неполадок. [Подробнее.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Встроенная роль администратора сети

**Тип.** Новая функция.

**Категория службы:** RBAC

**Возможность продукта.** Контроль доступа.

Пользователи с этой ролью могут просматривать рекомендации по архитектуре периметра сети от корпорации Майкрософт, основанные на данных телеметрии сети из расположений пользователей. Производительность сети для Office 365 полагается на тщательную архитектуру периметра сети клиентов предприятия, которая обычно зависит от местонахождения пользователя. Данная роль позволяет изменять обнаруженные расположения пользователей, настраивать их сетевые параметры и получать более оптимальную телеметрию и рекомендации по проектированию. [Подробнее.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Групповые операции и загрузки на портале администрирования Azure AD

**Тип.** Новая функция.

**Категория службы:** Управление пользователями

**Возможности продукта:** Каталоги

Теперь вы можете выполнять групповые действия для пользователей и групп в Azure AD, загружая CSV-файл на портале администрирования Azure AD. Вы можете создавать пользователей, удалять пользователей и приглашать гостевых пользователей. Можно добавлять и удалять членов из группы.

Вы также можете загрузить списки ресурсов Azure AD с портала администрирования Azure AD. Вы можете скачать список пользователей в каталоге, список групп в каталоге и членов определенной группы.

Дополнительные сведения см. в следующих примерах.

- [Создание пользователей](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-add) или [приглашение гостевых пользователей](https://docs.microsoft.com/azure/active-directory/b2b/tutorial-bulk-invite)
- [Удаление пользователей](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-delete) или [Восстановление удаленных пользователей](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-restore)
- [Скачивание списка пользователей](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-download) или [списка загрузки групп](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download)
- [Добавление (импорт) элементов](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members) или [Удаление участников](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members) или [скачивание списка членов](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members) группы

---

### <a name="my-staff-delegated-user-management"></a>Управление делегированными пользователями сотрудниками

**Тип.** Новая функция.

**Категория службы:** Управление пользователями

**Возможности продукта:**

Мой персонал позволяет менеджерам Фирстлине, таким как менеджер магазина, убедиться, что их сотрудники могут получить доступ к учетным записям Azure AD. Вместо того чтобы полагаться на центральную службу поддержки, организации могут делегировать общие задачи, такие как сброс паролей или изменение номеров телефонов, в Фирстлине Manager. Благодаря моему персоналу пользователь, который не может получить доступ к своей учетной записи, может повторно получить доступ всего за несколько щелчков, не требующих технической поддержки или персонала отдела ИТ. Дополнительные сведения см. в статьях [Управление пользователями с помощью моего персонала (Предварительная версия)](https://aka.ms/MyStaffAdminDocs) и [Делегирование управления пользователями с моим персоналом (Предварительная версия)](https://aka.ms/MyStaffUserDocs).

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>Обновленный интерфейс пользователя в проверках доступа

**Тип.** Измененная функция.

**Категория службы:** Проверки доступа

**Возможности продукта:** Управление удостоверениями

Мы обновили интерфейс рецензента для проверок доступа Azure AD на портале "Мои приложения". В конце апреля ваши рецензенты, вошедшие в систему проверки доступа Azure AD, увидят баннер, который позволит им попробовать обновленный интерфейс в разделе "мой доступ". Обратите внимание, что обновленная версия проверки доступа предоставляет те же функциональные возможности, что и текущий интерфейс, но с улучшенным пользовательским интерфейсом на основе новых возможностей, позволяющих пользователям работать эффективно. [Дополнительные сведения об обновленном интерфейсе](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review)см. здесь. Эта общедоступная Предварительная версия будет доступна до конца июля 2020. В конце июля рецензенты, которые не выбрали предварительную версию, будут автоматически направлены к моему доступу для выполнения проверок доступа. Если вы хотите, чтобы рецензенты постоянно перешли на предварительный просмотр в моем доступе, выполните [запрос здесь](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>Приложения для входящей подготовки и обратной записи пользователей workday теперь поддерживают новейшие версии API веб-служб Workday.

**Тип.** Измененная функция.

**Категория службы.** Подготовка приложений.

**Возможности продукта:** 

На основе отзывов клиентов мы обновили приложения для входящей подготовки пользователей и обратной записи Workday в коллекции корпоративных приложений для поддержки последних версий API-интерфейса Workday Web Services (WWS). С этим изменением клиенты могут указать версию API WWS, которую нужно использовать в строке подключения. Это дает клиентам возможность получить дополнительные атрибуты HR, доступные в выпусках Workday. Приложение обратной записи Workday теперь использует рекомендованную веб-службу Change_Work_Contact_Info Workday для преодоления ограничений Maintain_Contact_Info.

Если в строке подключения не указана версия, по умолчанию приложения для входящей передачи Workday продолжат использовать WWS v 21.1 для переключения на последние API-интерфейсы Workday для входящей подготовки пользователей. клиентам необходимо обновить строку подключения, как описано [в этом](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles) руководстве, а также обновить выражения, используемые для атрибутов workday, как описано в [руководстве по атрибуту workday](https://docs.microsoft.com/azure/active-directory/app-provisioning/workday-attribute-reference#xpath-values-for-workday-web-services-wws-api-v30). 

Чтобы использовать новый API для обратной записи, в приложении подготовки обратной записи Workday не требуется никаких изменений. На стороне Workday убедитесь, что учетная запись пользователя системы интеграции Workday (ИСУ) имеет разрешения на вызов бизнес-процесса Change_Work_Contact, как описано в разделе "учебник" [Настройка разрешений политики безопасности бизнес-процессов](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#configuring-business-process-security-policy-permissions). 

Мы обновили это [руководство](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial) , чтобы отразить новую поддержку версии API.

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>Пользователи с ролью доступа по умолчанию теперь находятся в области для подготовки

**Тип.** Измененная функция.

**Категория службы.** Подготовка приложений.

**Возможности продукта:** Управление жизненным циклом удостоверений

Исторически пользователи с ролью доступа по умолчанию выходят за пределы области подготовки. Мы слышали Отзывы о том, что клиенты хотят, чтобы пользователи с этой ролью были в области подготовки. По состоянию на 16 апреля 2020 г. все новые конфигурации подготовки позволяют подготовить пользователей с ролью доступа по умолчанию. Постепенно изменится поведение существующих конфигураций подготовки для поддержки подготовки пользователей с помощью этой роли. [Подробнее.](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned)

---

### <a name="updated-provisioning-ui"></a>Обновлен пользовательский интерфейс подготовки

**Тип.** Измененная функция.

**Категория службы.** Подготовка приложений.

**Возможности продукта:** Управление жизненным циклом удостоверений

Мы обновили процесс подготовки, чтобы создать более специализированное представление управления. При переходе к колонке подготовка для корпоративного приложения, которое уже было настроено, вы сможете легко отслеживать ход подготовки и управлять такими действиями, как запуск, остановка и перезапуск. [Подробнее.](https://docs.microsoft.com/azure/active-directory/app-provisioning/configure-automatic-user-provisioning-portal)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>Проверка правила динамической группы теперь доступна для общедоступной предварительной версии

**Тип.** Измененная функция.

**Категория службы.** Управление группами.

**Возможности продукта.** Совместная работа.

Azure Active Directory (Azure AD) теперь предоставляет средства для проверки динамических правил групп. На вкладке **Проверка правил** можно проверить динамическое правило по образцу членов группы, чтобы убедиться, что правило работает должным образом. При создании или обновлении динамических групп правил администраторам необходимо определить, будет ли пользователь или устройство членом группы. Это помогает определить, соответствует ли пользователь или устройство условиям правила, и помогает устранять неполадки, когда членство не ожидается.

Дополнительные сведения см. [в статье Проверка правила членства в динамической группе (Предварительная версия)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-rule-validation).

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>Оценка безопасности удостоверения — обновления по умолчанию и действия улучшения MFA

**Тип.** Измененная функция.

**Категория службы:** недоступно

**Возможности продукта.** Безопасность и защита удостоверений.

**Поддержка параметров безопасности по умолчанию для действий по улучшению Azure AD:** Microsoft Security Score будет обновлять действия по улучшению для [обеспечения безопасности по умолчанию в Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults), что упрощает защиту Организации с помощью предварительно настроенных параметров безопасности для распространенных атак. Это повлияет на следующие действия по улучшению:

- Убедитесь, что все пользователи могут выполнять многофакторную проверку подлинности для безопасного доступа.
- Требовать MFA для административных ролей
- Включить политику для блокировки устаревшей проверки подлинности
 
**Обновления действий по улучшению MFA:** Чтобы отразить потребность предприятий в обеспечении максимальной безопасности при применении политик, которые работают с бизнес-обработкой, Microsoft Security Score удалил три действия улучшения, центрированные вокруг многофакторной проверки подлинности, и добавил два.

Удалены действия по улучшению:

- Регистрация всех пользователей для многофакторной проверки подлинности
- Обязательная многофакторная проверка подлинности для всех пользователей
- Требовать MFA для привилегированных ролей Azure AD

Добавлены действия по улучшению:

- Убедитесь, что все пользователи могут выполнять многофакторную проверку подлинности для безопасного доступа.
- Требовать MFA для административных ролей

Эти новые действия по улучшению требуют регистрации пользователей или администраторов для многофакторной проверки подлинности (MFA) в каталоге и создания правильного набора политик, соответствующих потребностям Организации. Основная цель — обеспечить гибкость, при этом все пользователи и администраторы могут проходить проверку подлинности с использованием нескольких факторов или запросов на проверку удостоверения на основе рисков. Это может иметь форму с несколькими политиками, которые применяют решения с заданной областью или настройки по умолчанию (согласно состоянию на Март 16 марта), которые позволяют корпорации Майкрософт решать, когда следует выдать запрос MFA пользователям. [Узнайте больше о новых возможностях Microsoft Secure Score](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score?view=o365-worldwide#whats-new).

---

## <a name="march-2020"></a>Март 2020 г.

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>Неуправляемые учетные записи Azure Active Directory в обновлении B2B в марте, 2021

**Тип.** Планирование изменений.  
**Категория службы.** B2B.  
**Возможности продукта.** B2B и B2C.
 
**Начиная с 31 марта 2021**г. Корпорация Майкрософт больше не будет поддерживать погашение приглашений, создавая неуправляемые учетные записи Azure Active Directory (Azure AD) и клиенты для сценариев совместной работы B2B. В процессе подготовки мы рекомендуем использовать [одноразовую проверку подлинности с помощью секретного кода](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode).

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Пользователи с ролью доступа по умолчанию будут находиться в области подготовки

**Тип.** Планирование изменений.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта:** Управление жизненным циклом удостоверений
 
Исторически пользователи с ролью доступа по умолчанию выходят за пределы области подготовки. Мы слышали Отзывы о том, что клиенты хотят, чтобы пользователи с этой ролью были в области подготовки. Мы работаем над развертыванием изменений, чтобы все новые конфигурации подготовки могли быть подготовлены для пользователей с ролью доступа по умолчанию. Постепенно мы изменим поведение существующих конфигураций подготовки для поддержки подготовки пользователей с помощью этой роли. Никаких действий клиента не требуется. После этого изменения мы будем публиковать обновленную [документацию](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned) .

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Служба совместной работы Azure AD B2B будет доступна в Microsoft Azure, управляемых клиентами 21Vianet (Azure Китая 21Vianet).

**Тип.** Планирование изменений.  
**Категория службы.** B2B.  
**Возможности продукта.** B2B и B2C.
 
Возможности службы совместной работы Azure AD B2B будут доступны в Microsoft Azure, предоставляемых клиентами 21Vianet (Azure для Китая в Китае), что позволяет пользователям в клиенте Azure для Китая в Китае сотрудничать без проблем работать с пользователями в других клиентах Azure Китая (21Vianet). [Дополнительные сведения о службе совместной работы Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/).

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Перепроектирование приглашения службы совместной работы Azure AD B2B

**Тип.** Планирование изменений.  
**Категория службы.** B2B.  
**Возможности продукта.** B2B и B2C.
 
[Сообщения электронной почты](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) , отправленные службой приглашений службы совместной работы Azure AD B2B для приглашения пользователей в каталог, будут переработаны, чтобы сделать более четкими сведения о приглашении и дальнейшие действия пользователя.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>Изменения политики Хомереалмдисковери будут отображаться в журналах аудита.

**Тип.** Исправление.  
**Категория службы:** Попытк  
**Возможности продукта.** Мониторинг и отчетность.
 
Исправлена ошибка, из-за которой изменения в [политике хомереалмдисковери](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) не включались в журналы аудита. Теперь вы сможете видеть, когда и как была изменена политика и кем. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Новые Федеративные приложения, доступные в коллекции Azure AD App — 2020 марта

**Тип.** Новая функция.  
**Категория службы:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
В марте 2020 мы добавили следующие 51 новых приложений с поддержкой Федерации в коллекцию приложений: 

[Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect), [Zoho One Китая](https://docs.microsoft.com/azure/active-directory/saas-apps/zoho-one-china-tutorial), [Плусплус](https://test.plusplus.app/auth/login/azuread-outlook/), [Profit.co SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/profitco-saml-app-tutorial), [поставщик услуг ипоинт](https://docs.microsoft.com/azure/active-directory/saas-apps/ipoint-service-provider-tutorial), [Contexxt.AI Sphere](https://contexxt-sphere.com/login), [знания by Инвиктус](https://docs.microsoft.com/azure/active-directory/saas-apps/wisdom-by-invictus-tutorial), [блики для цифрового входа](https://spark-dev.pixelnebula.com/login), [Logz.IO-облачное наблюдение за инженерами](https://docs.microsoft.com/azure/active-directory/saas-apps/logzio-cloud-observability-for-engineers-tutorial), [спектруму](https://docs.microsoft.com/azure/active-directory/saas-apps/spectrumu-tutorial), [Биззконтакт](https://bizzcontact.app/), [елкано SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/elqano-sso-tutorial), [Маркетсигншаре](http://www.signshare.com/), [кросскновледже Learning Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/crossknowledge-learning-suite-tutorial), [Netvision Compas](https://docs.microsoft.com/azure/active-directory/saas-apps/netvision-compas-tutorial), [концентратор FCM](https://docs.microsoft.com/azure/active-directory/saas-apps/fcm-hub-tutorial), [RIB A/S Byggeweb мобильный](https://apps.apple.com/us/app/docia/id529058757), [GoLinks](https://docs.microsoft.com/azure/active-directory/saas-apps/golinks-tutorial), [Datadog](https://docs.microsoft.com/azure/active-directory/saas-apps/datadog-tutorial) [, Zscaler,](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-b2b-user-portal-tutorial)рабочий процесс, [точность](https://docs.microsoft.com/azure/active-directory/saas-apps/lift-tutorial), [PlanView Enterprise One](https://docs.microsoft.com/azure/active-directory/saas-apps/planview-enterprise-one-tutorial) [, WatchTeams, круговая](https://www.devfinition.com/) [,](https://demo.asterapp.io/login) [рабочие процессы](https://docs.microsoft.com/azure/active-directory/saas-apps/skills-workflow-tutorial), анализ узла [Платформа IP-адресов](https://docs.microsoft.com/azure/active-directory/saas-apps/ip-platform-tutorial) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/invision-tutorial) [Pipedrive](https://docs.microsoft.com/azure/active-directory/saas-apps/pipedrive-tutorial), [демонстрационный семинар](https://app.showcaseworkshop.com/), [платформа интеграции Greenlight](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-integration-platform-tutorial), [Greenlight совместимое управление доступом](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-compliant-access-management-tutorial), [Grok обучение](https://docs.microsoft.com/azure/active-directory/saas-apps/grok-learning-tutorial), [Miradore Online](https://login.online.miradore.com/), [Khoros внимание](https://docs.microsoft.com/azure/active-directory/saas-apps/khoros-care-tutorial), [AskYourTeam](https://docs.microsoft.com/azure/active-directory/saas-apps/askyourteam-tutorial), [TruNarrative](https://docs.microsoft.com/azure/active-directory/saas-apps/trunarrative-tutorial) [, Smartwaiver, Bizagi](https://www.smartwaiver.com/m/user/sw_login.php?wms_login) [Studio для автоматизации цифровых процессов](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial), [insuiteX](https://www.insuite.jp/) [, sybo, Britive](https://www.systexsoftware.com.tw/) [, WhosOffice](https://docs.microsoft.com/azure/active-directory/saas-apps/britive-tutorial) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/whosoffice-tutorial) [-Days](https://docs.microsoft.com/azure/active-directory/saas-apps/e-days-tutorial), Kollective [Sdn](https://portal.kollective.app/login), Witivio [Playvox](https://my.playvox.com/login), Playvox [, Korn паром 360](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-360-tutorial), [кафе](https://docs.microsoft.com/azure/active-directory/saas-apps/campus-cafe-tutorial), [Catchpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/catchpoint-tutorial) [Новая точка перехвата и Code42](https://app.witivio.com/) [Node Insight](https://admin.nodeinsight.com/AADLogin.aspx) [Code42](https://docs.microsoft.com/azure/active-directory/saas-apps/code42-tutorial)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Служба совместной работы Azure AD B2B, доступная в клиентах Azure для государственных организаций

**Тип.** Новая функция.  
**Категория службы.** B2B.  
**Возможности продукта.** B2B и B2C.
 
Возможности службы совместной работы Azure AD B2B теперь доступны для некоторых клиентов Azure для государственных организаций.  Чтобы узнать, есть ли у клиента возможность использовать эти возможности, следуйте инструкциям на странице [как узнать, доступна ли служба совместной работы B2B в моем клиенте Azure для государственных организаций США?](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant).

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Интеграция Azure Monitor для журналов Azure теперь доступна в Azure для государственных организаций

**Тип.** Новая функция.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.
 
Интеграция Azure Monitor с журналами Azure AD теперь доступна в Azure для государственных организаций. Вы можете направить журналы Azure AD (журналы аудита и входа) в учетную запись хранения, концентратор событий и Log Analytics. Ознакомьтесь с [подробной документацией](https://aka.ms/aadlogsinamd) , а также с [планами развертывания для создания отчетов и мониторинга](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting) сценариев Azure AD.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Обновление защиты идентификации в Azure для государственных организаций

**Тип.** Новая функция.  
**Категория службы:** Защита идентификации  
**Возможности продукта.** Безопасность и защита удостоверений.

Мы рады поделиться тем, что теперь обновленный [Защита идентификации Azure AD](https://aka.ms/IdentityProtectionDocs)   интерфейс на [портале Microsoft Azure для государственных организаций](https://portal.azure.us/). Дополнительные сведения см. в записи [блога о объявлениях](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667).

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Аварийное восстановление. Скачайте и сохраните конфигурацию подготовки.

**Тип.** Новая функция.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта:** Управление жизненным циклом удостоверений
 
Служба подготовки Azure AD предоставляет широкий набор возможностей настройки. Клиенты должны иметь возможность сохранить конфигурацию, чтобы они могли ссылаться на них позже, или выполнить откат до известной работоспособной версии. Мы добавили возможность загрузки конфигурации подготовки в виде JSON-файла и передачи ее при необходимости. [Подробнее.](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration)

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (самостоятельный сброс пароля) теперь требует двух шлюзов для администраторов в Microsoft Azure, обслуживаемых 21Vianet (Azure Китая 21Vianet) 

**Тип.** Измененная функция.  
**Категория службы:** Самостоятельный сброс пароля  
**Возможности продукта.** Безопасность и защита удостоверений.
 
Ранее в Microsoft Azure, обслуживаемом 21Vianet (Azure Китая 21Vianet), администраторы, использующие самостоятельный сброс пароля (SSPR) для сброса собственных паролей, требуют только одной "шлюза" (запроса) для подтверждения их личности. В общедоступных и других национальных облаках администраторы обычно должны использовать два шлюза для подтверждения их подлинности при использовании SSPR. Но так как мы не поддерживали SMS или телефонные звонки в Azure Китая 21Vianet, мы развернули администраторам один шлюз для сброса паролей.

Мы создаем SSPRую четность функций между Azure Китая 21Vianet и общедоступным облаком. В дальнейшем администраторы должны использовать два шлюза при использовании SSPR. Будут поддерживаться SMS, телефонные звонки и уведомления и коды приложений для проверки подлинности. [Подробнее.](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences)

---

### <a name="password-length-is-limited-to-256-characters"></a>Длина пароля ограничена 256 символами

**Тип.** Измененная функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Проверка подлинности пользователя
 
Для обеспечения надежности службы Azure AD пароли пользователей теперь ограничены длиной до 256 символов. Пользователям с более длинными паролями будет предложено изменить пароль при последующем входе в систему, обратившись к администратору или с помощью функции самостоятельного сброса пароля.

Это изменение было включено 13 марта 2020 г., в 10:00 PST (18:00 UTC), а ошибка — ААДСТС 50052, Инвалидпассвордексцеедсмаксленгс. Дополнительные сведения см. в разделе [уведомление о критическом изменении](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters) .

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Журналы входа Azure AD теперь доступны для всех бесплатных клиентов с помощью портал Azure

**Тип.** Измененная функция.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.
 
Начиная с, клиенты, у которых есть бесплатные клиенты, могут получить доступ к [журналам входа Azure AD из портал Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) в течение 7 дней. Ранее журналы входа были доступны только для клиентов с лицензиями Azure Active Directory Premium. После этого изменения все клиенты смогут получить доступ к этим журналам через портал.

> [!NOTE]
> Клиентам по-прежнему нужна лицензия Premium (Azure Active Directory Premium P1 или P2) для доступа к журналам входа через Microsoft Graph API и Azure Monitor.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Нерекомендуемый параметр групп на уровне каталога из групп общие параметры на портал Azure

**Тип.** Прекращение поддержки.  
**Категория службы.** Управление группами.  
**Возможности продукта.** Совместная работа.

Чтобы предоставить клиентам более гибкий способ создания групп на уровне каталога, которые лучше всего соответствуют своим потребностям, мы заменили параметр **группы уровня каталога** из **Groups**  >  раздела**Общие** параметры в портал Azure со ссылкой на [документацию по динамической группе](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership). Мы улучшили документацию для включения дополнительных инструкций, чтобы администраторы могли создавать группы всех пользователей, включающие или исключающие гостевые пользователи.

---

## <a name="february-2020"></a>Февраль 2020 г.

### <a name="upcoming-changes-to-custom-controls"></a>Предстоящие изменения в пользовательских элементах управления

**Тип.** Планирование изменений.  
**Категория службы:** MFA  
**Возможности продукта.** Безопасность и защита удостоверений.
 
Мы планируем заменить текущий предварительный просмотр настраиваемых элементов управления на подход, обеспечивающий возможность проверки подлинности, предоставляемой партнером, для беспрепятственной работы с Azure Active Directory администратором и конечными пользователями. Сегодня партнерские решения MFA имеют следующие ограничения: они работают только после ввода пароля. они не служат в качестве MFA для поэтапной проверки подлинности в других ключевых сценариях. и не интегрируются с функциями управления учетными данными пользователя или администратора. Новая реализация позволит использовать предоставленные партнером факторы проверки подлинности вместе со встроенными факторами для ключевых сценариев, включая регистрацию, использование, утверждения MFA, пошаговую проверку подлинности, отчеты и ведение журнала. 

Пользовательские элементы управления по прежнему будут поддерживаться в предварительной версии вместе с новой структурой, пока она не станет общедоступной. На этом этапе мы предоставим клиентам время перехода на новую архитектуру. Из-за ограничений текущего подхода мы не будем подключать новые поставщики до тех пор, пока не станет доступен новый проект. Мы тесно работаем с клиентами и поставщиками и будем связываться с временной шкалой по мере приближения. [Подробнее.](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#)

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Оценка безопасности идентификации — обновления действий по улучшению MFA

**Тип.** Планирование изменений.  
**Категория службы:** MFA  
**Возможности продукта.** Безопасность и защита удостоверений.
 
Чтобы отразить потребность предприятий в обеспечении максимальной безопасности при применении политик, которые работают с их бизнесом, Microsoft Security Score устраняет три действия по улучшению, центрированные вокруг многофакторной проверки подлинности (MFA), и добавляя два.

Будут удалены следующие действия по улучшению:

- Регистрация всех пользователей для MFA
- Обязательная многофакторная проверка подлинности для всех пользователей
- Требовать MFA для привилегированных ролей Azure AD

Будут добавлены следующие действия по улучшению:

- Убедитесь, что все пользователи могут выполнять MFA для безопасного доступа.
- Требовать MFA для административных ролей

Для этих новых действий по улучшению потребуется регистрация пользователей или администраторов для MFA в каталоге и создание правильного набора политик, соответствующих потребностям Организации. Основная цель — обеспечить гибкость, при этом все пользователи и администраторы могут проходить проверку подлинности с использованием нескольких факторов или запросов на проверку удостоверения на основе рисков. Это может принимать форму настройки параметров безопасности по умолчанию, которые позволяют корпорации Майкрософт решать, когда следует выдать запрос MFA пользователям, или иметь несколько политик, которые применяют решения с заданной областью. В ходе этих обновлений действий по улучшению политики базовой защиты больше не будут включаться в вычисления оценки. [Узнайте больше о том, что поступает в Microsoft Secure Score](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide).

---

### <a name="azure-ad-domain-services-sku-selection"></a>Выбор SKU доменных служб Azure AD

**Тип.** Новая функция.  
**Категория службы.** Доменные службы Azure AD.  
**Возможности продукта.** Доменные службы Azure AD.
 
Мы слышали Отзывы о том, что клиенты доменных служб Azure AD хотят обеспечить большую гибкость при выборе уровней производительности для своих экземпляров. Начиная с 1 февраля 2020 г. мы переключались с динамической модели (где Azure AD определяет ценовую категорию производительности и ценовой категории на основе числа объектов) в модель самостоятельного выбора. Теперь клиенты могут выбрать уровень производительности, соответствующий их среде. Это изменение также позволяет нам включать новые сценарии, такие как леса ресурсов, и такие функции уровня Premium, как ежедневная архивация. Теперь число объектов не ограничено для всех номеров SKU, но мы будем предлагать предложения по количеству объектов для каждого уровня.

**Непосредственные действия клиента не требуются.** Для существующих клиентов динамический уровень, который использовался 1 февраля 2020, определяет новый уровень по умолчанию. Результат этого изменения не влияет на цену или производительность. В дальнейшем клиенты AD DS Azure должны оценить требования к производительности по мере изменения размера каталога и характеристик рабочей нагрузки. Переключение между уровнями служб продолжится без простоя, и мы больше не будем автоматически переносить клиентов на новые уровни на основе роста каталога. Более того, Цена не будет увеличена, а Новая цена будет соответствовать текущей модели выставления счетов. Дополнительные сведения см. в [документации по AD DS SKU Azure](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus) и на [странице цен на доменные службы Azure AD](https://azure.microsoft.com/pricing/details/active-directory-ds/).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Новые Федеративные приложения, доступные в коллекции Azure AD App — Февраль 2020

**Тип.** Новая функция.  
**Категория службы:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
В феврале 2020 мы добавили в коллекцию приложений следующие 31 новые приложения с поддержкой федерации: 

[Иамип патентная платформа](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial), [опыт работы с ОБЛАКОМ](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial), [единый вход для Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial), [Служба безопасности электронной почты Barracuda](https://ess.barracudanetworks.com/sso/azure), [отчеты ABa](https://myaba.co.uk/client-access/signin/auth/msad), [в случае аварийного подключения к Интернет-порталу](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial), [БИК облачного проектирования](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial), [бикипер соединителя данных Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial) [паромные оценки](https://www.kornferry.com/solutions/kf-digital/kf-assess), [команда Веркада](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial), [Сплаштоп](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial), [сикссенсе](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial), [Еаб навигации](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial), [New Relic (ограниченный выпуск)](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial), [сулиум](https://admin.thulium.com/login/instance), [Диспетчер билетов](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial), средство [выбора шаблонов для команд](https://links.officeatwork.com/templatechooser-download-teams), [пчел](https://www.beesy.me/index.php/site/login), [система поддержки работоспособности](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial), [Мурал](https://app.mural.co/signup), [Hive](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial), [Лавадо](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview), [вакелет](https://wakelet.com/login), [фирмекс ВДР](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial), [ThingLink для преподавателей и учебных заведений](https://www.thinglink.com/), формат [Coda](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial), [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WeDo](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial), [InvitePeople](https://invitepeople.com/login), [Перепечатка рабочей статьи Galaxy](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial), [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Новые соединители подготовки в коллекции приложений Azure AD — Февраль 2020

**Тип.** Новая функция.  
**Категория службы:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
Теперь вы можете автоматизировать процессы создания, обновление и удаление учетных записей пользователей для следующих новых интегрированных приложений:

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud от Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

Дополнительные сведения об усилении защиты организации с помощью автоматизированной подготовки учетных записей пользователей см. в статье [Автоматическая подготовка пользователей для приложений SaaS в Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Поддержка Azure AD ключей безопасности FIDO2 в гибридных средах

**Тип.** Новая функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Проверка подлинности пользователя
 
Мы сообщаем общедоступную предварительную версию поддержки Azure AD для ключей безопасности FIDO2 в гибридных средах. Теперь пользователи могут использовать ключи безопасности FIDO2 для входа в гибридные устройства Windows 10, присоединенные к Azure AD, а также для быстрого входа в локальные и облачные ресурсы. Поддержка гибридных сред была наиболее востребованной функцией для наших клиентов, не защищенных паролем, так как мы первоначально запустили общедоступную предварительную версию для поддержки FIDO2 в устройствах, присоединенных к Azure AD. Проверка подлинности без пароля с использованием таких расширенных технологий, как биометрические функции и шифрование с открытым и закрытым ключом, обеспечивает удобство и простоту использования в защищенном режиме. В этой общедоступной предварительной версии теперь можно использовать современные проверки подлинности, такие как ключи безопасности FIDO2, для доступа к традиционным ресурсам Active Directory. Дополнительные сведения см. в подразделе [единый вход для локальных ресурсов](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises). 

Чтобы приступить к работе, посетите страницу [Включение ключей безопасности FIDO2 для вашего клиента](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key) , чтобы получить пошаговые инструкции. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>Новый интерфейс "Моя учетная запись" теперь является общедоступным

**Тип.** Измененная функция.  
**Категория службы:** Мой профиль или учетная запись  
**Возможности продукта:** Взаимодействие с конечным пользователем
 
Теперь в общедоступной учетной записи для всех задач управления учетными записями конечных пользователей доступна одна из них. Конечные пользователи могут получить доступ к этому новому сайту через URL-адрес или в заголовке нового интерфейса "Мои приложения". Узнайте больше о всех возможностях самообслуживания нового интерфейса на [портале учетной записи](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>URL-адрес сайта моей учетной записи обновлен до myaccount.microsoft.com

**Тип.** Измененная функция.  
**Категория службы:** Мой профиль или учетная запись  
**Возможности продукта:** Взаимодействие с конечным пользователем
 
Новый интерфейс пользователя моей учетной записи будет обновлять свой URL-адрес `https://myaccount.microsoft.com` в следующий месяц. Дополнительные сведения о работе и всех возможностях самообслуживания учетных записей, которые она предлагает конечным пользователям на [портале учетных записей, см](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview). здесь.

---
 
## <a name="january-2020"></a>Январь 2020 г.
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>Новый портал "Мои приложения" теперь является общедоступным

**Тип.** Планирование изменений.  
**Категория службы.** "Мои приложения".  
**Возможности продукта:** Взаимодействие с конечным пользователем
 
Обновите свою организацию до нового портала "Мои приложения", который теперь является общедоступным! Дополнительные сведения о новом портале и коллекциях см. в [документе Создание коллекций на портале "Мои приложения"](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Рабочие области в Azure AD были переименованы в коллекции

**Тип.** Измененная функция.  
**Категория службы.** "Мои приложения".   
**Возможности продукта:** Взаимодействие с конечным пользователем
 
Рабочие области — Фильтры, которые администраторы могут настроить для организации приложений пользователей, теперь будут называться коллекциями. Дополнительные сведения о том, как настроить их на [портале "Мои приложения",](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)см. здесь.

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Azure AD B2C регистрации телефона и входа с помощью настраиваемой политики (общедоступная Предварительная версия)

**Тип.** Новая функция.  
**Категория службы.** B2C — управление удостоверениями клиентов.  
**Возможности продукта.** B2B и B2C.
 
При регистрации и входе в систему разработчики и предприятия могут позволить своим клиентам регистрироваться и входить с помощью одноразового пароля, отправленного на телефонный номер пользователя через SMS. Эта функция также позволяет клиенту изменить свой номер телефона в случае утери доступа к телефону. Благодаря возможности пользовательских политик регистрация и вход с помощью телефона позволяют разработчикам и предприятиям обмениваться своими торговыми марками через настройку страниц. Узнайте, как [настроить регистрацию и вход в систему с помощью пользовательских политик в Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication).
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Новые соединители подготовки в коллекции приложений Azure AD — Январь 2020

**Тип.** Новая функция.  
**Категория службы:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
Теперь вы можете автоматизировать процессы создания, обновление и удаление учетных записей пользователей для следующих новых интегрированных приложений:

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

Дополнительные сведения об усилении защиты организации с помощью автоматизированной подготовки учетных записей пользователей см. в статье [Автоматическая подготовка пользователей для приложений SaaS в Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Новые Федеративные приложения, доступные в коллекции Azure AD App-Январь 2020

**Тип.** Новая функция.  
**Категория службы:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
В январе 2020 мы добавили следующие 33 новых приложений с поддержкой Федерации в коллекцию приложений: 

[Жоса](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial), [высокореберное облако](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial), [terraform Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial), [спинтр SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial), [абибот нетлогистик](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik), [скикикк](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), [с](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial)более ранними [, леавебот](https://leavebot.io/#home), [Camp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial), [Трипактионс](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial), [смартворк](https://www.intumit.com/english/SmartWork.html), [Дотком-Monitor](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial), [ссожен — шлюз единого входа Azure AD для Oracle E-Business Suite-EBS, PeopleSoft и жде](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial), [размещенный MyCirqa единый вход](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial), [платформа управления свойствами Yuhu](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial), [LumApps](https://sites.lumapps.com/login), [выпуск Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial), [Talentsoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial), [SmartDB для Microsoft Teams](http://teams.smartdb.jp/login/), [PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial), [ContractSafe Saml2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial), [Maxient выполнять управление программным обеспечением](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial), [Helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial), [PortalTalk 365](https://www.portaltalk.com/), [совещание](https://portal.coreview.com/), [squelch Cloud Office 365 Connector](https://laxmi.squelch.io/login), PingFlow [Аутентификация](https://app-staging.pingview.io/), [PrinterLogic SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial), Taskize [Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial), [Sandwai](https://app.sandwai.com/), [EZRentOut](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial) [, AssetSonar, Akari](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial) [Virtual Assistant](https://akari.io/akari-virtual-assistant/)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="two-new-identity-protection-detections"></a>Два новых обнаружения защиты идентификации

**Тип.** Новая функция.  
**Категория службы:** Защита идентификации  
**Возможности продукта.** Безопасность и защита удостоверений.
 
Мы добавили два новых типа связывания для входа в систему защиты идентификации: подозрительные правила обработки входящих сообщений и невозможность перемещения. Эти обнаружения в автономном режиме обнаруживаются Microsoft Cloud App Security (МКАС) и влияют на риск пользователя и входа в систему защиты идентификации. Дополнительные сведения об этих обнаружениях см. в статье [типы рисков для входа в](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk)систему.
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Критическое изменение: фрагменты кода URI не будут проходить через перенаправление имени входа

**Тип.** Измененная функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Проверка подлинности пользователя
 
Начиная с 8 февраля 2020 г. при отправке запроса в login.microsoftonline.com для входа пользователя служба добавит в запрос пустой фрагмент.  Это не позволяет классу атак перенаправления, гарантируя, что браузер очищает любой существующий фрагмент в запросе. Ни одно приложение не должно иметь зависимости от этого поведения. Дополнительные сведения см. в разделе [критические изменения](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020) в документации по платформе удостоверений Майкрософт.

