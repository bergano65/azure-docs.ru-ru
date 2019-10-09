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
ms.openlocfilehash: 616a637df20e43c20faadaf3db9243c28f94de34
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174729"
---
# <a name="whats-new-in-azure-active-directory"></a>Новые возможности Azure Active Directory

>Получите уведомления о том, когда следует повторно посетить эту страницу для получения обновлений, скопировав и вставляя этот URL-адрес: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` в значок модуля чтения ![RSS на канале чтения no__t-2.

Azure AD усовершенствуется на постоянной основе. Чтобы вы оставались в курсе последних разработок, в этой статье предоставлены такие сведения:

- Последние выпуски.
- Известные проблемы
- Исправления ошибок
- Нерекомендуемые функции.
- Планы по изменениям.

Эта страница обновляется ежемесячно, поэтому регулярно пересматривайте ее. Если вам понадобятся материалы, которым более шести месяцев, их можно найти в статье [Архив сведений о новых возможностях в Azure Active Directory](whats-new-archive.md).

---

## <a name="september-2019"></a>Сентябрь 2019 г.

### <a name="my-profile-is-re-naming-and-integrating-with-the-microsoft-office-account-page"></a>Мой профиль переименовывается и интегрируется со страницей учетной записи Microsoft Office

**Тип:** план изменений  
**Категория службы:** Мой профиль или учетная запись  
**Возможности продукта:** Совместная работа

Начиная с октября, мой профиль пользователя станет моей учетной записью. В рамках этого изменения везде, где в настоящий момент написано, **Мой профиль** изменится на **Моя учетная запись**. В начале изменения именования и некоторых улучшений в разработке обновленный интерфейс будет предоставлять дополнительную интеграцию со страницей учетной записи Microsoft Office. В частности, вы сможете получить доступ к установкам и подпискам Office со страницы **обзорной учетной записи** , а также с контактными данными, связанными с Office, на странице **Конфиденциальность** .

Дополнительные сведения о работе с моим профилем (Предварительная версия) см. в [обзоре портала "Мой профиль (Предварительная версия)](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview)".

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Управление группами и членами с помощью CSV-файлов на портале Azure AD (общедоступная Предварительная версия)

**Тип:** Новая функция  
**Категория службы.** Управление группами  
**Возможности продукта:** Совместная работа

Мы рады сообщить о доступности общедоступной предварительной версии функций управления массовыми группами на портале Azure AD. Теперь вы можете использовать CSV-файл и портал Azure AD для управления группами и списками членов, в том числе:

- Добавление или удаление членов из группы.

- Скачивание списка групп из каталога.

- Скачивание списка членов группы для определенной группы.

Дополнительные сведения см. в разделе [Пакетное добавление членов](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members), [Групповое удаление членов](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members), [список загрузки участников](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)и [список групп загрузки](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>Динамическое согласие теперь поддерживается через новую конечную точку согласия администратора.

**Тип:** Новая функция  
**Категория службы.** аутентификация (вход)  
**Возможности продукта:** Аутентификация пользователей

Мы создали новую конечную точку согласия администратора для поддержки динамического согласия, что полезно для приложений, которые хотят использовать модель динамического согласия на платформе Microsoft Identity.

Дополнительные сведения о том, как использовать эту новую конечную точку, см. [в разделе Использование конечной точки согласия администратора](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent).

---

### <a name="new-azure-ad-global-reader-role"></a>Новая роль глобального читателя Azure AD

**Тип:** Новая функция  
**Категория службы:** RBAC  
**Возможности продукта:** Контроль доступа

Начиная с 24 сентября 2019 г. начинается развертывание новой роли Azure Active Directory (AD) с именем Global Reader. Это развертывание будет начинаться с корпоративных и глобальных облачных клиентов (GCC), в октябре.

Роль глобального читателя — это аналог глобального администратора, предназначенный только для чтения. Пользователи с этой ролью могут считывать параметры и административные данные в службах Microsoft 365 Services, но не могут выполнять действия по управлению. Мы создали глобальную роль читателя, которая поможет сократить число глобальных администраторов в Организации. Поскольку учетные записи глобального администратора являются мощными и уязвимыми для атак, рекомендуется иметь менее пяти глобальных администраторов. Мы рекомендуем использовать глобальную роль читателя для планирования, аудита или исследования. Мы также советуем использовать глобальную роль читателя в сочетании с другими ограниченными ролями администратора, например с администратором Exchange, чтобы обеспечить работу без использования роли глобального администратора.

Роль глобального читателя работает с новым центром администрирования Microsoft 365, центром администрирования Exchange, центром администрирования группы, центром безопасности, центром соответствия, центром администрирования Azure AD и центром администрирования управления устройствами.

>[!NOTE]
> В начале общедоступной предварительной версии роль глобального читателя не будет работать с: SharePoint, Privileged Access Management, защищенное хранилище, метки чувствительности, жизненный цикл групп, группы отчетов & анализа вызовов, Управление устройствами с IP-телефонами и каталог приложений группы. Все эти службы предназначены для работы с ролью в будущем.

Дополнительные сведения см. [в разделе разрешения роли администратора в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Доступ к локальному серверу отчетов из приложения Power BI Mobile с помощью Azure Active Directory Application Proxy

**Тип:** Новая функция  
**Категория службы:** Прокси приложения  
**Возможности продукта:** Контроль доступа

Новая интеграция между мобильным приложением Power BI и Azure AD Application Proxy позволяет безопасно входить в Power BI мобильное приложение и просматривать отчеты Организации, размещенные на локальном Сервер отчетов Power BI.

Сведения о Power BI Mobile приложении, включая расположение для скачивания приложения, см. на [сайте Power BI](https://powerbi.microsoft.com/mobile/). Дополнительные сведения о настройке Power BI мобильного приложения с помощью AD Application Proxy Azure см. в статье [Включение удаленного доступа для Power BI Mobile с помощью azure AD application proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>Доступна новая версия модуля PowerShell AzureADPreview

**Тип:** Измененная функция  
**Категория службы:** Другие  
**Возможности продукта:** Каталог

В модуль AzureADPreview были добавлены новые командлеты, которые помогут определить и назначить пользовательские роли в Azure AD, в том числе:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Новая версия Azure AD Connect

**Тип:** Измененная функция  
**Категория службы:** Другие  
**Возможности продукта:** Каталог

Выпущена обновленная версия Azure AD Connect для автоматического обновления клиентов. Эта новая версия включает несколько новых функций, улучшений и исправлений ошибок. Дополнительные сведения об этой новой версии см. в разделе [Azure AD Connect: История выпусков версий](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250).

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Теперь доступен сервер многофакторной идентификации Azure (MFA) версии версии 8.0.2.

**Тип:** исправление  
**Категория службы:** MFA  
**Возможности продукта:** безопасность и защита идентификации

Если вы являетесь существующим клиентом, который активировал сервер MFA до 1 июля 2019, теперь можно скачать последнюю версию сервера MFA (версия версии 8.0.2). В этой новой версии мы:

- Исправлена проблема, поэтому когда Azure AD Sync изменяет пользователя с отключенным на Enabled, пользователю отправляется сообщение электронной почты.

- Исправлена проблема, поэтому клиенты могут успешно обновиться, продолжая использовать функции тегов.

- Добавлен код страны Косово (+ 383).

- В MultiFactorAuthSvc. log добавлен однократный обход журнала аудита.

- Улучшена производительность для пакета SDK веб-службы.

- Исправлены другие незначительные ошибки.

С 1 июля 2019 г. Корпорация Майкрософт прекратила предлагать сервер MFA для новых развертываний. Новые клиенты, которым требуется многофакторная проверка подлинности, должны использовать службу многофакторной идентификации Azure на основе облака. Дополнительные сведения см. [в статье Планирование развертывания многофакторной идентификации Azure на основе облачных служб](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="august-2019"></a>Август 2019 г.

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Планирование изменений: Устаревшие пакеты содержимого Power BI

**Тип:** план изменений  
**Категория службы:** Отчеты  
**Возможности продукта:** мониторинг и создание отчетов

Начиная с 1 октября 2019 г. Power BI начнет использовать все пакеты содержимого, включая пакет содержимого Azure AD Power BI. В качестве альтернативы этому пакету содержимого можно использовать книги Azure AD для получения ценных сведений о службах, связанных с Azure AD. Появятся дополнительные книги, включая книги о политиках условного доступа в режиме «только отчет», «аналитика на основе согласия приложений» и т. д.

Дополнительные сведения о книгах см. в разделе [Использование книг Azure Monitor для Azure Active Directory отчетов](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks). Дополнительные сведения о прекращении использования пакетов содержимого см. в записи блога о [общедоступной версии приложений Power BI шаблонов](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) .

---

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>Расширенный поиск, фильтрация и сортировка для групп доступны на портале Azure AD (общедоступная Предварительная версия).

**Тип:** Новая функция  
**Категория службы.** Управление группами  
**Возможности продукта:** Совместная работа

Мы рады сообщить о доступности общедоступной предварительной версии для расширенной работы с группами на портале Azure AD. Эти улучшения помогают лучше управлять группами и списками членов, предоставляя:

- Расширенные возможности поиска, например поиск подстрок в списках групп.
- Расширенные параметры фильтрации и сортировки для списков членов и владельцев.
- Новые возможности поиска для списков членов и владельцев.
- Более точные счетчики групп для больших групп.

Дополнительные сведения см. [в разделе Управление группами в портал Azure](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Для управления регистрацией приложений доступны новые пользовательские роли (общедоступная Предварительная версия)

**Тип:** Новая функция  
**Категория службы:** RBAC  
**Возможности продукта:** Контроль доступа

Пользовательские роли (доступные в подписке Azure AD P1 или P2) теперь могут помочь в обеспечении точного доступа, позволяя создавать определения ролей с конкретными разрешениями, а затем назначать эти роли конкретным ресурсам. Сейчас вы создаете пользовательские роли с помощью разрешений для управления регистрацией приложений, а затем назначаете роль конкретному приложению. Дополнительные сведения о пользовательских ролях см. [в разделе роли настраиваемого администратора в Azure Active Directory (Предварительная версия)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview).

Если вам нужны дополнительные разрешения или ресурсы, которые сейчас не отображаются, вы можете отправить отзыв на наш [сайт обратной связи Azure](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) , и мы добавим ваш запрос на карту обновления в дороге.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Новые журналы подготовки могут помочь в мониторинге и устранении неполадок развертывания подготовки приложений (общедоступная Предварительная версия)

**Тип:** Новая функция  
**Категория службы:** подготовка приложений  
**Возможности продукта:** управление жизненным циклом удостоверений

Доступны новые журналы подготовки, помогающие отслеживать и устранять неполадки при развертывании пользователей и групп. Эти новые файлы журналов содержат следующие сведения:

- Какие группы были успешно созданы в [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)
- Какие роли были импортированы из [Amazon Web Services (AWS)](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws)
- Какие сотрудники не были импортированы из [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)

Дополнительные сведения см. [в разделе Подготовка отчетов на портале Azure Active Directory (Предварительная версия)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs).

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Новые отчеты о безопасности для всех администраторов Azure AD (общая доступность)

**Тип:** Новая функция  
**Категория службы:** Защита идентификации  
**Возможности продукта.** безопасность и защита идентификации

По умолчанию все администраторы Azure AD вскоре получат доступ к современным отчетам о безопасности в Azure AD. До конца сентября вы сможете использовать баннер в верхней части современных отчетов о безопасности, чтобы вернуться к старым отчетам.

Современные отчеты о безопасности предоставляют дополнительные возможности из старых версий, включая:

- Расширенная фильтрация и сортировка
- Групповые действия, такие как отклонение пользователя
- Подтверждение скомпрометированных или защищенных сущностей
- Состояние риска, охватывающее: Под угрозой, отклонено, исправлено и подтверждено скомпрометировано
- Новые обнаружения, связанные с рисками (доступны для Azure AD Premium Подписчики)

Дополнительные сведения см. в разделе [рискованные пользователи](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users-report), [рискованные входы](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins-report)и [обнаружения рисков](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections-report).

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>Назначаемое пользователем управляемое удостоверение доступно для виртуальных машин и масштабируемых наборов виртуальных машин (общая доступность)

**Тип:** Новая функция  
**Категория службы:** Управляемые удостоверения для ресурсов Azure  
**Возможности продукта:** Опыт разработчика

Назначенные пользователем управляемые удостоверения теперь общедоступны для виртуальных машин и масштабируемых наборов виртуальных машин. В рамках этого Azure может создать удостоверение в клиенте Azure AD, который является доверенным для используемой подписки, и может быть назначен одному или нескольким экземплярам службы Azure. Дополнительные сведения о назначенных пользователем управляемых удостоверениях см. в статье [что такое управляемые удостоверения для ресурсов Azure?](https://aka.ms/azuremanagedidentity).

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Пользователи могут сбрасывать пароли с помощью мобильного приложения или маркера оборудования (общая доступность).

**Тип:** Измененная функция  
**Категория службы:** Самостоятельный сброс пароля  
**Возможности продукта:** Аутентификация пользователей

Пользователи, которые зарегистрировали мобильное приложение в Организации, теперь могут сбросить свой пароль, утверждая уведомление из Microsoft Authenticator приложения или введя код из своего мобильного приложения или маркера оборудования.

Дополнительные сведения см. в разделе [How, что он работает: Самостоятельный сброс пароля](https://aka.ms/authappsspr). Дополнительные сведения о пользовательском интерфейсе см. в статье [о сбросе собственного рабочего или учебного пароля](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview).

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET игнорирует общий кэш MSAL.NET для сценариев от имени.

**Тип:** исправление  
**Категория службы:** аутентификация (вход)  
**Возможности продукта:** Аутентификация пользователей

Начиная с библиотеки проверки подлинности Azure AD (ADAL.NET) версии 5.0.0-Preview, разработчики приложений должны [сериализовать один кэш на учетную запись для веб-приложений и веб-API](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). В противном случае в некоторых сценариях, использующих [поток «от имени](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow)» и в некоторых случаях использования `UserAssertion`, может возникнуть повышение привилегий. Чтобы избежать этой уязвимости, ADAL.NET теперь игнорирует общий кэш библиотеки проверки подлинности Майкрософт для DotNet (MSAL.NET) для сценариев от имени.

Дополнительные сведения об этой неполадке см. в разделе [Azure Active Directory уязвимость повышения привилегий в библиотеке проверки подлинности](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Новые Федеративные приложения, доступные в коллекции Azure AD App — 2019 августа

**Тип:** Новая функция  
**Категория службы:** корпоративные приложения  
**Возможности продукта:** интеграция сторонних продуктов

В августе 2019 мы добавили 26 новых приложений с поддержкой Федерации в коллекцию приложений:

[Civic Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial), [Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial), [ProNovos Ops Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial), [Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial), [Viareport's Inativ Portal (Европа)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial), [Academy Attendance](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial), [Priority Matrix](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [Carbonite Endpoint Backup](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial), [CPQSync by Cincom](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial), [Chargebee](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial), [deliver.media™ Portal](https://portal.deliver.media), [Frontline Education](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat AD connect](https://www.stashcat.com), [Blink](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial), [Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial), [ProNovos Analytics](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial), [Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial), [Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial), [Watch by Colors](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial), [Harness](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial), [EAB Navigate Strategic Care](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>Доступны новые версии модулей PowerShell AzureAD и AzureADPreview

**Тип:** Измененная функция  
**Категория службы:** Другие  
**Возможности продукта:** Каталог

Доступны новые обновления модулей PowerShell для AzureAD и AzureAD Preview:

- Новый параметр `-Filter` добавлен в параметр `Get-AzureADDirectoryRole` в модуле AzureAD. Этот параметр позволяет фильтровать роли каталога, возвращаемые командлетом.
- В модуль AzureADPreview были добавлены новые командлеты, которые помогут определить и назначить пользовательские роли в Azure AD, в том числе:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Усовершенствования в пользовательском интерфейсе построителя динамических групп правил в портал Azure

**Тип:** Измененная функция  
**Категория службы:** Управление группами  
**Возможности продукта:** Совместная работа

Мы внесли некоторые улучшения пользовательского интерфейса в построитель динамических групп правил, доступный в портал Azure, чтобы упростить настройку нового правила или изменить существующие правила. Это улучшение проектирования позволяет создавать правила с использованием до пяти выражений, а не только одного. Мы также обновили список свойств устройства, чтобы удалить нерекомендуемые свойства устройства.

Дополнительные сведения см. в разделе [Управление правилами динамического членства](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-update-rule).

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Доступно новое разрешение приложения Microsoft Graph для использования с проверками доступа

**Тип:** Измененная функция  
**Категория службы.** Проверки доступа  
**Возможности продукта:** Identity Governance

Мы предоставили новое разрешение Microsoft Graph приложения `AccessReview.ReadWrite.Membership`, которое позволяет приложениям автоматически создавать и получать проверки доступа для членства в группах и назначений приложений. Это разрешение можно использовать в запланированных заданиях или в рамках автоматизации, не требуя контекста пользователя, выполнившего вход.

Дополнительные сведения см. в [примере, посвященном созданию проверок доступа Azure AD с помощью Microsoft Graph разрешений приложения в блоге PowerShell](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>В Azure Monitor теперь доступны журналы действий Azure AD для экземпляров облаков для государственных организаций

**Тип:** Измененная функция  
**Категория службы:** Отчеты  
**Возможности продукта:** мониторинг и создание отчетов

Мы рады сообщить, что теперь журналы действий Azure AD доступны для облачных экземпляров государственных организаций в Azure Monitor. Теперь вы можете отправить журналы Azure AD в учетную запись хранения или в концентратор событий, чтобы интегрировать их со средствами SIEM, такими как [Sumologic](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic), [Splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)и [ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight). 

Дополнительные сведения о настройке Azure Monitor см. в статье [журналы действий Azure AD в Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Обновите свои пользователи до новой, расширенной информации о безопасности

**Тип:** Измененная функция  
**Категория службы:**  аутентификация (вход)   
**Возможности продукта:** Аутентификация пользователей

25 сентября 2019 г. Мы будем отключать старые, нерасширенные сведения о безопасности для регистрации и управления сведениями о безопасности пользователей и включать только новую, [расширенную версию](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271). Это означает, что пользователи больше не смогут использовать старый интерфейс.

Дополнительные сведения о расширенном интерфейсе сведений о безопасности см. в [документации администратора](https://aka.ms/securityinfodocs) and в нашей [пользовательской документации](https://aka.ms/securityinfoguide).

#### <a name="to-turn-on-this-new-experience-you-must"></a>Чтобы включить этот новый интерфейс, необходимо выполнить следующие действия.

1. Войдите в портал Azure в качестве глобального администратора или администратора пользователей.

2. Перейдите в раздел **Azure Active Directory > Параметры пользователя > Управление параметрами для функций предварительной версии панели доступа**.

3. В разделе **пользователи могут использовать функции предварительной версии для регистрации и управления сведениями о безопасности — Расширенная** , выберите пункт **выбрано**, а затем выберите группу пользователей или выберите **все** , чтобы включить эту функцию для всех пользователей в клиенте.

4. В разделе * * пользователи могут использовать функции предварительной версии для регистрации и управления областью безопасности * * info * * * *, выберите **нет**.

5. Сохраните параметры.

    После сохранения параметров вы больше не сможете получить доступ к старому интерфейсу сведений о безопасности.

>[!Important]
>Если вы не выполните эти действия до 25 сентября 2019, ваш клиент Azure Active Directory будет автоматически включен для повышения удобства работы. Если у вас возникнут вопросы, свяжитесь с нами по адресу registrationpreview@microsoft.com.

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Запросы проверки подлинности, использующие имена входа POST, будут проверяться более строго

**Тип:** Измененная функция  
**Категория службы:** аутентификация (вход)  
**Возможности продукта:** Стандарты

Начиная с 2 сентября 2019, запросы проверки подлинности, использующие метод POST, будут более строго проверяться по стандартам HTTP. В частности, пробелы и двойные кавычки (") больше не будут удаляться из значений формы запроса. Эти изменения не приводят к нарушению работы существующих клиентов и позволяют гарантировать, что запросы, отправляемые в Azure AD, будут надежно обрабатываться каждый раз.

Дополнительные сведения см. в статье [уведомления о критических изменениях в Azure AD](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored).

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
**Возможности продукта:** безопасность и защита идентификации

Мы удалим IP-адрес сервера MFA из [IP-адреса и с URL Office 365](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Если в настоящее время вы используете эти страницы для обновления параметров брандмауэра, необходимо убедиться, что вы также включаете список IP-адресов, описанных в разделе **требования к брандмауэру сервер многофакторной идентификации Azure** статьи [Приступая к работе. в статье сервер Многофакторной идентификации Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements) .

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Для токенов только для приложений теперь требуется, чтобы клиентское приложение существовало в клиенте ресурса.

**Тип:** исправление  
**Категория службы:** аутентификация (вход)  
**Возможности продукта:** Аутентификация пользователей

26 июля 2019 г. Мы изменили, как мы предоставляем маркеры только для приложений через [предоставление учетных данных клиента](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow). Ранее приложения могли получить токены для вызова других приложений, независимо от того, находилось ли клиентское приложение у клиента. Мы обновили это поведение, так что ресурсы с одним клиентом, иногда называемые веб-API, могут вызываться только клиентскими приложениями, которые существуют в клиенте ресурса.

Если ваше приложение не расположено в клиенте ресурсов, вы получите сообщение об ошибке с текстом `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`, чтобы устранить эту проблему, необходимо создать субъект клиента службы приложений в клиенте с помощью [конечной точки согласия администратора](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) или с [помощью PowerShell](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell). Это гарантирует, что клиент предоставил приложению разрешение на работу в клиенте.

Дополнительные сведения см. в разделе [новые возможности проверки подлинности](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Существующее согласие между клиентом и API-интерфейсом остается необязательным. Приложения по-прежнему должны выполнять свои собственные проверки авторизации.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Новый безпарольный вход в Azure AD с помощью ключей безопасности FIDO2

**Тип:** Новая функция  
**Категория службы.** аутентификация (вход)  
**Возможности продукта:** Аутентификация пользователей

Теперь клиенты Azure AD могут задавать политики для управления ключами безопасности FIDO2 для пользователей и групп Организации. Конечные пользователи также могут самостоятельно регистрировать свои ключи безопасности, использовать ключи для входа в учетные записи Майкрософт на веб-сайтах, пока на устройствах с поддержкой FIDO, а также входить на устройства Windows 10, присоединенные к Azure AD.

Дополнительные сведения см. [в статье Включение входа без пароля для Azure AD (Предварительная версия)](/azure/active-directory/authentication/concept-authentication-passwordless) для сведений, относящихся к администратору, и Настройка сведений о [безопасности для использования ключа безопасности (Предварительная версия)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) для сведений, относящихся к конечным пользователям.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Новые Федеративные приложения, доступные в коллекции Azure AD App-Июль 2019

**Тип:** Новая функция  
**Категория службы:** корпоративные приложения  
**Возможности продукта:** интеграция сторонних продуктов

В июле 2019 мы добавили 18 новых приложений с поддержкой Федерации в коллекцию приложений:

[Унжербоекк Software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [яркий шаблон омничаннел Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), [Нелли](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [Аккуиреио](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [Луоп](https://www.looop.co/schedule-a-demo/), [продуктбоард](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), Microsoft [Azure SSO Access для есидекс соответствует Office™](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [устанавливать ориентиры](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [ Abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [хождение](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [флипснакк](https://www.flipsnack.com/accounts/sign-in-sso.html), [Вандера](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [твинесоЦиал](https://twinesocial.com/), [каллидус](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [хиперанна](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [фармид вастевитнесс](https://www.pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog артефакты](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Автоматизация подготовки учетных записей пользователей для новых поддерживаемых приложений SaaS

**Тип:** Новая функция  
**Категория службы:** корпоративные приложения  
**Возможности продукта:** мониторинг и создание отчетов

Теперь вы можете автоматизировать создание, обновление и удаление учетных записей пользователей для новых интегрированных приложений:

- [Поднимая](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Федеративный каталог](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [пеакон](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Дополнительные сведения о том, как лучше защитить организацию с помощью автоматизированной подготовки учетных записей пользователей, см. в статье [Автоматизация подготовки пользователей в приложениях SaaS с помощью Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) .

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Новый тег доменных служб Azure AD для группы безопасности сети

**Тип:** Новая функция  
**Категория службы:** Доменные службы Azure AD  
**Возможности продукта:** Доменные службы Azure AD

Если вы устали управлять длинными списками IP-адресов и диапазонов, вы можете использовать новый тег **азуреактиведиректоридомаинсервицес** Network Service в группе безопасности сети Azure, чтобы защитить входящий трафик к виртуальным службам домена Azure AD. Сетевая подсеть.

Дополнительные сведения об этом новом теге службы см. в статье [группы безопасности сети для доменных служб Azure AD](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Новые Аудиты безопасности для доменных служб Azure AD (общедоступная Предварительная версия)

**Тип:** Новая функция  
**Категория службы:** Доменные службы Azure AD  
**Возможности продукта:** Доменные службы Azure AD

Мы рады сообщить о выпуске аудита безопасности службы домена Azure AD до общедоступной предварительной версии. Аудит безопасности позволяет получить важные сведения о службах проверки подлинности путем потоковой передачи событий аудита безопасности в целевые ресурсы, включая службу хранилища Azure, рабочие области Azure Log Analytics и концентратор событий Azure, с помощью службы домена Azure AD. портала.

Дополнительные сведения см. в статье [Включение аудита безопасности для доменных служб Azure AD (Предварительная версия)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Новые методы проверки подлинности использование & Insights (общедоступная Предварительная версия)

**Тип:** Новая функция  
**Категория службы:** Самостоятельный сброс пароля  
**Возможности продукта:** мониторинг и создание отчетов

Новые методы проверки подлинности & отчеты об аналитике позволяют понять, как в организации регистрируются такие функции, как многофакторная идентификация Azure и самостоятельный сброс пароля, включая число зарегистрированных. Пользователи для каждого компонента, частота использования самостоятельного сброса пароля для сброса паролей и метод сброса.

Дополнительные сведения см. в статье [использование методов проверки Подлинности & Insights (Предварительная версия)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Новые отчеты о безопасности доступны для всех администраторов Azure AD (общедоступная Предварительная версия)

**Тип:** Новая функция  
**Категория службы:** Защита идентификации  
**Возможности продукта.** безопасность и защита идентификации

Все администраторы Azure AD теперь могут выбрать баннер в верхней части существующих отчетов о безопасности, например отчет " **Пользователи, помеченные для риска** ", чтобы приступить к использованию новой системы безопасности, как показано в отчетах о **рискованных пользователях** и событиях **входа в систему** . . Со временем все отчеты о безопасности будут перенесены из старых версий в новые, а новые отчеты предоставляют следующие дополнительные возможности:

- Расширенная фильтрация и сортировка

- Групповые действия, такие как отклонение пользователя

- Подтверждение скомпрометированных или защищенных сущностей

- Состояние риска, охватывающее: Под угрозой, отклонено, исправлено и подтверждено скомпрометировано

Дополнительные сведения см. в разделе Отчет [о рискованных пользователях](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users-report) и отчет о событиях [входа с рисками](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins-report).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Новые Аудиты безопасности для доменных служб Azure AD (общедоступная Предварительная версия)

**Тип:** Новая функция  
**Категория службы:** Доменные службы Azure AD  
**Возможности продукта:** Доменные службы Azure AD

Мы рады сообщить о выпуске аудита безопасности службы домена Azure AD до общедоступной предварительной версии. Аудит безопасности позволяет получить важные сведения о службах проверки подлинности путем потоковой передачи событий аудита безопасности в целевые ресурсы, включая службу хранилища Azure, рабочие области Azure Log Analytics и концентратор событий Azure, с помощью службы домена Azure AD. портала.

Дополнительные сведения см. в статье [Включение аудита безопасности для доменных служб Azure AD (Предварительная версия)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Новая прямая Федерация B2B с использованием SAML/WS-подача (общедоступная Предварительная версия)

**Тип:** Новая функция  
**Категория службы:** B2B  
**Возможности продукта:** B2B или B2C

Прямая Федерация позволяет упростить работу с партнерами, для которых управляемое ИТ-решение не является Azure AD, работая с системами идентификации, поддерживающими стандарты SAML или WS-подач. После настройки прямой связи Федерации с партнером любой новый гостевой пользователь, который вы пригласили от этого домена, сможет совместно работать с вами с помощью существующей учетной записи организации, что делает работу пользователя с вашими гостями более удобной.

Дополнительные сведения см. [в статье прямая Федерация с AD FS и сторонними поставщиками для гостевых пользователей (Предварительная версия)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Автоматизация подготовки учетных записей пользователей для новых поддерживаемых приложений SaaS

**Тип:** Новая функция  
**Категория службы:** корпоративные приложения  
**Возможности продукта:** мониторинг и создание отчетов

Теперь вы можете автоматизировать создание, обновление и удаление учетных записей пользователей для новых интегрированных приложений:

- [Поднимая](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Федеративный каталог](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [пеакон](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Дополнительные сведения о том, как лучше защитить организацию с помощью автоматизированной подготовки учетных записей пользователей, см. в статье [Автоматизация подготовки пользователей в приложениях SaaS с помощью Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Новая проверка повторяющихся имен групп на портале Azure AD

**Тип:** Новая функция  
**Категория службы.** Управление группами  
**Возможности продукта:** Совместная работа

Теперь при создании или обновлении имени группы на портале Azure AD мы продолжим проверку на дублирование существующего имени группы в ресурсе. Если мы определили, что имя уже используется другой группой, вам будет предложено изменить имя.

Дополнительные сведения см. [в статье Управление группами на портале Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD теперь поддерживает статические параметры запроса в URI ответа (Redirect)

**Тип:** Новая функция  
**Категория службы.** аутентификация (вход)  
**Возможности продукта:** Аутентификация пользователей

Приложения Azure AD теперь могут регистрировать и использовать URI-идентификаторы ответа (перенаправления) со статическими параметрами запроса (например, `https://contoso.com/oauth2?idp=microsoft`) для запросов OAuth 2,0. Параметр статического запроса подчиняется строковому совпадению для URI ответа, как и любая другая часть URI ответа. Если нет ни одной зарегистрированной строки, соответствующей URL-адресу, декодированному, то запрос отклоняется. Если URI ответа найден, то для перенаправления пользователя используется вся строка, включая параметр статического запроса.

