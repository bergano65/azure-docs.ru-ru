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
ms.date: 04/06/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 491908b651cd2b875fcfeed4c55d34f0e8c5cfa1
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802515"
---
# <a name="whats-new-in-azure-active-directory"></a>Новые возможности Azure Active Directory

>Получайте уведомления о том, когда вернуться на эту страницу `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` для ![обновления, копируя и вставляя этот URL: в ваш RSS канал чтения значок](./media/whats-new/feed-icon-16x16.png) читателя читателя читателя читателя читателя.

Azure AD усовершенствуется на постоянной основе. Чтобы вы оставались в курсе последних разработок, в этой статье предоставлены такие сведения:

- Последние выпуски.
- Известные проблемы
- Исправления ошибок
- Нерекомендуемые функции.
- Планы по изменениям.

Эта страница обновляется ежемесячно, поэтому регулярно пересматривайте ее. Если вам понадобятся материалы, которым более шести месяцев, их можно найти в статье [Архив сведений о новых возможностях в Azure Active Directory](whats-new-archive.md).

---

## <a name="march-2020"></a>Март 2020 г.

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>Неуправляемые учетные записи Active Directory В B2B-обновлении за март 2021 года

**Тип.** Планирование изменений.  
**Категория службы.** B2B.  
**Возможности продукта.** B2B и B2C.
 
**Начиная с 31 марта 2021**года, корпорация Майкрософт больше не будет поддерживать выкуп приглашений, создавая неуправляемые учетные записи Active Directory (Azure AD) и наниматели для сценариев совместной работы B2B. В рамках подготовки к этому, мы рекомендуем вам выбрать в [электронной почте одноразовый проверки подлинности пароля](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode).

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Пользователи с ролью доступа по умолчанию будут в области подготовки

**Тип.** Планирование изменений.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта:** Управление жизненным циклом идентичности
 
Исторически сложилось так, что пользователи, выполняющие роль доступа по умолчанию, были вне сферы подготовки. Мы слышали отзывы о том, что клиенты хотят, чтобы пользователи с этой ролью были в возможности для подготовки. Мы работаем над развертыванием изменений, чтобы все новые конфигурации подготовки позволяли предусмотреть пользователям, с ролью доступа по умолчанию. Постепенно мы изменим поведение существующих конфигураций подготовки для поддержки подготовки пользователей этой ролью. Никаких действий клиента не требуется. Мы опубликуем обновление в нашей [документации,](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned) как только это изменение будет на месте.

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Совместное сотрудничество Azure AD B2B будет доступно в Microsoft Azure, управляемом 21Вианет (Azure China 21Vianet)

**Тип.** Планирование изменений.  
**Категория службы.** B2B.  
**Возможности продукта.** B2B и B2C.
 
