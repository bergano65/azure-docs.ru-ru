---
title: Новые возможности Заметки о выпуске Azure Active Directory | Документация Майкрософт
description: Узнайте о новых возможностях Azure Active Directory, включая последние заметки о выпуске, известные проблемы, исправления ошибок, нерекомендуемые функции и предстоящие изменения.
services: active-directory
author: msmimart
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cf12c28dc04d0dbb8a680d45c8d8f5f5faa2d82
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262104"
---
# <a name="whats-new-in-azure-active-directory"></a>Новые возможности Azure Active Directory

>Получите уведомления о том, когда следует повторно посетить эту страницу для получения обновлений, скопировав и вставляя этот URL-адрес: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` в ![значок модуля чтения RSS,](./media/whats-new/feed-icon-16x16.png) средство чтения веб-канала.

Azure AD усовершенствуется на постоянной основе. Чтобы вы оставались в курсе последних разработок, в этой статье предоставлены такие сведения:

- Последние выпуски.
- Известные проблемы
- Исправления ошибок
- Нерекомендуемые функции.
- Планы по изменениям.

Эта страница обновляется ежемесячно, поэтому регулярно пересматривайте ее. Если вам понадобятся материалы, которым более шести месяцев, их можно найти в статье [Архив сведений о новых возможностях в Azure Active Directory](whats-new-archive.md).

---

## <a name="february-2020"></a>Февраль 2020 г.
 
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
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
В феврале 2020 мы добавили в коллекцию приложений 31 новые приложения с поддержкой федерации: 

[Иамип патентная платформа](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial), [опыт работы с ОБЛАКОМ](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial), [единый вход на основе NS1 для Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial), [Служба безопасности электронной почты Barracuda](https://ess.barracudanetworks.com/sso/azure), [ABa отчетность](https://myaba.co.uk/client-access/signin/auth/msad), [в случае аварийного перехода на Интернет-портал](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial), [БИК облачного проектирования](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial), [бикипер Connector данных Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial), [Korn паром](https://www.kornferry.com/solutions/kf-digital/kf-assess), [команда Веркада](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial), [сплаштоп](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial), [сикссенсе](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial), [еаб навигации](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial), [New Relic (ограниченный выпуск)](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial), [сулиум](https://admin.thulium.com/login/instance), [Диспетчер билетов](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial), [шаблон Chooser для команд](https://links.officeatwork.com/templatechooser-download-teams), [пчел](https://www.beesy.me/index.php/site/login), [системы поддержки работоспособности](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial), [Мурал](https://app.mural.co/signup), [Hive](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial), [Лавадо](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview), [вакелет](https://wakelet.com/login), [фирмекс ВДР](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial), [синглинк для преподавателей и школ](https://www.thinglink.com/), [Coda](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial), [неарподапп](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [ведо](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial), [инвитепеопле](https://invitepeople.com/login), [Перепечатка Desk-article Galaxy](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial), [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Новые соединители подготовки в коллекции приложений Azure AD — Февраль 2020

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
Теперь вы можете автоматизировать создание, обновление и удаление учетных записей пользователей для новых интегрированных приложений:

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [Пуреклауд по Женесис](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

Дополнительные сведения о том, как лучше защитить организацию с помощью автоматизированной подготовки учетных записей пользователей, см. в статье [Автоматизация подготовки пользователей в приложениях SaaS с помощью Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Поддержка Azure AD ключей безопасности FIDO2 в гибридных средах

**Тип.** Новая функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Проверка подлинности пользователей.
 
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
 
Новый интерфейс пользователя моей учетной записи будет обновлять его URL-адрес на https://myaccount.microsoft.com в следующем месяце. Дополнительные сведения о работе и всех возможностях самообслуживания учетных записей, которые она предлагает конечным пользователям на [портале учетных записей, см](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview). здесь.

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
 
Рабочие области — Фильтры, которые администраторы могут настроить для организации своих приложений, теперь будут называться коллекциями. Дополнительные сведения о том, как настроить их на [портале "Мои приложения",](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)см. здесь.

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Azure AD B2C регистрации телефона и входа с помощью настраиваемой политики (общедоступная Предварительная версия)

**Тип.** Новая функция.  
**Категория службы.** B2C — управление удостоверениями клиентов.  
**Возможности продукта.** B2B и B2C.
 
При регистрации и входе в систему разработчики и предприятия могут позволить своим клиентам регистрироваться и входить с помощью одноразового пароля, отправленного на телефонный номер пользователя через SMS. Эта функция также позволяет клиенту изменить свой номер телефона в случае утери доступа к телефону. Благодаря возможности пользовательских политик регистрация и вход с помощью телефона позволяют разработчикам и предприятиям обмениваться своими торговыми марками через настройку страниц. Узнайте, как [настроить регистрацию и вход в систему с помощью пользовательских политик в Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication).
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Новые соединители подготовки в коллекции приложений Azure AD — Январь 2020

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
Теперь вы можете автоматизировать создание, обновление и удаление учетных записей пользователей для новых интегрированных приложений:

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

Дополнительные сведения о том, как лучше защитить организацию с помощью автоматизированной подготовки учетных записей пользователей, см. в статье [Автоматизация подготовки пользователей в приложениях SaaS с помощью Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Новые Федеративные приложения, доступные в коллекции Azure AD App-Январь 2020

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
В январе 2020 мы добавили следующие 33 новых приложений с поддержкой Федерации в коллекцию приложений: 

[Жоса](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial), [высокореберное облако](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial), [terraform Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial), [Спинтр SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial), [абибот Нетлогистик](https://1030-review-develop-3zknud.netlogistik.com/), [скикикк облачное резервное копирование для Office 365](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access) [,,](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial) [леавебот](https://leavebot.io/#home), [Camp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial), [Трипактионс](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial), [смартворк](https://www.intumit.com/english/SmartWork.html), [Дотком-Monitor](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial), [Ссожен — шлюз единого входа Azure AD для Oracle E-бизнес Suite — EBS, PeopleSoft и жде](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial), [Hosted MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial), [Yuhu платформы управления свойствами](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial), [LumApps](https://sites.lumapps.com/login), [Талентсофт](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial), [предприятие](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial), [смартдб для Microsoft Teams](http://teams.smartdb.jp/login/), [Пресспаже](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial), [контрактсафе Saml2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial), максиент по работе с [менеджерами](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial), [хелпшифт](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial), [Порталталк 365](https://www.portaltalk.com/), [сообзор](https://portal.coreview.com/), [скуелч Cloud Office 365 соединитель](https://laxmi.squelch.io/login), [пингфлов Authentication](https://app-staging.pingview.io/), [PrinterLogic SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial), [Taskize Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial), [Sandwai](https://app.sandwai.com/), [EZRentOut](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial), [AssetSonar](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial), [Akari Виртуальный помощник](https://akari.io/akari-virtual-assistant/)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="two-new-identity-protection-detections"></a>Два новых обнаружения защиты идентификации

**Тип.** Новая функция.  
**Категория службы.** Защита идентификации.  
**Возможности продукта.** Безопасность и защита удостоверений.
 
Мы добавили два новых типа связывания для входа в систему защиты идентификации: подозрительные правила обработки входящих сообщений и невозможность перемещения. Эти обнаружения в автономном режиме обнаруживаются Microsoft Cloud App Security (МКАС) и влияют на риск пользователя и входа в систему защиты идентификации. Дополнительные сведения об этих обнаружениях см. в статье [типы рисков для входа в](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk)систему.
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Критическое изменение: фрагменты кода URI не будут проходить через перенаправление имени входа

**Тип.** Измененная функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Проверка подлинности пользователей.
 
Начиная с 8 февраля 2020 г. при отправке запроса в login.microsoftonline.com для входа пользователя служба добавит в запрос пустой фрагмент.  Это не позволяет классу атак перенаправления, гарантируя, что браузер очищает любой существующий фрагмент в запросе. Ни одно приложение не должно иметь зависимости от этого поведения. Дополнительные сведения см. в разделе [критические изменения](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020) в документации по платформе удостоверений Майкрософт.

---

## <a name="december-2019"></a>Декабрь 2019 г.

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>Интеграция подготовки SAP SuccessFactors в Azure AD и локальную службу AD (общедоступная Предварительная версия)

**Тип.** Новая функция.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта.** Управление жизненным циклом удостоверений.

Теперь вы можете интегрировать SAP SuccessFactors в качестве полномочного источника удостоверений в Azure AD. Эта интеграция помогает автоматизировать комплексный жизненный цикл идентификации, включая использование событий на основе HR, таких как новые сотрудники или прекращения, для управления подготовкой учетных записей Azure AD.

Дополнительные сведения о настройке входящей подготовки SAP SuccessFactors в Azure AD см. в руководстве по [настройке автоматической подготовки SAP SuccessFactors](https://aka.ms/SAPSuccessFactorsInboundTutorial) .

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Поддержка настраиваемых сообщений электронной почты в Azure AD B2C (общедоступная Предварительная версия)

**Тип.** Новая функция.  
**Категория службы.** B2C — управление удостоверениями клиентов.  
**Возможности продукта.** B2B и B2C.

Теперь вы можете использовать Azure AD B2C для создания настраиваемых сообщений электронной почты, когда пользователи подписываются для использования ваших приложений. Используя Дисплайконтролс (в настоящее время в предварительной версии) и сторонний поставщик электронной почты (например, [SendGrid](https://sendgrid.com/), [SparkPost](https://sparkpost.com/)или настраиваемый REST API), можно использовать собственный шаблон электронной почты, адрес отсчета и текст **темы, а** также поддерживать локализацию и пользовательские параметры одноразового пароля (OTP).

Дополнительные сведения см. [в разделе настраиваемая проверка электронной почты в Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-email).

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Замена базовых политик на параметры безопасности по умолчанию

**Тип.** Измененная функция.  
**Категория службы.** Другая.  
**Возможности продукта.** Безопасность и защита удостоверений.

В рамках безопасной модели по умолчанию для проверки подлинности удаляются существующие политики базовой защиты для всех клиентов. Это удаление предназначено для завершения в конце февраля. Замена этих политик базовой защиты по умолчанию имеет значение безопасности. Если вы использовали политики базовой защиты, необходимо запланировать переход на новую политику безопасности по умолчанию или на условный доступ. Если вы не использовали эти политики, вам не нужно предпринимать никаких действий.

Дополнительные сведения о новых значениях по умолчанию для системы безопасности см. в разделе [что такое параметры безопасности по умолчанию?](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) Дополнительные сведения о политиках условного доступа см. в разделе [Общие политики условного доступа](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common).

---

## <a name="november-2019"></a>Ноябрь 2019 г.

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>Поддержка атрибута SameSite и Chrome 80

**Тип.** Планирование изменений.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Проверка подлинности пользователей.

В рамках безопасной модели по умолчанию для файлов cookie браузер Chrome 80 изменяет, как он обрабатывает файлы cookie без атрибута `SameSite`. Любой файл cookie, не указывающий атрибут `SameSite`, будет рассматриваться так, как если бы он был установлен в `SameSite=Lax`, что приведет к блокированию Chrome некоторых сценариев совместного использования файлов cookie, которые могут зависеть от приложения. Чтобы сохранить старое поведение Chrome, можно использовать атрибут `SameSite=None` и добавить дополнительный атрибут `Secure`, чтобы межсайтовые файлы cookie могли быть доступны только через HTTPS-соединения. Chrome планирует выполнить это изменение на 4 февраля 2020.

Мы рекомендуем, чтобы наши разработчики протестируют свои приложения с помощью этого руководства:

- Задайте для параметра **использовать защищенный файл cookie** значение **Да**.

- Установите значение атрибута **SameSite** по умолчанию равным **None**.

- Добавьте дополнительный атрибут `SameSite` **Secure**.

Дополнительные сведения см. [в разделе предстоящие изменения файлов cookie SameSite в ASP.NET и ASP.NET Core](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) и [возможном нарушении работы веб-сайтов клиентов и продуктов и служб Майкрософт в Chrome версии 79 и более поздних](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79).

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Новое исправление для Microsoft Identity Manager (MIM) 2016 с пакетом обновления 2 (SP2)

**Тип.** Исправление.  
**Категория службы:** Microsoft Identity Manager  
**Возможности продукта.** Управление жизненным циклом удостоверений.

Накопительный пакет исправлений (сборка 4.6.34.0) доступен для Microsoft Identity Manager (MIM) 2016 с пакетом обновления 2 (SP2). Этот пакет исправлений устраняет проблемы и добавляет улучшения, описанные в разделе "исправленные проблемы и улучшения, добавленные в этом обновлении".

Дополнительные сведения и Загрузка пакета исправлений см. в разделе [накопительный пакет обновления 2 для Microsoft Identity Manager 2016 (сборка 4.6.34.0)](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r).

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Новый отчет о действиях AD FS приложений для переноса приложений в Azure AD (общедоступная Предварительная версия)

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Единый вход.

С помощью нового отчета о действиях приложений службы федерации Active Directory (AD FS) (AD FS) в портал Azure, чтобы указать, какие приложения могут быть перенесены в Azure AD. Этот отчет оценивает все AD FS приложения на совместимость с Azure AD, проверяет наличие проблем и предоставляет рекомендации по подготовке отдельных приложений к миграции.

Дополнительные сведения см. в статье [Использование отчета об активности приложений AD FS для переноса приложений в Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity).

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Новый рабочий процесс для запроса согласия администратора (общедоступная Предварительная версия)

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможность продукта.** Контроль доступа.

Новый рабочий процесс согласия администратора предоставляет администраторам способ предоставления доступа к приложениям, для которых требуется утверждение администратором. Если пользователь пытается получить доступ к приложению, но не может предоставить согласие, он теперь может отправить запрос на утверждение администратором. Запрос отправляется по электронной почте и помещается в очередь, доступную из портал Azure, ко всем администраторам, которые были обозначены как рецензенты. После того как проверяющий выполняет действие над ожидающим запросом, запрашивающие пользователи получают соответствующее действие.

Дополнительные сведения см. [в статье Настройка рабочего процесса согласия администратора (Предварительная версия)](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow).

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Новые возможности настройки маркеров регистрации Azure AD App для управления необязательными утверждениями (общедоступная Предварительная версия)

**Тип.** Новая функция.  
**Категория службы.** Другая.  
**Возможности продукта.** Возможности для разработчиков

В колонке "Новая **Конфигурация токенов Azure AD App регистрации** " на портал Azure Теперь разработчики приложений получают динамический список необязательных утверждений для своих приложений. Этот новый интерфейс упрощает миграцию приложений Azure AD и минимизирует необязательные настройки заявок.

Дополнительные сведения см. [в статье предоставление дополнительных утверждений для приложения Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Новый рабочий процесс утверждения с двумя этапами в управлении назначениями Azure AD (общедоступная Предварительная версия)

**Тип.** Новая функция.  
**Категория службы.** Другая.  
**Возможности продукта:** Управление назначениями

Мы предоставили новый рабочий процесс утверждения с двумя этапами, который позволяет потребовать от двух утверждающих утвердить запрос пользователя в пакет Access. Например, вы можете задать его, чтобы менеджер запрашивающего пользователя сначала утвердил, а затем вы также можете запросить утверждение у владельца ресурса. Если один из утверждающих не утверждается, доступ не предоставляется.

Дополнительные сведения см. [в статье изменение параметров запросов и утверждений для пакета Access в разделе "Управление назначениями Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy)".

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Обновления страницы "Мои приложения" вместе с новыми рабочими областями (общедоступная Предварительная версия)

**Тип.** Новая функция.  
**Категория службы.** "Мои приложения".  
**Возможности продукта.** Интеграция с решениями сторонних производителей.

Теперь вы можете настроить способ просмотра и доступа пользователей вашей организации к обновленной работе моих приложений. В этот новый интерфейс также входит новая функция рабочих областей, которая упрощает поиск и организацию приложений для пользователей.

Дополнительные сведения о новом интерфейсе "Мои приложения" и создании рабочих областей см. в статье [создание рабочих областей на портале "Мои приложения"](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Поддержка Google Social ID для службы совместной работы Azure AD B2B (общая доступность)

**Тип.** Новая функция.  
**Категория службы.** B2B.  
**Возможности продукта.** Проверка подлинности пользователей.

Новая поддержка по использованию идентификаторов Google Social (учетных записей Gmail) в Azure AD упрощает совместную работу пользователей и партнеров. У ваших партнеров больше нет необходимости создавать и администрировать новую учетную запись Майкрософт. Теперь Microsoft Teams полностью поддерживает пользователей Google на всех клиентах, а также общие конечные точки проверки подлинности, связанные с клиентом.

Дополнительные сведения см. в статье [Добавление Google в качестве поставщика удостоверений для гостевых пользователей B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Мобильная поддержка Microsoft погранично для условного доступа и единого входа (общая доступность)

**Тип.** Новая функция.  
**Категория службы.** Условный доступ.  
**Возможности продукта.** Безопасность и защита удостоверений.

Azure AD для Microsoft ребр в iOS и Android теперь поддерживает единый вход и условный доступ Azure AD:

- **Единый вход Microsoft пограничных устройств (SSO):** Единый вход теперь доступен в собственных клиентах (например, Microsoft Outlook и Microsoft погранично) для всех приложений, подключенных к Azure AD.

- **Условный доступ Microsoft ребр:** С помощью политик условного доступа на основе приложений пользователи должны использовать защищенные Microsoft Intune браузеры, такие как Microsoft ребро.

Дополнительные сведения об условном доступе и единый вход с помощью Microsoft ребр см. в записи блога о [поддержке Microsoft погранично Mobile для условного доступа и единого входа](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) . Дополнительные сведения о настройке клиентских приложений с помощью [условного доступа на основе приложений](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) или [условного доступа на основе устройств](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)см. в статье [Управление веб-доступом с помощью браузера Microsoft Intune, защищенного политикой](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser).

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Управление назначениями Azure AD (общедоступная доступность)

**Тип.** Новая функция.  
**Категория службы.** Другая.  
**Возможности продукта:** Управление назначениями

Управление назначением Azure AD — это новая функция управления удостоверениями, которая помогает организациям управлять жизненным циклом удостоверений и доступа в масштабе. Эта новая функция позволяет автоматизировать рабочие процессы запросов на доступ, назначения доступа, обзоры и срок действия групп, приложений и сайтов SharePoint Online.

С помощью управления назначением Azure AD можно более эффективно управлять доступом для сотрудников, а также для пользователей за пределами Организации, которым необходим доступ к этим ресурсам.

Дополнительные сведения см. в статье [что такое управление запасами в Azure AD?](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Автоматизация подготовки учетных записей пользователей для новых поддерживаемых приложений SaaS

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.  

Теперь вы можете автоматизировать создание, обновление и удаление учетных записей пользователей для новых интегрированных приложений:

[SAP Cloud Platform Identity Authentication Service](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial), [рингцентрал](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial), [SpaceIQ](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial), [Миро](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial), [клаудгате](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial), [Инфо клаудсуите](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial), [программное обеспечение officespace Software](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial), [Матрица приоритетов](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

Дополнительные сведения о том, как лучше защитить организацию с помощью автоматизированной подготовки учетных записей пользователей, см. в статье [Автоматизация подготовки пользователей в приложениях SaaS с помощью Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Новые Федеративные приложения, доступные в коллекции Azure AD App — Ноябрь 2019

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.

В ноябре 2019 мы добавили 21 новые приложения с поддержкой Федерации в коллекцию приложений:

[Аиртабле](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial), [Хутсуите](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial), [синий доступ для участников (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial), [Битли](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial), [Рива](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [реслифе Portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [негометрикспортал единый вход (SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial), [теамсчамп](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), [Мотус](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial), [мяряка](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial), [блуемаил](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial), [OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial), [foko Retail](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial), Qmarkets [идея & управления инновациями](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial), [Netskope аутентификация пользователей](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial), [uniFLOW Online](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial), [Claromentis](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial) , [JISC Student избирателей Registration](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial), [e4enable](https://portal.e4enable.com/)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Новая и улучшенная коллекция приложений Azure AD

**Тип.** Измененная функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Единый вход.

Мы обновили коллекцию приложений Azure AD, чтобы упростить поиск предварительно интегрированных приложений, поддерживающих подготовку, OpenID Connect Connect и SAML в клиенте Azure Active Directory.

Дополнительные сведения см. в статье [Добавление приложения в клиент Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal).

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Увеличено ограничение длины определения роли приложения с 120 до 240 символов

**Тип.** Измененная функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Единый вход.

Мы слышали о том, что длина значения определения роли приложения в некоторых приложениях и службах слишком мала в 120 символов. В ответ мы увеличили максимальную длину определения значения роли до 240 символов.

Дополнительные сведения об использовании определений ролей для конкретных приложений см. в разделе [Добавление ролей приложения в приложение и их получение в токене](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps).

---

## <a name="october-2019"></a>Октябрь 2019 г.

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Нерекомендуемый API Идентитирискевент для обнаружения рисков защита идентификации Azure AD  

**Тип.** Планирование изменений.  
**Категория службы.** Защита идентификации.  
**Возможности продукта.** Безопасность и защита удостоверений.

В ответ на Отзывы разработчиков Azure AD Premium Подписчики P2 теперь могут выполнять сложные запросы к данным обнаружения рисков защита идентификации Azure AD, используя новый API Рискдетектион для Microsoft Graph. Существующая бета-версия [идентитирискевент](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) API перестанет возвращать данные **10 января 2020 г**. Если ваша организация использует API Идентитирискевент, следует перейти на новый API Рискдетектион.

Дополнительные сведения о новом API Рискдетектион см. в [справочной документации по API обнаружения рисков](https://aka.ms/RiskDetectionsAPI).

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Поддержка прокси приложения для атрибута SameSite и Chrome 80

**Тип.** Планирование изменений.  
**Категория службы.** Прокси-сервер приложения.  
**Возможность продукта.** Контроль доступа.

Несколько недель до выпуска браузера Chrome 80 мы планируем обновить, как файлы cookie прокси приложения обрабатывают атрибут **SameSite** . В выпуске Chrome 80 любой файл cookie, не указывающий атрибут **SameSite** , будет рассматриваться так, как если бы он был установлен в `SameSite=Lax`.

Чтобы избежать потенциально негативных последствий из-за этого изменения, мы обновляем доступ прокси приложения и файлы cookie сеанса, выполнив следующие действия.

- Установка значения по умолчанию для параметра **использовать защищенный файл cookie** имеет значение **Да**.

- Установка значения по умолчанию для атрибута **SameSite** равным **None**.

    >[!NOTE]
    > Файлы cookie для доступа к прокси приложения всегда передаются исключительно по защищенным каналам. Эти изменения применяются только к файлам cookie сеанса.

Дополнительные сведения о параметрах cookie прокси приложения см. [в разделе Параметры файлов cookie для доступа к локальным приложениям в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>Регистрация приложений (устаревшая) и Управление приложениями с конвергенцией на портале регистрации приложений (apps.dev.microsoft.com) больше не будут доступны.

**Тип.** Планирование изменений.  
**Категория службы:** недоступно  
**Возможности продукта.** Возможности для разработчиков

В ближайшем будущем пользователи с учетными записями Azure AD больше не смогут регистрировать приложения и управлять ими с помощью портала регистрации приложений (apps.dev.microsoft.com), а также регистрировать приложения и управлять ими в Регистрация приложений (прежние версии). опыт работы в портал Azure.

Дополнительные сведения о новом Регистрация приложений интерфейсе см. в [Регистрация приложений в учебном курсе по портал Azure](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md).

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Пользователям больше не требуется повторная регистрация во время миграции с MFA для каждого пользователя на MFA на основе условного доступа.

**Тип.** Исправление.  
**Категория службы:** MFA  
**Возможности продукта.** Безопасность и защита удостоверений.

Исправлена известная проблема, при которой пользователям было необходимо повторно зарегистрироваться, если они были отключены для многофакторной проверки подлинности (MFA) для каждого пользователя, а затем включены для MFA через политику условного доступа.

Чтобы требовать повторной регистрации пользователей, можно выбрать **требуемую функцию повторной регистрации MFA** в методах проверки подлинности пользователя на портале Azure AD. Дополнительные сведения о переносе пользователей из MFA для каждого пользователя в MFA на основе условного доступа см. в статье [Преобразование пользователей из многопользовательского MFA в условный доступ на основе MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Новые возможности для преобразования и отправки утверждений в токене SAML

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Единый вход.

Мы добавили дополнительные возможности, которые помогут вам настроить и отправить утверждения в токене SAML. К этим новым возможностям относятся:

- Дополнительные функции преобразования утверждений, помогающие изменить значение, отправляемое в утверждении.

- Возможность применения нескольких преобразований к одному утверждению.

- Возможность указать источник утверждения на основе типа пользователя и группы, к которой принадлежит пользователь.

Подробные сведения об этих новых возможностях, включая способы их использования, см. [в разделе Настройка утверждений, выданных в токене SAML для корпоративных приложений](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Страница "Создание входа" для конечных пользователей в Azure AD

**Тип.** Новая функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Мониторинг и отчетность.

Мы добавили новую страницу " **мои входы** " (https://mysignins.microsoft.com), чтобы пользователи вашей организации могли просматривать свои недавние истории входа, чтобы проверить любое необычное действие. Эта новая страница позволяет пользователям видеть следующее:

- Если кто-то пытается угадать свой пароль.

- Если злоумышленник успешно выполнил вход в учетную запись и из расположения.

- Приложения, к которым злоумышленник пытался получить доступ.

Дополнительные сведения см. в статье " [Пользователи теперь могут проверить журнал входа для необычного блога действий](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) ".

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Миграция доменных служб Azure AD (AD DS Azure) с классической на Azure Resource Manager виртуальные сети

**Тип.** Новая функция.  
**Категория службы.** Доменные службы Azure AD.  
**Возможности продукта.** Доменные службы Azure AD.

Для наших клиентов, которые задержали классические виртуальные сети, у нас есть отличная новость! Теперь вы можете выполнить однократную миграцию из классической виртуальной сети в существующую виртуальную сеть диспетчер ресурсов. После перехода на виртуальную сеть диспетчер ресурсов вы сможете воспользоваться преимуществами дополнительных и обновленных функций, таких как, детализированные политики паролей, уведомления по электронной почте и журналы аудита.

Дополнительные сведения см. [в статье предварительный просмотр — перенос доменных служб Azure AD из классической модели виртуальной сети в Диспетчер ресурсов](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet).

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Обновления макета контракта страницы Azure AD B2C

**Тип.** Новая функция.  
**Категория службы.** B2C — управление удостоверениями клиентов.  
**Возможности продукта.** B2B и B2C.

Мы предоставили некоторые новые изменения в версии 1.2.0 контракта страницы для Azure AD B2C. В этой обновленной версии теперь можно управлять порядком загрузки элементов, что также может помочь предотвратить мерцание, происходящее при загрузке таблицы стилей (CSS).

Полный список изменений, внесенных в контракт страницы, см. в [журнале изменений версии](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120).

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Обновление страницы "Мои приложения" вместе с новыми рабочими областями (общедоступная Предварительная версия)

**Тип.** Новая функция.  
**Категория службы.** "Мои приложения".  
**Возможность продукта.** Контроль доступа.

Теперь вы можете настроить способ просмотра и доступа пользователей вашей организации к новым возможностям моего приложения, включая использование новых рабочих областей, чтобы упростить поиск приложений. Новые функции рабочих областей действуют как фильтры для приложений, к которым у пользователей вашей организации уже есть доступ.

Дополнительные сведения о развертывании нового интерфейса "Мои приложения" и создании рабочих областей см. в статье [создание рабочих областей на портале "Мои приложения" (Предварительная версия)](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Поддержка месячной активной модели выставления счетов на основе пользователей (общая доступность)

**Тип.** Новая функция.  
**Категория службы.** B2C — управление удостоверениями клиентов.  
**Возможности продукта.** B2B и B2C.

Azure AD B2C теперь поддерживает выставление счетов за ежемесячные активные пользователи (MAU). Выставление счетов за MAU зависит от количества уникальных пользователей с действиями проверки подлинности в календарном месяце. Существующие клиенты могут в любое время переключиться на этот новый метод выставления счетов.

Начиная с 1 ноября 2019 г. все новые клиенты будут автоматически оплачиваться с помощью этого метода. Этот метод выставления счетов позволяет клиентам пользоваться преимуществами затрат и возможность планироваться заранее.

Дополнительные сведения см. [в статье модель выставления счетов активных пользователей с обновлением до месяца](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Новые Федеративные приложения, доступные в коллекции Azure AD App — Октябрь 2019

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.

В октябре 2019 мы добавили следующие 35 новых приложений с поддержкой Федерации в коллекцию приложений:

[В случае критического: Mobile](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial), [Juno путешествие](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial), [Експоненср](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial), [Tact](https://tact.ai/assistant/)опускапита, [Управление денежными](http://cm1.opuscapita.com/tenantname)средствами, [салестим](https://prd.salestim.io/forms), [Леарнстер](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial), [dynaTrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial), [хунчбузз](https://login.hunchbuzz.com/integrations/azure/process), [фрешворкс](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial), [Екорнелл](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial), [шифазмат](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial), [нетскопе Cloud Security](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial), [contenting](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial), [Bindtuning](https://bindtuning.com/login), [HireVue координировать — ЕС](https://www.hirevue.com/), [HireVue координировать-USOnly](https://www.hirevue.com/), [HireVue координировать-US](https://www.hirevue.com/), [WittyParrot Область знаний](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [клаудморе](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial), [Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial), [камбиум ксиррус еасипасс Portal](https://login.xirrus.com/azure-signup), [Paylocity](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial), ["почта"!](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial), [Группа](https://theteamie.com/), [скорость для групп](https://velocity.peakup.org/teams/login), [SIGNL4](https://account.signl4.com/manage), [еаб навигации по Impl](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial), [Скринмит](https://console.screenmeet.com/), [Омега Point](https://pi.ompnt.com/), [выступление электронной почты для Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [выступление электронной почты для Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ексакткаре SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial), [ихеалсхоме системы навигации](https://ihealthnav.com/account/signin) [ Кубие](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Элемент меню "консолидированная безопасность" на портале Azure AD

**Тип.** Измененная функция.  
**Категория службы.** Защита идентификации.  
**Возможности продукта.** Безопасность и защита удостоверений.

Теперь вы можете получить доступ ко всем доступным функциям безопасности Azure AD из нового пункта меню " **Безопасность** " и из панели **поиска** в портал Azure. Кроме того, Новая целевая страница **безопасности** , именуемая « **Начало работы**», будет предоставлять ссылки на нашу общедоступную документацию, руководство по безопасности и руководства по развертыванию.

Новое меню **безопасности** включает в себя:

- Условный доступ
- Защита идентификации
- Центр безопасности
- Оценка безопасности удостоверений
- Методы проверки подлинности
- MFA
- Отчеты о рисках — рискованные пользователи, рискованные входы, обнаружение рисков
- И многое другое!

Дополнительные сведения см. в разделе [Приступая к работе с безопасностью](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted).

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Расширенная политика срока действия групп Office 365 с автопродлением

**Тип.** Измененная функция.  
**Категория службы.** Управление группами.  
**Возможности продукта.** Управление жизненным циклом удостоверений.

Политика срока действия групп Office 365 была улучшена для автоматического продления групп, активно используемых ее членами. Группы будут обновляться на основе действий пользователей во всех приложениях Office 365, включая Outlook, SharePoint и команды.

Это улучшение позволяет сократить количество уведомлений об истечении срока действия групп и помогает убедиться в том, что активные группы остаются доступными. Если у вас уже есть активная политика срока действия для групп Office 365, вам не нужно ничего делать, чтобы включить эту новую функциональность.

Дополнительные сведения см. [в статье Настройка политики срока действия для групп Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle).

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Обновлен интерфейс создания доменных служб Azure AD (Azure AD DS)

**Тип.** Измененная функция.  
**Категория службы.** Доменные службы Azure AD.  
**Возможности продукта.** Доменные службы Azure AD.

Мы обновили доменные службы Azure AD (Azure AD DS), чтобы включить новый и Улучшенный интерфейс создания, что поможет вам создать управляемый домен всего за три щелчка! Кроме того, теперь вы можете отправлять и развертывать AD DS Azure из шаблона.

Дополнительные сведения см. [в разделе Учебник. Создание и настройка Azure Active Directory экземпляра доменных служб](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance).

---

## <a name="september-2019"></a>Сентябрь 2019 г.

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Планирование изменений: прекращение использования пакетов содержимого Power BI

**Тип.** Планирование изменений.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.

Начиная с 1 октября 2019 г. Power BI начнет использовать все пакеты содержимого, включая пакет содержимого Azure AD Power BI. В качестве альтернативы этому пакету содержимого можно использовать книги Azure AD для получения ценных сведений о службах, связанных с Azure AD. Появятся дополнительные книги, включая книги о политиках условного доступа в режиме «только отчет», «аналитика на основе согласия приложений» и т. д.

Дополнительные сведения о книгах см. в разделе [Использование книг Azure Monitor для Azure Active Directory отчетов](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks). Дополнительные сведения о прекращении использования пакетов содержимого см. в записи блога о [общедоступной версии приложений Power BI шаблонов](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) .

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>Мой профиль переименовывается и интегрируется со страницей учетной записи Microsoft Office

**Тип.** Планирование изменений.  
**Категория службы:** Мой профиль или учетная запись  
**Возможности продукта.** Совместная работа.

Начиная с октября, мой профиль пользователя станет моей учетной записью. В рамках этого изменения везде, где в настоящий момент написано, **Мой профиль** изменится на **Моя учетная запись**. В начале изменения именования и некоторых улучшений в разработке обновленный интерфейс будет предоставлять дополнительную интеграцию со страницей учетной записи Microsoft Office. В частности, вы сможете получить доступ к установкам и подпискам Office со страницы **обзорной учетной записи** , а также с контактными данными, связанными с Office, на странице **Конфиденциальность** .

Дополнительные сведения о работе с моим профилем (Предварительная версия) см. в [обзоре портала "Мой профиль (Предварительная версия)](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview)".

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Управление группами и членами с помощью CSV-файлов на портале Azure AD (общедоступная Предварительная версия)

**Тип.** Новая функция.  
**Категория службы.** Управление группами.  
**Возможности продукта.** Совместная работа.

Мы рады сообщить о доступности общедоступной предварительной версии функций управления массовыми группами на портале Azure AD. Теперь вы можете использовать CSV-файл и портал Azure AD для управления группами и списками членов, в том числе:

- Добавление или удаление членов из группы.

- Скачивание списка групп из каталога.

- Скачивание списка членов группы для определенной группы.

Дополнительные сведения см. в разделе [Пакетное добавление членов](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members), [Групповое удаление членов](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members), [список загрузки участников](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)и [список групп загрузки](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>Динамическое согласие теперь поддерживается через новую конечную точку согласия администратора.

**Тип.** Новая функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Проверка подлинности пользователей.

Мы создали новую конечную точку согласия администратора для поддержки динамического согласия, что полезно для приложений, которые хотят использовать модель динамического согласия на платформе Microsoft Identity.

Дополнительные сведения о том, как использовать эту новую конечную точку, см. [в разделе Использование конечной точки согласия администратора](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Новые Федеративные приложения, доступные в коллекции Azure AD App-Сентябрь 2019

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.

В сентябре 2019 мы добавили в коллекцию приложений следующие 29 новых приложений с поддержкой федерации:

[Счедулелук](https://schedulelook.bbsonlineservices.net/), Microsoft [Cintoo Cloud](https://aec.cintoo.com/login) [Azure SSO Access для есидекс соответствия требованиям Office™ — единый вход](https://docs.microsoft.com/azure/active-directory/saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial), [iServer Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/iserver-portal-tutorial), [скисите](https://docs.microsoft.com/azure/active-directory/saas-apps/skysite-tutorial), [Concur командировок и расходов](https://docs.microsoft.com/azure/active-directory/saas-apps/concur-travel-and-expense-tutorial), [воркбоард](https://docs.microsoft.com/azure/active-directory/saas-apps/workboard-tutorial), [еефлов](https://apps.yeeflow.com/), [Arc](https://docs.microsoft.com/azure/active-directory/saas-apps/arc-facilities-tutorial), [луваре Стратус Team](https://stratus.emea.luware.cloud/login), [широкие идеи](https://wideideas.online/wideideas/), [присма Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial) [, ждлт, ренраку](https://clients.jdlt.co.uk/login) [,](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive)SealPath [, Облако](https://docs.microsoft.com/azure/active-directory/saas-apps/renraku-tutorial) [Prisma Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial) [Penneo](https://app.penneo.com/) [Hiretual](https://app.testhtm.com/settings/email-integration) [Whitesource](https://docs.microsoft.com/azure/active-directory/saas-apps/whitesource-tutorial), [размещенный Heritage Online SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-heritage-online-sso-tutorial), [IDC](https://docs.microsoft.com/azure/active-directory/saas-apps/idc-tutorial), [Какехр](https://docs.microsoft.com/azure/active-directory/saas-apps/cakehr-tutorial), [BIS](https://docs.microsoft.com/azure/active-directory/saas-apps/bis-tutorial), [директор по производству Кай Team Build](https://ms-contacts.coo-kai.jp/), [Sonarqube](https://docs.microsoft.com/azure/active-directory/saas-apps/sonarqube-tutorial), [Управление удостоверениями](https://docs.microsoft.com/azure/active-directory/saas-apps/adobe-identity-management-tutorial), возможности [обнаружения, преимущества единого входа](https://docs.microsoft.com/azure/active-directory/saas-apps/discovery-benefits-sso-tutorial), [Амелио](https://app.amelio.co/), [iTask](https://itask.yipinapp.com/)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-global-reader-role"></a>Новая роль глобального читателя Azure AD

**Тип.** Новая функция.  
**Категория службы:** RBAC  
**Возможность продукта.** Контроль доступа.

Начиная с 24 сентября 2019 г. начинается развертывание новой роли Azure Active Directory (AD) с именем Global Reader. Это развертывание будет начинаться с корпоративных и глобальных облачных клиентов (GCC), в октябре.

Роль глобального читателя — это аналог глобального администратора, предназначенный только для чтения. Пользователи с этой ролью могут считывать параметры и административные данные в службах Microsoft 365 Services, но не могут выполнять действия по управлению. Мы создали глобальную роль читателя, которая поможет сократить число глобальных администраторов в Организации. Поскольку учетные записи глобального администратора являются мощными и уязвимыми для атак, рекомендуется иметь менее пяти глобальных администраторов. Мы рекомендуем использовать глобальную роль читателя для планирования, аудита или исследования. Мы также советуем использовать глобальную роль читателя в сочетании с другими ограниченными ролями администратора, например с администратором Exchange, чтобы обеспечить работу без использования роли глобального администратора.

Роль глобального читателя работает с новым центром администрирования Microsoft 365, центром администрирования Exchange, центром администрирования группы, центром безопасности, центром соответствия, центром администрирования Azure AD и центром администрирования управления устройствами.

>[!NOTE]
> В начале общедоступной предварительной версии роль глобального устройства чтения не будет работать с: SharePoint, Privileged Access Management, защищенное хранилище, метки чувствительности, жизненный цикл групп, отчеты по командам & аналитика звонков, команды для управления телефонными устройствами и приложения Teams Каталога. Все эти службы предназначены для работы с ролью в будущем.

Дополнительные сведения см. [в разделе разрешения роли администратора в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Доступ к локальному серверу отчетов из приложения Power BI Mobile с помощью Azure Active Directory Application Proxy

**Тип.** Новая функция.  
**Категория службы.** Прокси-сервер приложения.  
**Возможность продукта.** Контроль доступа.

Новая интеграция между мобильным приложением Power BI и Azure AD Application Proxy позволяет безопасно входить в Power BI мобильное приложение и просматривать отчеты Организации, размещенные на локальном Сервер отчетов Power BI.

Сведения о Power BI Mobile приложении, включая расположение для скачивания приложения, см. на [сайте Power BI](https://powerbi.microsoft.com/mobile/). Дополнительные сведения о настройке Power BI мобильного приложения с помощью AD Application Proxy Azure см. в статье [Включение удаленного доступа для Power BI Mobile с помощью azure AD application proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>Доступна новая версия модуля PowerShell AzureADPreview

**Тип.** Измененная функция.  
**Категория службы.** Другая.  
**Возможности продукта.** Каталог.

В модуль AzureADPreview были добавлены новые командлеты, которые помогут определить и назначить пользовательские роли в Azure AD, в том числе:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Новая версия Azure AD Connect

**Тип.** Измененная функция.  
**Категория службы.** Другая.  
**Возможности продукта.** Каталог.

Выпущена обновленная версия Azure AD Connect для автоматического обновления клиентов. Эта новая версия включает несколько новых функций, улучшений и исправлений ошибок. Дополнительные сведения об этой новой версии см. в разделе [Azure AD Connect: История выпусков версий](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250).

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Теперь доступен сервер многофакторной идентификации Azure (MFA) версии версии 8.0.2.

**Тип.** Исправление.  
**Категория службы:** MFA  
**Возможности продукта.** Безопасность и защита удостоверений.

Если вы являетесь существующим клиентом, который активировал сервер MFA до 1 июля 2019, теперь можно скачать последнюю версию сервера MFA (версия версии 8.0.2). В этой новой версии мы:

- Исправлена проблема, поэтому когда Azure AD Sync изменяет пользователя с отключенным на Enabled, пользователю отправляется сообщение электронной почты.

- Исправлена проблема, поэтому клиенты могут успешно обновиться, продолжая использовать функции тегов.

- Добавлен код страны Косово (+ 383).

- В MultiFactorAuthSvc. log добавлен однократный обход журнала аудита.

- Улучшена производительность для пакета SDK веб-службы.

- Исправлены другие незначительные ошибки.

С 1 июля 2019 г. Корпорация Майкрософт прекратила предлагать сервер MFA для новых развертываний. Новые клиенты, которым требуется многофакторная проверка подлинности, должны использовать службу многофакторной идентификации Azure на основе облака. Дополнительные сведения см. [в статье Планирование развертывания многофакторной идентификации Azure на основе облачных служб](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---