Идентификаторы URI динамических ответов по-прежнему запрещены, так как они представляют угрозу безопасности и не могут использоваться для сохранения сведений о состоянии в запросе проверки подлинности. Для этой цели используйте параметр `state`.

В настоящее время экраны регистрации приложений портал Azure все еще блокируют параметры запроса. Однако можно вручную изменить манифест приложения, чтобы добавить и проверить параметры запроса в приложении. Дополнительные сведения см. в разделе [новые возможности проверки подлинности](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Журналы действий (API-интерфейсы MS Graph) для Azure AD теперь доступны с помощью командлетов PowerShell.

**Тип:** Новая функция  
**Категория службы:** Отчеты  
**Возможности продукта:** мониторинг и создание отчетов

Мы рады сообщить, что журналы действий Azure AD (отчеты для аудита и входа) теперь доступны в модуле Azure AD PowerShell. Ранее можно было создавать собственные сценарии с помощью конечных точек MS API Graph, и теперь мы расширили эту возможность для командлетов PowerShell.

Дополнительные сведения об использовании этих командлетов см. в разделе [командлеты Azure AD PowerShell для создания отчетов](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Обновлены элементы управления фильтра для журналов аудита и входа в Azure AD.

**Тип:** Измененная функция  
**Категория службы:** Отчеты  
**Возможности продукта:** мониторинг и создание отчетов

Отчеты журнала аудита и входа обновлены, поэтому теперь можно применять различные фильтры, не добавляя их в качестве столбцов на экранах отчета. Кроме того, теперь можно решить, сколько фильтров нужно отображать на экране. Все эти обновления работают вместе, чтобы сделать отчеты более удобными для чтения и в большей области с учетом ваших потребностей.

Дополнительные сведения об этих обновлениях см. в разделе [Фильтрация журналов аудита](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) и [Фильтрация действий входа](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities).

---

## <a name="june-2019"></a>Июнь 2019 г.

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Новый API Рискдетектионс для Microsoft Graph (общедоступная Предварительная версия)

**Тип:** Новая функция  
**Категория службы:** Защита идентификации  
**Возможности продукта.** безопасность и защита идентификации

Мы рады сообщить о новом API Рискдетектионс для Microsoft Graph теперь в общедоступной предварительной версии. Вы можете использовать этот новый API для просмотра списка обнаруженных в Организации рисков, связанных с защитой удостоверений и пользователями. Этот API также можно использовать для более эффективного выполнения запросов к обнаружению рисков, включая сведения о типе обнаружения, состоянии, уровне и т. д.

Дополнительные сведения см. в [справочной документации по API обнаружения рисков](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Новые Федеративные приложения, доступные в коллекции приложений Azure AD — Июнь 2019

**Тип:** Новая функция  
**Категория службы:** корпоративные приложения  
**Возможности продукта:** интеграция сторонних продуктов

В июне 2019 мы добавили 22 новых приложения с поддержкой Федерации в коллекцию приложений:

[Azure AD SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Отсука шокаи (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [анакуа](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [VPN-клиент Azure](https://portal.azure.com/), [расход](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [вспомогательный вспомогательный модуль](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial), [костпоинт](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [глобалоне](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Мерцедес-бенз в автомобиле офисе](https://me.secure.mercedes-benz.com/), [скоре](https://app.justskore.it/), [Консоль инфраструктуры облака Oracle](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [Аутентификация циберарк SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [скрибле edu](https://www.scrible.com/sign-in/#/create-account), [пандадок](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [перцептикс](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Проптимисе OS](https://proptimise.co.uk/software/), [втижер CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager для Oracle Товары розничной торговли, диспетчер доступа Oracle для Oracle E-бизнес Suite, Oracle ИДКС для E-Business Suite, Oracle ИДКС для PeopleSoft, Oracle ИДКС для JD Edwards

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Автоматизация подготовки учетных записей пользователей для новых поддерживаемых приложений SaaS

**Тип:** Новая функция  
**Категория службы:** корпоративные приложения  
**Возможности продукта:** мониторинг и создание отчетов

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
**Возможности продукта:** управление жизненным циклом удостоверений

Мы обновили интерфейс подготовки Azure AD, включив в него новый индикатор выполнения, который показывает, насколько вы находились в процессе подготовки пользователей. Этот обновленный интерфейс также предоставляет сведения о количестве пользователей, подготовленных в ходе текущего цикла, а также о количестве пользователей, которые были подготовлены к работе.

Дополнительные сведения см. [в разделе Проверка состояния подготовки пользователей](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Теперь при выходе из системы и на экранах ошибок отображается фирменная символика организации

**Тип:** Измененная функция  
**Категория службы:** аутентификация (вход)  
**Возможности продукта:** Аутентификация пользователей

Мы обновили Azure AD, чтобы фирменная символика компании появилась на экранах выхода и ошибок, а также на странице входа. Чтобы включить эту функцию, Azure AD просто использует уже настроенные ресурсы в области **фирменной символики компании** портал Azure.

Дополнительные сведения о настройке фирменной символики компании см. [в статье Добавление фирменной символики на Azure Active Directory страницы вашей организации](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Сервер многофакторной идентификации Azure (MFA) больше не доступен для новых развертываний

**Тип:** Не рекомендуется  
**Категория службы:** MFA  
**Возможности продукта:** безопасность и защита идентификации

По состоянию на 1 июля 2019 Корпорация Майкрософт больше не будет предлагать сервер MFA для новых развертываний. Новые клиенты, желающие требовать многофакторную проверку подлинности в Организации, теперь должны использовать службу многофакторной идентификации Azure на основе облака. Клиенты, которые активировали сервер MFA до 1 июля, не увидят изменений. Вы по-прежнему сможете скачать последнюю версию, получить будущие обновления и создать учетные данные активации.

Дополнительные сведения см. [в статье Приступая к работе с сервер многофакторной идентификации Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy). Дополнительные сведения об облачной многофакторной идентификации Azure см. в статье [Планирование развертывания многофакторной идентификации Azure на основе облачных служб](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

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
**Возможности продукта:** мониторинг и создание отчетов

Теперь можно использовать отчет об использовании и Insights, расположенный в области **корпоративные приложения** портал Azure, чтобы получить представление данных входа, ориентированное на приложения, включая следующие сведения:

- Популярные приложения, используемые в Организации

- Приложения с наиболее неудачными входами

- Основные ошибки входа для каждого приложения

Дополнительные сведения об этой функции см. [в разделе Отчет об использовании и Insights на портале Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report) .

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Автоматизация подготовки пользователей в облачных приложениях с помощью Azure AD

**Тип:** Новая функция  
**Категория службы:** корпоративные приложения  
**Возможности продукта:** мониторинг и создание отчетов

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
**Возможности продукта:** безопасность и защита идентификации

Теперь вы можете отслеживать и улучшать безопасность идентификации с помощью функции "Оценка безопасности удостоверений" в Azure AD. Функция оценки безопасности идентификации использует одну панель мониторинга, которая поможет вам:

- Пообъектно измерять уровень безопасности идентификации на основе оценки от 1 до 223.

- Планирование улучшений безопасности удостоверений

- Изучите успешность улучшения безопасности

Дополнительные сведения о функции оценки безопасности удостоверений см. [в разделе что такое идентификатор защиты идентификации в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Теперь доступна новая возможность Регистрация приложений (общая доступность)

**Тип:** Новая функция  
**Категория службы.** аутентификация (вход)  
**Возможности продукта:** Опыт разработчика

Новая работа [Регистрация приложений](https://aka.ms/appregistrations) теперь доступна в общедоступной области. Этот новый интерфейс включает все основные функции, с которыми вы знакомы, с помощью портал Azure и портала регистрации приложений, и они улучшаются с помощью следующих средств:

- **Улучшенное управление приложениями.** Вместо того чтобы просматривать приложения на разных порталах, теперь можно просматривать все приложения в одном месте.

- **Упрощенная регистрация приложения.** С улучшенной навигацией по усовершенствованному интерфейсу выбора разрешений теперь стало проще регистрировать приложения и управлять ими.

- **Более подробные сведения.** Вы можете найти дополнительные сведения о приложении, включая краткие руководства и многое другое.

Дополнительные сведения см. в [статье платформа Microsoft Identity](https://docs.microsoft.com/azure/active-directory/develop/) и [Регистрация приложений теперь общедоступна.](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) объявление в блоге.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Новые возможности, доступные в API рискованных пользователей для защиты идентификации

**Тип:** Новая функция  
**Категория службы:** Защита идентификации  
**Возможности продукта.** безопасность и защита идентификации

Мы рады сообщить о том, что теперь вы можете использовать API рискованных пользователей для получения журнала рисков пользователей, закрытия рискованных пользователей и подтверждения пользователей как скомпрометированных. Это изменение позволяет более эффективно обновлять состояние риска для пользователей и анализировать историю рисков.

Дополнительные сведения см. в [справочной документации по API рискованных пользователей](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Новые Федеративные приложения, доступные в коллекции приложений Azure AD — Май 2019

**Тип:** Новая функция  
**Категория службы:** корпоративные приложения  
**Возможности продукта:** интеграция сторонних продуктов

В 2019 мая мы добавили в коллекцию приложений 21 новые приложения с поддержкой федерации:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [Real Links](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Sign](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [Displayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo Sales Engage](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [OutSystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [Meta4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [Quantum Workplace](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [Cobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [webMethods API Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Улучшенные возможности создания групп и управления ими на портале Azure AD

**Тип:** Новая функция  
**Категория службы.** Управление группами  
**Возможности продукта:** Совместная работа

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
**Возможности продукта:** Совместная работа

Теперь администраторы могут настроить политику именования для групп Office 365 с помощью портала Azure AD. Это изменение помогает обеспечить соответствие соглашений об именовании для групп Office 365, созданных или измененных пользователями в Организации.

Политику именования для групп Office 365 можно настроить двумя разными способами.

- Определите префиксы или суффиксы, которые автоматически добавляются в имя группы.

- Отправка настроенного набора заблокированных слов для Организации, которые не разрешены в именах групп (например, "Исполнительный директор, зарплата, HR").

Дополнительные сведения см. [в разделе принудительная политика именования для групп Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Конечные точки API Microsoft Graph теперь доступны для журналов действий Azure AD (общая доступность).

**Тип:** Измененная функция  
**Категория службы:** Отчеты  
**Возможности продукта:** мониторинг и создание отчетов

Мы рады сообщить о выходе общедоступной версии Microsoft Graph поддержки конечных точек API для журналов действий Azure AD. В этом выпуске теперь можно использовать версии 1,0 журналов аудита Azure AD, а также интерфейсы API для входа в систему.

Дополнительные сведения см. в статье [Общие сведения об API журнала аудита Azure AD](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Теперь администраторы могут использовать условный доступ для Объединенного процесса регистрации (общедоступная Предварительная версия)

**Тип:** Новая функция  
**Категория службы.** Условный доступ  
**Возможности продукта.** безопасность и защита идентификации  

Теперь администраторы могут создавать политики условного доступа для использования в общей странице регистрации. Сюда входит применение политик для разрешения регистрации, если:

- Пользователи находятся в доверенной сети.

- Пользователи являются низким риском для входа.

- Пользователи находятся на управляемом устройстве.

- Пользователи согласны с условиями использования (условий использования) Организации.

Дополнительные сведения о условном доступе и сбросе пароля см. в [блоге о условном доступе для совместной работы по регистрации MFA и сброса пароля в Azure AD](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Дополнительные сведения о политиках условного доступа для Объединенного процесса регистрации см. в разделе [политики условного доступа для Объединенной регистрации](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration). Дополнительные сведения о функции "условия использования Azure AD" см. в разделе [Azure Active Directory условия использования](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

## <a name="april-2019"></a>Апрель 2019 г.

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Новое обнаружение аналитики угроз Azure AD теперь доступно в составе защита идентификации Azure AD

**Тип:** Новая функция  
**Категория службы:** Защита идентификации Azure AD  
**Возможности продукта:** безопасность и защита идентификации

Обнаружение аналитики угроз Azure AD теперь доступно в составе обновленной защита идентификации Azure AD функции. Эта новая функция позволяет указать необычные действия пользователя для конкретного пользователя или действия, которые соответствуют известным шаблонам атак, основанным на внутренних и внешних источниках анализа угроз Майкрософт.

Дополнительные сведения о обновленной версии защита идентификации Azure AD см. в блоге, посвященном [четырем усовершенствованиям Защита идентификации Azure AD](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) , а также о том, [что защита идентификации Azure Active Directory (Обновлено)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) рассмотрен. Дополнительные сведения об обнаружении аналитики угроз в Azure AD см. в статье [Защита идентификации Azure Active Directory обнаружения рисков](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence) .

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
**Возможности продукта:** Совместная работа

Теперь администраторы могут настроить политику именования для групп Office 365 с помощью портала Azure AD. Это изменение помогает обеспечить соответствие соглашений об именовании для групп Office 365, созданных или измененных пользователями в Организации.

Политику именования для групп Office 365 можно настроить двумя разными способами.

- Определите префиксы или суффиксы, которые автоматически добавляются в имя группы.

- Отправка настроенного набора заблокированных слов для Организации, которые не разрешены в именах групп (например, "Исполнительный директор, зарплата, HR").

Дополнительные сведения см. [в разделе принудительная политика именования для групп Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Журналы действий Azure AD теперь доступны в Azure Monitor (общая доступность)

**Тип:** Новая функция  
**Категория службы:** Отчеты  
**Возможности продукта:** мониторинг и создание отчетов

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
**Возможности продукта:** интеграция сторонних продуктов

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

Azure AD Connect находится в процессе переноса системы предупреждений электронной почты, потенциально показывая некоторым клиентам новый отправитель электронной почты. Чтобы решить эту эту проблемы, необходимо добавить `azure-noreply@microsoft.com` в список разрешений вашей организации или продолжить получение важных оповещений из Office 365, Azure или ваших служб синхронизации.

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
**Возможности продукта.** безопасность и защита идентификации

Условный доступ на основе защиты приложений теперь доступен с помощью политики " **требуется политика защиты приложений** ". Эта новая политика позволяет повысить безопасность вашей организации, помогая предотвратить следующее:

- Пользователи получают доступ к приложениям без лицензии Microsoft Intune.

- Пользователи не могут получить политику защиты приложений Microsoft Intune.

- Пользователи получают доступ к приложениям без настроенной политики защиты приложений Microsoft Intune.

Дополнительные сведения см. [в разделе как требовать политику защиты приложений для облачного доступа к приложениям с помощью условного доступа](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Новая поддержка единого входа и условного доступа Azure AD в Microsoft ребр (общедоступная Предварительная версия)

**Тип:** Новая функция  
**Категория службы.** Условный доступ  
**Возможности продукта.** безопасность и защита идентификации

Мы улучшили поддержку Azure AD для Microsoft ребра, включая предоставление новой поддержки единого входа и условного доступа Azure AD. Если вы ранее использовали Microsoft Intune Managed Browser, теперь можно использовать Microsoft ребро.

Дополнительные сведения о настройке устройств и приложений, а также об управлении ими с помощью условного доступа см. в статьях использование [управляемых устройств для доступа к облачным приложениям с помощью условного](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) доступа и [требование утвержденных клиентских приложений для доступа к облачным приложениям с помощью условного доступа ](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Дополнительные сведения об управлении доступом с помощью Microsoft ребра с Microsoft Intune политиками см. [в разделе Управление доступом в Интернет с помощью Microsoft Intune браузера, защищенного политикой](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---