Возможности совместной работы Azure AD B2B будут доступны в Microsoft Azure, управляемых 21Vianet (Azure China 21Vianet), что позволит пользователям в azure China 21Vianet беспрепятственно сотрудничать с пользователями в других арендаторах Azure China 21Vianet. [Узнайте больше о совместной работе Azure AD B2B.](https://docs.microsoft.com/azure/active-directory/b2b/)

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Редизайн приглашения по электронной почте Azure AD B2B

**Тип.** Планирование изменений.  
**Категория службы.** B2B.  
**Возможности продукта.** B2B и B2C.
 
Электронные [письма,](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) отправленные службой приглашения совместной работы Azure AD B2B для приглашения пользователей в каталог, будут переработаны, чтобы сделать информацию о приглашении и следующие шаги пользователя более четкими.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>Изменения в политике HomeRealmDiscovery появятся в журналах аудита

**Тип.** Исправление.  
**Категория обслуживания:** Аудита  
**Возможности продукта.** Мониторинг и отчетность.
 
Мы исправили ошибку, когда изменения в [политике HomeRealmDiscovery](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) не были включены в журналы аудита. Теперь вы сможете увидеть, когда и как была изменена политика, и кем. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Новые федеративные приложения доступны в галерее Azure AD App - март 2020

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
В марте 2020 года мы добавили в галерею приложений 51 новое приложение с поддержкой Федерации: 

[Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect), [Зохо Один Китай](https://docs.microsoft.com/azure/active-directory/saas-apps/zoho-one-china-tutorial), [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/), [Profit.co SAML App](https://docs.microsoft.com/azure/active-directory/saas-apps/profitco-saml-app-tutorial), [iPoint поставщик услуг](https://docs.microsoft.com/azure/active-directory/saas-apps/ipoint-service-provider-tutorial), contexxt.ai [SPHERE](https://contexxt-sphere.com/login), [Мудрость Invictus](https://docs.microsoft.com/azure/active-directory/saas-apps/wisdom-by-invictus-tutorial), [Flare Digital Signage](https://spark-dev.pixelnebula.com/login), [Logz.io - Облако Наблюдаемость для инженеров](https://docs.microsoft.com/azure/active-directory/saas-apps/logzio-cloud-observability-for-engineers-tutorial), [Спектру](https://docs.microsoft.com/azure/active-directory/saas-apps/spectrumu-tutorial), [BizzContact](https://bizzcontact.app/), [Elqano S СО](https://docs.microsoft.com/azure/active-directory/saas-apps/elqano-sso-tutorial), [MarketSignShare](http://www.signshare.com/), [CrossKnowledge Обучение Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/crossknowledge-learning-suite-tutorial), [Netvision Compas](https://docs.microsoft.com/azure/active-directory/saas-apps/netvision-compas-tutorial), [FCM HUB](https://docs.microsoft.com/azure/active-directory/saas-apps/fcm-hub-tutorial), RIB A / S [Byggeweb Мобильный](https://apps.apple.com/us/app/docia/id529058757), [GoLinks](https://docs.microsoft.com/azure/active-directory/saas-apps/golinks-tutorial), [Datadog](https://docs.microsoft.com/azure/active-directory/saas-apps/datadog-tutorial), [Sscaler B2B Пользовательский портал](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-b2b-user-portal-tutorial), [LIFT](https://docs.microsoft.com/azure/active-directory/saas-apps/lift-tutorial), [Planview Предприятие Один](https://docs.microsoft.com/azure/active-directory/saas-apps/planview-enterprise-one-tutorial), [WatchTeams](https://www.devfinition.com/), [Aster](https://demo.asterapp.io/login), [Навыки Рабочий процесс](https://docs.microsoft.com/azure/active-directory/saas-apps/skills-workflow-tutorial), [Узел Insight](https://admin.nodeinsight.com/AADLogin.aspx), [IP-платформа](https://docs.microsoft.com/azure/active-directory/saas-apps/ip-platform-tutorial), [InVision](https://docs.microsoft.com/azure/active-directory/saas-apps/invision-tutorial), [Pipedrive](https://docs.microsoft.com/azure/active-directory/saas-apps/pipedrive-tutorial), [Витрина Семинар](https://app.showcaseworkshop.com/), [Greenlight Интеграция платформа](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-integration-platform-tutorial), [Greenlight Совместимое управление доступом](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-compliant-access-management-tutorial), [Grok Learning](https://docs.microsoft.com/azure/active-directory/saas-apps/grok-learning-tutorial), [Miradore Online](https://login.online.miradore.com/), [Khoros Care](https://docs.microsoft.com/azure/active-directory/saas-apps/khoros-care-tutorial), [AskYourTeam](https://docs.microsoft.com/azure/active-directory/saas-apps/askyourteam-tutorial), [TruNarrative](https://docs.microsoft.com/azure/active-directory/saas-apps/trunarrative-tutorial), [Smartwaiver](https://www.smartwaiver.com/m/user/sw_login.php?wms_login), [Bizagi Studio для цифровой автоматизации процессов](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial), [insuiteX](https://www.insuite.jp/), [sybo](https://www.systexsoftware.com.tw/), [Britive](https://docs.microsoft.com/azure/active-directory/saas-apps/britive-tutorial), [WhosOffice](https://docs.microsoft.com/azure/active-directory/saas-apps/whosoffice-tutorial), [E-days](https://docs.microsoft.com/azure/active-directory/saas-apps/e-days-tutorial), [Kollective SDN](https://portal.kollective.app/login), [Witivio](https://app.witivio.com/), [Playvox](https://my.playvox.com/login), [Korn Ferry 360](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-360-tutorial), [Campus Cafe](https://docs.microsoft.com/azure/active-directory/saas-apps/campus-cafe-tutorial), [Catchpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/catchpoint-tutorial), [Code42](https://docs.microsoft.com/azure/active-directory/saas-apps/code42-tutorial)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Сотрудничество Azure AD B2B доступно в клиентах правительства Azure

**Тип.** Новая функция.  
**Категория службы.** B2B.  
**Возможности продукта.** B2B и B2C.
 
Функции совместной работы Azure AD B2B теперь доступны между некоторыми арендаторами Azure Government.  Чтобы узнать, может ли ваш арендатор использовать эти возможности, следуйте инструкциям, как [я могу узнать, доступна ли совместная работа B2B в моем арендаторе Правительства Azure в США?.](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Интеграция Azure Monitor для журналов Azure теперь доступна в правительстве Azure

**Тип.** Новая функция.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.
 
Интеграция Azure Monitor с журналами Azure AD теперь доступна в правительстве Azure. Вы можете направлять журналы Azure AD (аудит и вход в журналы) в учетную запись хранения, концентратор событий и аналитику журналов. Пожалуйста, ознакомьтесь с [подробной документацией,](https://aka.ms/aadlogsinamd) а также [планами развертывания для отчетности и мониторинга](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting) сценариев Azure AD.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Обновление защиты идентификационных данных в правительстве Azure

**Тип.** Новая функция.  
**Категория обслуживания:** Защита личных данных  
**Возможности продукта.** Безопасность и защита удостоверений.

Мы рады поделиться тем, что теперь мы внедрили обновленный опыт  [защиты идентификационных данных Azure AD](https://aka.ms/IdentityProtectionDocs)на [правительственном портале Microsoft Azure.](https://portal.azure.us/) Для получения дополнительной информации, смотрите наше [объявление блоге](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667).

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Аварийное восстановление: Загрузите и храните конфигурацию подготовки

**Тип.** Новая функция.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта:** Управление жизненным циклом идентичности
 
Служба предоставления AD AD Azure предоставляет широкий набор возможностей конфигурации. Клиенты должны быть в состоянии сохранить свою конфигурацию, чтобы они могли обратиться к нему позже или откат к известной хорошей версии. Мы добавили возможность загрузить конфигурацию подготовки в виде файла JSON и загрузить ее, когда это необходимо. [Подробнее](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (сбросить пароли самообслуживания) теперь требует двух ворот для админв в Microsoft Azure, управляемых 21Vianet (Azure China 21Vianet) 

**Тип.** Измененная функция.  
**Категория обслуживания:** Спрозанизм паролем самообслуживания  
**Возможности продукта.** Безопасность и защита удостоверений.
 
Ранее в Microsoft Azure, управляемом 21Vianet (Azure China 21Vianet), админы с помощью сброса паролей самообслуживания (SSPR) для сброса собственных паролей требовали только одного "ворота" (вызов), чтобы доказать свою личность. В общественных и других национальных облаках, админы обычно должны использовать два ворота, чтобы доказать свою идентичность при использовании SSPR. Но поскольку мы не поддерживали SMS или телефонные звонки в Azure China 21Vianet, мы разрешили сбросить пароль с одними воротами от администов.

Мы создаем паритет функций SSPR между Azure China 21Vianet и общедоступным облаком. Заглядывая вперед, админы должны использовать два выхода при использовании SSPR. SMS, телефонные звонки и уведомления и коды приложений Authenticator будут поддерживаться. [Подробнее](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>Длина пароля ограничена 256 символами

**Тип.** Измененная функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Проверка подлинности пользователей
 
Для обеспечения надежности службы Azure AD пароли пользователей теперь ограничены в длине 256 символами. Пользователям с паролями, превышаюющими этот пароль, будет предложено изменить свой пароль на последующем логине, либо связавшись со своим админом, либо с помощью функции сбросить пароль самообслуживания.

Это изменение было включено 13 марта 2020 года, в 10 утра по времени (18:00 UTC), и ошибка AADSTS 50052, InvalidPasswordExceedsMaxLength. Подробнее [о неразбивещемся уведомлении об изменении.](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters)

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Входные журналы Azure AD теперь доступны для всех бесплатных арендаторов через портал Azure

**Тип.** Измененная функция.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.
 
Начиная с этого момента, клиенты, имеющие свободных арендаторов, могут получить доступ к [журналам входа в журналы Azure AD с портала Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) на срок до 7 дней. Ранее журналы входа были доступны только для клиентов с лицензиями Azure Active Directory Premium. С этим изменением все арендаторы могут получить доступ к этим журналам через портал.

> [!NOTE]
> Клиентам по-прежнему нужна лицензия премиум-класса (Azure Active Directory Premium P1 или P2) для доступа к журналам входа через Microsoft Graph API и Azure Monitor.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Изнурить опцию групп по каталогам из общих настроек групп на портале Azure

**Тип.** Прекращение поддержки.  
**Категория службы.** Управление группами.  
**Возможности продукта.** Совместная работа.

Чтобы обеспечить более гибкий способ создания групп по каталогам, которые наилучшим образом отвечают их потребностям, мы **заменили** опцию **«Группы** каталогов» из общих настроек **групп** > на портале Azure ссылкой на [динамическую групповую документацию.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership) Мы улучшили нашу документацию, чтобы включить больше инструкций, чтобы администраторы могли создавать группы пользователей, которые включают или исключают гостевых пользователей.

---

## <a name="february-2020"></a>Февраль 2020 г.

### <a name="upcoming-changes-to-custom-controls"></a>Предстоящие изменения в пользовательском управлении

**Тип.** Планирование изменений.  
**Категория обслуживания:** Мид  
**Возможности продукта.** Безопасность и защита удостоверений.
 
Мы планируем заменить текущий предварительный просмотр пользовательских элементов на подход, который позволяет предоставленным партнером возможностям аутентификации беспрепятственно работать с администратором Active Directory Azure и интерфейсами конечных пользователей. Сегодня решения МИД-партнера сталкиваются со следующими ограничениями: они работают только после ввода пароля; они не служат в качестве МИД для усиления аутентификации в других ключевых сценариях; и они не интегрируются с функциями управления конечными пользователями или административными учетными данными. Новая реализация позволит предоставленным партнерам факторами аутентификации работать вместе со встроенными факторами для ключевых сценариев, включая регистрацию, использование, претензии МИД, усиление аутентификации, отчетность и журналирование. 

Пользовательские элементы управления будут поддерживаться в предварительном просмотре вместе с новым дизайном до тех пор, пока он не достигнет общей доступности. В этот момент мы дадим клиентам время на переход на новый дизайн. Из-за ограничений текущего подхода, мы не будем на борту новых поставщиков, пока новый дизайн не будет доступен. Мы работаем в тесном контакте с клиентами и поставщиками и будем сообщать сроки, как мы становимся ближе. [Подробнее](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Identity Secure Score - Обновления действий по улучшению МИД

**Тип.** Планирование изменений.  
**Категория обслуживания:** Мид  
**Возможности продукта.** Безопасность и защита удостоверений.
 
Чтобы отразить необходимость для предприятий обеспечить максимальную безопасность при применении политик, которые работают с их бизнесом, Microsoft Secure Score удаляет три действия по улучшению, сосредоточенные вокруг многофакторной аутентификации (MFA), и добавляет два.

Следующие действия по улучшению будут удалены:

- Регистрация всех пользователей для МИД
- Обязательная многофакторная проверка подлинности для всех пользователей
- Требуйте МИД для привилегированных ролей Azure AD

Следующие действия по улучшению будут добавлены:

- Убедитесь, что все пользователи могут заполнить МИД для безопасного доступа
- Требование МИД для административных ролей

Эти новые действия по улучшению потребуют регистрации пользователей или админ-миде мид овечий каталог и установления правильного набора политик, которые соответствуют вашим организационным потребностям. Основная цель состоит в том, чтобы иметь гибкость, обеспечивая при этом все ваши пользователи и админы могут аутентифицировать себя с несколькими факторами или риск-ориентированных запросов проверки личности. Это может принимать форму настройки по умолчанию безопасности, которые позволяют корпорации Майкрософт решать, когда оспаривать пользователей для МИД, или иметь несколько политик, которые применяются объем решений. В рамках этих обновлений действий по улучшению политики защиты базовых линий больше не будут включены в расчеты скоринга. [Подробнее о том, что происходит в Microsoft Secure Score](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide).

---

### <a name="azure-ad-domain-services-sku-selection"></a>Выбор SKU служб ы домена AD Azure

**Тип.** Новая функция.  
**Категория службы.** Доменные службы Azure AD.  
**Возможности продукта.** Доменные службы Azure AD.
 
Мы слышали отзывы о том, что клиенты доменных служб Azure AD хотят большей гибкости при выборе уровней производительности для своих экземпляров. Начиная с 1 февраля 2020 года мы перешли от динамической модели (где Azure AD определяет производительность и уровень ценообразования на основе количества объектов) к модели самовыбора. Теперь клиенты могут выбрать уровень производительности, соответствующий их среде. Это изменение также позволяет нам включать новые сценарии, такие как Ресурсные леса, и функции Premium, такие как ежедневные резервные копирования. Количество объектов теперь не ограничено для всех SkUS, но мы будем продолжать предлагать предложения по подсчету объектов для каждого уровня.

**Немедленных действий клиента не требуется.** Для существующих клиентов динамический уровень, который использовался 1 февраля 2020 года, определяет новый уровень по умолчанию. В результате этого изменения не наблюдается влияния на ценообразование или производительность. В дальнейшем клиентам Azure AD DS необходимо будет оценить требования к производительности по мере изменения размера каталога и характеристик рабочей нагрузки. Переключение между уровнями обслуживания будет по-прежнему быть операцией простоя, и мы больше не будем автоматически перемещать клиентов на новые уровни в зависимости от роста их каталога. Кроме того, не будет никакого повышения цен, и новые цены будут соответствовать нашей текущей модели выставления счетов. Для получения дополнительной информации смотрите [документацию Azure AD DS SKUs](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus) и [страницу ценообразования на доменные услуги Azure AD.](https://azure.microsoft.com/pricing/details/active-directory-ds/)

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Новые федеративные приложения доступны в галерее Azure AD App - февраль 2020

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
В феврале 2020 года мы добавили в галерею приложений 31 новое приложение с поддержкой Федерации: 

[IamIP Патентная платформа](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial), [Опыт Облако](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial), [NS1 SSO для Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial), [Барракуда Email Security Service](https://ess.barracudanetworks.com/sso/azure), [ABa отчетности](https://myaba.co.uk/client-access/signin/auth/msad), [В случае кризиса - Интернет-портал](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial), [BIC Облачный дизайн](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial), [Пчеловод Azure AD Data Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial), [Korn Паром оценки](https://www.kornferry.com/solutions/kf-digital/kf-assess), [Веркада Команда](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial), [Splashtop](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial), [Syxsense](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial), [EAB Навигация](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial), [Новая реликвия (Ограниченный релиз)](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial), [Thulium](https://admin.thulium.com/login/instance), [менеджер по билетам](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial), Шаблон Выбор [для команд](https://links.officeatwork.com/templatechooser-download-teams), [Beesy](https://www.beesy.me/index.php/site/login), [система поддержки здоровья](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial), [MURAL](https://app.mural.co/signup), [Hive](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial), [LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview), [Wakelet](https://wakelet.com/login), [Firmex VDR](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial), [ThingLink для учителей и школ](https://www.thinglink.com/), [Кода](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial), [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WEDO](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial), [InvitePeople](https://invitepeople.com/login), [Перепечатки Стол - Статья Галактика](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial), [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Новые разъемы для подготовки в галерее приложений Azure AD - февраль 2020 г.

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
Теперь можно автоматизировать создание, обновление и удаляние учетных записей пользователей для этих новых интегрированных приложений:

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud от Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

Для получения дополнительной информации о том, как лучше обезопасить свою организацию с помощью автоматизированной подготовки учетной записи пользователя, [см.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Поддержка Azure AD для ключей безопасности FIDO2 в гибридных средах

**Тип.** Новая функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Проверка подлинности пользователей
 
Мы объявляем об общедоступном предварительном просмотре поддержки Azure AD для ключей безопасности FIDO2 в гибридных средах. Теперь пользователи могут использовать ключи безопасности FIDO2 для вхотвора в свои гибридные устройства Azure AD, объединенных устройствами Windows 10, и беспрепятственного ввоза на свои собственные и облачные ресурсы. Поддержка гибридных сред была самой востребованной функцией от наших клиентов без паролей, так как мы первоначально запустили публичный предварительный просмотр поддержки FIDO2 в устройствах, объединенных Azure AD. Безпачная аутентификация с использованием передовых технологий, таких как биометрия и публичная/частная криптография ключей, обеспечивает удобство и простоту использования при обеспечении безопасности. С помощью этого общедоступного предварительного просмотра теперь можно использовать современную аутентификацию, такую как ключи безопасности FIDO2, для доступа к традиционным ресурсам Active Directory. Для получения дополнительной информации перейдите в [SSO на ресурсы.](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises) 

Чтобы начать работу, посетите [включить ключи безопасности FIDO2 для вашего арендатора](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key) для пошаговых инструкций. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>Новый опыт "Мой счет" теперь в целом доступен

**Тип.** Измененная функция.  
**Категория обслуживания:** Мой профиль/аккаунт  
**Возможности продукта:** Опыт конечных пользователей
 
Моя учетная запись, одна остановка магазин для всех конечных пользователей потребностей управления счетом, в настоящее время, как правило, доступны! Конечные пользователи могут получить доступ к этому новому сайту через URL или в заголовке нового опыта My Apps. Узнайте больше обо всех возможностях самообслуживания, которые предлагает новый опыт, на [портале My Account Overview](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>URL-адрес сайта "Моя учетная запись" обновляется до myaccount.microsoft.com

**Тип.** Измененная функция.  
**Категория обслуживания:** Мой профиль/аккаунт  
**Возможности продукта:** Опыт конечных пользователей
 
Новый пользовательский интерфейс My Account будет `https://myaccount.microsoft.com` обновлять свой URL-адрес в следующем месяце. Узнайте больше об опыте и всех возможностях самообслуживания учетной записи, которые она предлагает конечным пользователям на [портале My Account.](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)

---
 
## <a name="january-2020"></a>Январь 2020 г.
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>Новый портал My Apps теперь широко доступен

**Тип.** Планирование изменений.  
**Категория службы.** "Мои приложения".  
**Возможности продукта:** Опыт конечных пользователей
 
Обновите свою организацию до нового портала My Apps, который теперь широко доступен! Более подробную информацию о новом портале и коллекциях можно найти [на портале My Apps.](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Рабочие области в Azure AD были переименованы в коллекции

**Тип.** Измененная функция.  
**Категория службы.** "Мои приложения".   
**Возможности продукта:** Опыт конечных пользователей
 
Рабочие пространства, фильтры, которые админы могут настроить для организации приложений своих пользователей, теперь будут называться коллекциями. Узнайте больше о том, как настроить их в [коллекциях Create на портале My Apps.](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Регистрация и регистрация на телефоне Azure AD B2C с использованием пользовательской политики (Public Preview)

**Тип.** Новая функция.  
**Категория службы.** B2C — управление удостоверениями клиентов.  
**Возможности продукта.** B2B и B2C.
 
С регистрацией номера телефона и входе в систему, разработчики и предприятия могут позволить своим клиентам зарегистрироваться и войти в систему с помощью одноразового пароля, отправленного на номер телефона пользователя через SMS. Эта функция также позволяет клиенту изменить свой номер телефона, если он потеряет доступ к своему телефону. Благодаря пользовательским политикам регистрация по телефону и регистрация позволяют разработчикам и предприятиям общаться со своим брендом через настройки страниц. Узнайте, как [настроить регистрацию по телефону и войти в систему с пользовательскими политиками в Azure AD B2C.](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication)
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Новые разъемы для подготовки в галерее приложений Azure AD - январь 2020 года

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
Теперь можно автоматизировать создание, обновление и удаляние учетных записей пользователей для этих новых интегрированных приложений:

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

Для получения дополнительной информации о том, как лучше обезопасить свою организацию с помощью автоматизированной подготовки учетной записи пользователя, [см.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Новые федеративные приложения доступны в галерее Azure AD App - январь 2020

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
В январе 2020 года мы добавили эти 33 новых приложения с поддержкой Федерации в галерею приложений: 

[JOSA](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial), [Fastly Edge Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial), [Terraform Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial), [Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial), [Abibot Netlogistik,](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik) [SkyKick](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), [Upshotly](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial), [LeaveBot,](https://leavebot.io/#home) [DataCamp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial), [TripActions](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial), [SmartWork](https://www.intumit.com/english/SmartWork.html), [Dotcom-Monitor](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial), [SSOGEN - Azure AD SSO Gateway для Oracle E-Business Suite - EBS, PeopleSoft, и JDE](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial), [Хостинг MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial), [Yuhu Управления недвижимостью платформа](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial), [LumApps](https://sites.lumapps.com/login), [Upwork предприятие](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial), [Talentsoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial), [SmartDB для Microsoft Teams](http://teams.smartdb.jp/login/), [PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial), [ContractSafe Saml2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial), [Maxient Поведение менеджер программного обеспечения](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial), [Helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial), [Портал Поговорите 365](https://www.portaltalk.com/), [CoreView](https://portal.coreview.com/), [Squelch Облако Office365 Разъем](https://laxmi.squelch.io/login), [PingFlow аутентификация](https://app-staging.pingview.io/), [PrinterLogic SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial), [Taskize Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial), [Sandwai](https://app.sandwai.com/), [E'RentOut](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial), [AssetSonar](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial), [Акари Виртуальный помощник](https://akari.io/akari-virtual-assistant/)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="two-new-identity-protection-detections"></a>Два новых обнаружения защиты идентификационных данных

**Тип.** Новая функция.  
**Категория обслуживания:** Защита личных данных  
**Возможности продукта.** Безопасность и защита удостоверений.
 
Мы добавили два новых типа обнаружения, связанных со ссылкой на систему регистрации, в систему Identity Protection: подозрительные правила манипуляции в папке "Входящие" и "Невозможное путешествие". Эти автономные обнаружения обнаружены Microsoft Cloud App Security (MCAS) и влияют на пользователя и риск вхводом в Identity Protection. Для получения дополнительной информации об [sign-in risk types](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk)этих обнаружений см.
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Нарушение изменения: URI Фрагменты не будут осуществляться через перенаправление входа

**Тип.** Измененная функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Проверка подлинности пользователей
 
Начиная с 8 февраля 2020 года, когда запрос отправляется login.microsoftonline.com для входиного пользователя, служба приговыт пустой фрагмент к запросу.  Это предотвращает перенаправление группы атак, гарантируя, что браузер уничтожит любой существующий фрагмент запроса. Ни одно приложение не должно зависеть от такого поведения. Для получения дополнительной информации [см. Нарушение изменений](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020) в документации платформы идентификации Майкрософт.

---

## <a name="december-2019"></a>Декабрь 2019 г.

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>Интеграция SAP SuccessFactors, предовещающая в Azure AD и на территории AD (Общественный предварительный просмотр)

**Тип.** Новая функция.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта:** Управление жизненным циклом идентичности

Теперь вы можете интегрировать SAP SuccessFactors в качестве авторитетного источника идентификации в Azure AD. Эта интеграция позволяет автоматизировать сквозной жизненный цикл идентификации, в том числе с помощью событий на основе HR, таких как новые сотрудники или увольнения, для контроля за подготовкой учетных записей Azure AD.

Для получения дополнительной информации о том, как настроить SAP SuccessFactors входящих подготовки к Azure AD, [см.](https://aka.ms/SAPSuccessFactorsInboundTutorial)

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Поддержка индивидуальных сообщений электронной почты в Azure AD B2C (публичный предварительный просмотр)

**Тип.** Новая функция.  
**Категория службы.** B2C — управление удостоверениями клиентов.  
**Возможности продукта.** B2B и B2C.

Теперь вы можете использовать Azure AD B2C для создания настраиваемых электронных писем, когда пользователи регистрируются, чтобы использовать ваши приложения. Используя DisplayControls (в настоящее время в предварительном просмотре) и стороннего поставщика электронной почты (например, [SendGrid,](https://sendgrid.com/) [SparkPost](https://sparkpost.com/)или пользовательский REST API), вы можете использовать свой собственный шаблон электронной почты, **от** адреса и текста темы, а также поддержку локализации и пользовательских одноразовых параметров паролей (OTP).

Для получения дополнительной информации смотрите [проверку пользовательской электронной почты в Azure Active Directory B2C.](https://docs.microsoft.com/azure/active-directory-b2c/custom-email)

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Замена базовых политик по умолчанию

**Тип.** Измененная функция.  
**Категория службы.** Другая.  
**Возможности продукта:** Безопасность и защита личных данных

В рамках модели проверки подлинности по умолчанию мы удаляем существующие базовые политики защиты от всех клиентов. Это удаление планируется завершить в конце февраля. Заменой этих базовых политик защиты являются по умолчанию безопасности. Если вы использовали базовые политики защиты, необходимо запланировать переход к новой политике по умолчанию безопасности или к условному доступу. Если вы не использовали эти политики, нет никаких действий для вас принять.

Для получения дополнительной информации о новых по умолчанию безопасности [см.](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) Для получения дополнительной информации о [Common Conditional Access policies](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)политиках условного доступа см.

---

## <a name="november-2019"></a>Ноябрь 2019 г.

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>Поддержка атрибута SameSite и Chrome 80

**Тип.** Планирование изменений.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Проверка подлинности пользователей

В рамках безопасной по умолчанию модели для файлов cookie браузер Chrome 80 изменяет `SameSite` способ обработки файлов cookie без атрибута. Любое файлcookieое `SameSite` файлы, в которых не `SameSite=Lax`указан атрибут, будет рассматриваться так, как если бы оно было установлено, что приведет к блокировке Chrome определенных сценариев совместного использования кросс-доменов, от которых может зависеть ваше приложение. Для поддержания старого поведения Chrome `SameSite=None` можно использовать `Secure` атрибут и добавить дополнительный атрибут, поэтому к файлам кросс-сайта можно получить доступ только по соединениям HTTPS. Chrome планирует завершить это изменение к 4 февраля 2020 года.

Мы рекомендуем всем нашим разработчикам протестировать свои приложения, используя это руководство:

- Установите значение по умолчанию для настройки **«Безопасные файлы использования»** на **«Да».**

- Установите значение по умолчанию для атрибута **SameSite** на **None.**

- Добавьте `SameSite` дополнительный атрибут **Secure.**

Для получения дополнительной информации смотрите [Предстоящие изменения Cookie SameSite в ASP.NET и ASP.NET Core](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) и [потенциальные нарушения веб-сайтов клиентов и продуктов и услуг Майкрософт в версии Chrome 79 и позже.](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79)

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Новый hotfix для Microsoft Identity Manager (MIM) 2016 Сервисный пакет 2 (SP2)

**Тип.** Исправление.  
**Категория обслуживания:** Менеджер идентификации Майкрософт  
**Возможности продукта:** Управление жизненным циклом идентичности

Пакет свертывания hotfix (построить 4.6.34.0) доступен для Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Этот пакет свертывания устраняет проблемы и добавляет улучшения, описанные в разделе "Вопросы фиксированной и улучшения, добавленные в этом обновлении".

Для получения дополнительной информации и загрузки пакета hotfix [см.](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r)

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Новый отчет о деятельности приложения AD FS, помогая перенести приложения в Azure AD (публичный предварительный просмотр)

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Единый вход.

Используйте новый отчет о деятельности приложений Active Directory Federation (AD FS) на портале Azure, чтобы определить, какие из ваших приложений могут быть перенесены в Azure AD. В отчете оценивается все приложения AD FS по совместимости с Azure AD, проверяется на наличие любых проблем и дается рекомендации по подготовке отдельных приложений к миграции.

Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity)

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Новый рабочий процесс для пользователей, запрашивающих согласие администратора (Public Preview)

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможность продукта.** Контроль доступа.

Новый рабочий процесс согласия админа дает таким образом, чтобы поставщики предоставили доступ к приложениям, требующим одобрения админа. Если пользователь пытается получить доступ к приложению, но не может дать согласие, теперь он может отправить запрос на утверждение админ. Запрос отправляется по электронной почте и помещается в очередь, доступную с портала Azure, всем адм-имам, которые были назначены рецензентами. После того, как рецензент принимает меры по ожидающего запроса, запрашивающие пользователи уведомляются об действии.

Для получения дополнительной информации [см. Накструированный рабочий процесс согласия админа (предварительный просмотр).](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow)

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Новый опыт настройки токенов AD Azure AD для управления дополнительными претензиями (Public Preview)

**Тип.** Новая функция.  
**Категория службы.** Другая.  
**Возможности продукта.** Возможности для разработчиков

Новое лезвие конфигурации маркеров приложений **Azure AD Регистрации** на портале Azure теперь показывает разработчикам приложений динамический список дополнительных требований для своих приложений. Этот новый опыт помогает упорядочить миграцию приложений Azure AD и свести к минимуму неверную конфигурацию требований.

Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Новый двухэтапный рабочий процесс утверждения в управлении правами Azure AD (общественный предварительный просмотр)

**Тип.** Новая функция.  
**Категория службы.** Другая.  
**Возможности продукта:** Управление правами

Мы ввели новый двухэтапный рабочий процесс утверждения, который позволяет требовать от двух одобрителей утвердить запрос пользователя на пакет доступа. Например, можно установить его таким образом, чтобы менеджер запрашивающего пользователя сначала должен утвердить, а затем вы также можете потребовать, чтобы владелец ресурса одобрил. Если один из одобрителей не одобряет, доступ не предоставляется.

Для получения дополнительной информации смотрите [настройки запроса и утверждения изменений для пакета доступа в управлении правами Azure AD.](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy)

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Обновления на странице My Apps вместе с новыми рабочими пространствами (Публичный предварительный просмотр)

**Тип.** Новая функция.  
**Категория службы.** "Мои приложения".  
**Возможности продукта.** Интеграция с решениями сторонних производителей.

Теперь вы можете настроить способ просмотра пользователями вашей организации и доступа к обновленной возможности My Apps. Этот новый опыт также включает в себя новую функцию рабочих областей, которая упрощает поиск и организацию приложений пользователями.

Для получения дополнительной информации о новом опыте My Apps и создании рабочих областей [см.](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Поддержка google в социальной поддержке для совместной работы Azure AD B2B (общая доступность)

**Тип.** Новая функция.  
**Категория службы.** B2B.  
**Возможности продукта:** Проверка подлинности пользователей

Новая поддержка использования социальных идентипов Google (учетных записей Gmail) в Azure AD помогает упростить совместную работу для пользователей и партнеров. Партнерам больше не нужно создавать и управлять новой учетной записью майкрософт. В настоящее время Microsoft Teams полностью поддерживает пользователей Google на всех клиентах и в общих конечных точках проверки подлинности, связанных с арендаторами.

Для получения дополнительной информации просмотрите [Добавление Google в качестве поставщика идентификационных данных для гостевых пользователей B2B.](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Мобильная поддержка Microsoft Edge для условного доступа и единого ввоза (общая доступность)

**Тип.** Новая функция.  
**Категория обслуживания:** Условный доступ  
**Возможности продукта.** Безопасность и защита удостоверений.

Azure AD для Microsoft Edge на iOS и Android теперь поддерживает Azure AD Single Sign-On и условный доступ:

- **Microsoft Edge одиночный вписаться (SSO):** Единый входной связь теперь доступен для наиболее отечественных клиентов (таких как Microsoft Outlook и Microsoft Edge) для всех приложений, подключенных к Azure AD.

- **Условная доступность Microsoft Edge:** С помощью политик условного доступа на основе приложений пользователи должны использовать защищенные от Microsoft браузеры, такие как Microsoft Edge.

Для получения дополнительной информации об условном доступе и SSO с Microsoft Edge, см [Microsoft Edge Мобильная поддержка для условного доступа и единого вступления в настоящее время в целом доступны](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) блога. Для получения дополнительной информации о том, как настроить клиентские приложения с помощью [условного доступа на основе приложений](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) или [условного доступа на основе устройств,](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)см. [Управление веб-доступом с помощью защищенного от политики браузера Microsoft Intune.](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser)

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Управление правами Azure AD (общая доступность)

**Тип.** Новая функция.  
**Категория службы.** Другая.  
**Возможности продукта:** Управление правами

Управление правами Azure AD — это новая функция управления идентификацией, которая помогает организациям управлять жизненным циклом идентификации и доступа в масштабе. Эта новая функция помогает автоматизировать рабочие процессы запроса доступа, назначения доступа, обзоры и истечение срока действия в группах, приложениях и сайтах SharePoint Online.

С помощью управления правами Azure AD можно более эффективно управлять доступом как для сотрудников, так и для пользователей за пределами организации, которым необходим доступ к этим ресурсам.

Для получения дополнительной [What is Azure AD entitlement management?](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements) информации см.

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Автоматизировать подготовку учетной записи пользователя для этих недавно поддерживаемых приложений SaaS

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.  

Теперь можно автоматизировать создание, обновление и удаляние учетных записей пользователей для этих новых интегрированных приложений:

[Служба аутентификации идентификационных данных облачной платформы SAP](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial), [SpaceI ,](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial) [Миро](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial), [Cloudgate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial), [Программное обеспечение OfficeSpace](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial), [Приоритетная матрица](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

Для получения дополнительной информации о том, как лучше обезопасить свою организацию с помощью автоматизированной подготовки учетной записи пользователя, [см.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Новые федеративные приложения доступны в галерее Azure AD App - ноябрь 2019

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.

В ноябре 2019 года мы добавили в галерею приложений эти 21 новое приложение с поддержкой Федерации:

[Airtable](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial), [Hootsuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial), [Синий доступ для членов (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial), [Bitly](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial) [e4enable](https://portal.e4enable.com/) , [Рива](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [ResLife портал](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal Единый знак на (SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial) [Jisc Student Voter Registration](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial), [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), [Motus](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial) [Netskope User Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial), [MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial), [uniFLOW Online](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial) [BlueMail](https://loginself1.bluemail.me/) [Claromentis](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial), [Beedle](https://teams-web.beedle.co/#/), [Visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial), [OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial), [Foko Retail](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial) [&](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial),

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Новая и улучшенная галерея приложений Azure AD

**Тип.** Измененная функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Единый вход.

Мы обновили галерею приложений Azure AD, чтобы облегчить вам поиск предварительно интегрированных приложений, поддерживающих подготовку, OpenID Connect и SAML на вашем арендаторе Active Directory Azure.

Для получения дополнительной информации можно [добавить приложение в свой арендатор Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal)

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Увеличено ограничение длины определения роли приложения со 120 до 240 символов

**Тип.** Измененная функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Единый вход.

Мы слышали от клиентов, что ограничение длины для определения роли приложения в некоторых приложениях и службах слишком короткий на 120 символов. В ответ мы увеличили максимальную длину определения значения роли до 240 символов.

Для получения дополнительной информации об использовании определений ролей конкретных приложений [см.](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)

---

## <a name="october-2019"></a>Октябрь 2019 г.

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Уничтожение API-извента в идентификационный риск для обнаружения рисков защиты идентификационных данных Azure AD  

**Тип.** Планирование изменений.  
**Категория обслуживания:** Защита личных данных  
**Возможности продукта.** Безопасность и защита удостоверений.

В ответ на отзывы разработчиков абоненты Azure AD Premium P2 теперь могут выполнять сложные запросы на данных обнаружения рисков Azure AD Identity Protection, используя новый API-извне для Microsoft Graph. Существующая бета-версия [identityRiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) API перестанет возвращать данные около **10 января 2020 года.** Если ваша организация использует API identityRiskEvent, следует перейти на новый API riskDetection.

Для получения дополнительной информации о новом [Risk detection API reference documentation](https://aka.ms/RiskDetectionsAPI)API обнаружения рисков см.

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Поддержка прокси-серверов приложений для атрибута SameSite и Chrome 80

**Тип.** Планирование изменений.  
**Категория службы.** Прокси-сервер приложения.  
**Возможность продукта.** Контроль доступа.

За пару недель до выхода браузера Chrome 80 мы планируем обновить способы обработки файлов **Cookie-файлов Сapplication** Proxy атрибутом SameSite. С выпуском Chrome 80, любое печенье, которое не указывает атрибут **SameSite,** будет рассматриваться так, как будто оно было настроено на. `SameSite=Lax`

Чтобы избежать потенциально негативных последствий из-за этого изменения, мы обновляем доступ к прокси-серверу приложения и файлы cookie:

- Настройка значения по умолчанию для настройки **«Безопасные файлы использования»** для **Yes.**

- Установка значения по умолчанию для атрибута **SameSite** **нет.**

    >[!NOTE]
    > Файлы доступа к приложениям всегда передавались исключительно по защищенным каналам. Эти изменения применяются только к файлам cookie сеансов.

Для получения дополнительной информации о настройках файлов cookie application Application [см.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>Регистрации приложений (устаревшие) и конвергентное управление приложениями с портала регистрации приложений (apps.dev.microsoft.com) больше не будут доступны

**Тип.** Планирование изменений.  
**Категория службы:** недоступно  
**Возможности продукта.** Возможности для разработчиков

В ближайшем будущем пользователи с учетными записями Azure AD больше не смогут регистрировать и управлять конвергентными приложениями с помощью портала регистрации приложений (apps.dev.microsoft.com), а также регистрировать и управлять приложениями в опыте регистрации приложений (наследие) на портале Azure.

Чтобы узнать больше о новом опыте регистрации приложений, смотрите [регистрации приложений в руководстве по обучению порталу Azure](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md).

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Пользователи больше не обязаны перерегистрироваться во время миграции из МИД на одного пользователя в МИД условного доступа

**Тип.** Исправление.  
**Категория обслуживания:** Мид  
**Возможности продукта.** Безопасность и защита удостоверений.

Мы исправили известную проблему, по которой пользователи должны были перерегистрироваться, если они были отключены для мультифакторной аутентификации (MFA), а затем включены для МИД через политику условного доступа.

Чтобы потребовать от пользователей перерегистрации, можно выбрать опцию **Обязательная перерегистрация MFA** из методов проверки подлинности пользователя на портале Azure AD. Для получения дополнительной информации о миграции пользователей из МИД на [Convert users from per-user MFA to Conditional Access based MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa)одного пользователя в МИД на основе условного доступа см.

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Новые возможности для преобразования и отправки заявок в токене SAML

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Единый вход.

Мы добавили дополнительные возможности, которые помогут вам настроить и отправить претензии в маркере SAML. Эти новые возможности включают в себя:

- Дополнительные функции преобразования требований, помогающие изменить значение, присылаевеете в претензии.

- Возможность применения нескольких преобразований к одной претензии.

- Возможность указать источник претензии на основе типа пользователя и группы, к которой принадлежит пользователь.

Подробную информацию об этих новых возможностях, в ключая ими, можно найти в [токене SAML для корпоративных приложений.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Новая страница встыков для конечных пользователей в Azure AD

**Тип.** Новая функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Мониторинг и отчетность.

Мы добавили новую страницу **"Мой вводом"** (чтобыhttps://mysignins.microsoft.com) пользователи вашей организации могли просматривать свою недавнюю историю входного регистра, чтобы проверить наличие каких-либо необычных действий. Эта новая страница позволяет пользователям видеть:

- Если кто-то пытается угадать свой пароль.

- Если злоумышленник успешно зарегистрировался в своей учетной записи и с какого места.

- К каким приложениям злоумышленник пытался получить доступ.

Для получения дополнительной информации, [см. Пользователи теперь могут проверить их входе в историю необычных деятельности](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) блоге.

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Миграция служб домена Azure AD (Azure AD DS) из классических в виртуальные сети управления ресурсами Azure

**Тип.** Новая функция.  
**Категория службы.** Доменные службы Azure AD.  
**Возможности продукта.** Доменные службы Azure AD.

Для наших клиентов, которые застряли на классических виртуальных сетей - у нас есть хорошая новость для вас! Теперь можно выполнять одноразовую миграцию из классической виртуальной сети в существующую виртуальную сеть Resource Manager. После перехода в виртуальную сеть Resource Manager вы сможете воспользоваться дополнительными и обновленными функциями, такими как, мелкозернистые политики паролей, уведомления по электронной почте и журналы аудита.

Для получения дополнительной информации [см. Предварительный просмотр - Переносите службы домена Azure AD из классической виртуальной сетевой модели в ресурсный менеджер.](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet)

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Обновления макета контракта на страницу Azure AD B2C

**Тип.** Новая функция.  
**Категория службы.** B2C — управление удостоверениями клиентов.  
**Возможности продукта.** B2B и B2C.

Мы внесли некоторые новые изменения в версию 1.2.0 контракта на страницу Azure AD B2C. В этой обновленной версии теперь можно управлять заказом нагрузки для элементов, что также может помочь остановить мерцание, которое происходит при загрузке листа стиля (CSS).

Полный список изменений, внесенных в [Version change log](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120)контракт на странице, см.

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Обновление страницы My Apps вместе с новыми рабочими областями (публичный предварительный просмотр)

**Тип.** Новая функция.  
**Категория службы.** "Мои приложения".  
**Возможность продукта.** Контроль доступа.

Теперь вы можете настроить способ просмотра пользователями организации и доступа к совершенно новому опыту My Apps, втомяв новую функцию рабочих пространств, чтобы облегчить им поиск приложений. Функциональность новых рабочих областей действует как фильтр для приложений, к которые пользователи организации уже имеют доступ.

Для получения дополнительной информации о развертывании нового опыта My Apps и создании рабочих областей [см. Создание рабочих пространств на портале My Apps (предварительный просмотр).](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Поддержка ежемесячной активной модели выставления счетов на основе пользователей (общая доступность)

**Тип.** Новая функция.  
**Категория службы.** B2C — управление удостоверениями клиентов.  
**Возможности продукта.** B2B и B2C.

Azure AD B2C теперь поддерживает ежемесячные активные пользователи (MAU) биллинга. Биллинг MAU основан на количестве уникальных пользователей с активностью аутентификации в течение календарного месяца. Существующие клиенты могут переключиться на этот новый метод выставления счетов в любое время.

С 1 ноября 2019 года всем новым клиентам автоматически выставят счет с помощью этого метода. Этот метод выставления счетов приносит пользу клиентам за счет затрат и возможности планировать заранее.

Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Новые федеративные приложения доступны в галерее Azure AD App - октябрь 2019

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.

В октябре 2019 года мы добавили эти 35 новых приложений с поддержкой Федерации в галерею приложений:

[В случае кризиса - Мобильный](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial), [Juno Путешествие](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial), [ExponentHR](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial), [Такт](https://tact.ai/assistant/), [OpusCapita Управление наличностью](http://cm1.opuscapita.com/tenantname), [Salestim](https://prd.salestim.io/forms), [Learnster](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial), [Dynatrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial), [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process), [Freshworks](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial), [eCornell](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial), [ShipHazmat](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial), [Netskope Cloud Security](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial), [Contentful](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial), [Bindtuning](https://bindtuning.com/login), [HireVue Координация - Европа](https://www.hirevue.com/), [HireVue Координация - USOnly](https://www.hirevue.com/), [HireVue Координация - США](https://www.hirevue.com/), [WittyParrot Знания Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial), [Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial), [Cambium Xirrus EasyPass портал](https://login.xirrus.com/azure-signup), [Paylocity](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial), [Почта Удача!](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial), [Teamie](https://theteamie.com/), [Скорость для команд](https://velocity.peakup.org/teams/login), [SIGNL4](https://account.signl4.com/manage), [EAB Навигация IMPL](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial), [ScreenMeet](https://console.screenmeet.com/), [Omega Point](https://pi.ompnt.com/), Говоря [электронная почта для Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [Говоря электронная почта для Office 365 Прямая (iPhone / Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ExactCare SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial), [iHealthHome Care Навигационная система](https://ihealthnav.com/account/signin), [Зуби](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Сводный элемент меню безопасности на портале Azure AD

**Тип.** Измененная функция.  
**Категория обслуживания:** Защита личных данных  
**Возможности продукта.** Безопасность и защита удостоверений.

Теперь вы можете получить доступ ко всем доступным функциям безопасности Azure AD из нового элемента меню **безопасности** и из панели **поиска** на портале Azure. Кроме того, новая целевая страница **безопасности,** называемая **Security - Getting started,** будет предоставлять ссылки на нашу публичную документацию, руководство по безопасности и руководства по развертыванию.

Новое меню **безопасности** включает в себя:

- Условный доступ
- Защита идентификации
- Центр безопасности
- Оценка безопасности удостоверений
- Методы проверки подлинности
- MFA
- Отчеты о рисках - Рискованные пользователи, рискованные вводки, обнаружение рисков
- И многое другое!

Для получения дополнительной [информации см.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted)

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Политика истечения срока действия Office 365 групп усилена с помощью автообновления

**Тип.** Измененная функция.  
**Категория службы.** Управление группами.  
**Возможности продукта:** Управление жизненным циклом идентичности

Политика истечения срока действия Групп Управления 365 была усовершенствована, с тем чтобы автоматически обновлять группы, которые активно используются его членами. Группы будут автоматически обновляться на основе активности пользователей во всех приложениях Office 365, включая Outlook, SharePoint и Teams.

Это усовершенствование помогает уменьшить количество уведомлений об истечении срока действия группы и гарантирует, что активные группы по-прежнему доступны. Если у вас уже есть активная политика истечения срока действия для групп Office 365, вам не нужно ничего делать, чтобы включить эту новую функциональность.

Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle)

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Обновленный опыт создания доменов Azure AD (Azure Ad DS)

**Тип.** Измененная функция.  
**Категория службы.** Доменные службы Azure AD.  
**Возможности продукта.** Доменные службы Azure AD.

Мы обновили службы домена Azure AD (Azure AD DS), чтобы включить новый и улучшенный опыт создания, помогая вам создать управляемый домен всего за три клика! Кроме того, теперь можно загружать и развертывать Azure AD DS из шаблона.

Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)

---
