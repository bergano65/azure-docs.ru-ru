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
ms.date: 02/28/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a50dadb7ae401a5655745a799e6e9fcebb8bb886
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935928"
---
# <a name="whats-new-in-azure-active-directory"></a>Новые возможности Azure Active Directory

>Получение уведомлений о том, когда вернитесь на эту страницу для обновлений, скопируйте и вставьте этот URL-адрес: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` в вашей ![RSS-канала чтения значок](./media/whats-new/feed-icon-16x16.png) чтения веб-канала.

Azure AD усовершенствуется на постоянной основе. Чтобы вы оставались в курсе последних разработок, в этой статье предоставлены такие сведения:

- Последние выпуски.
- Известные проблемы
- Исправления ошибок
- Нерекомендуемые функции.
- Планы по изменениям.

Эта страница обновляется ежемесячно, поэтому регулярно пересматривайте ее. Если вам понадобятся материалы, которым более шести месяцев, их можно найти в статье [Архив сведений о новых возможностях в Azure Active Directory](whats-new-archive.md).

---

## <a name="april-2019"></a>Апреля 2019 г.

### <a name="azure-active-directory-azure-ad-entitlement-management-is-now-available-public-preview"></a>Управление правами Azure Active Directory (Azure AD) — теперь доступны (Предварительная версия)

**Тип:** Новая функция  
**Категория службы:** Функции управления удостоверениями  
**Возможности продукта:** Функции управления удостоверениями

Управление правами на Azure AD, теперь в общедоступной предварительной версии, помогает клиентам делегировать управление пакеты, который определяет как сотрудникам и деловым партнерам можно запросить доступ, кто должен утверждать и как долго они имеют доступ. Пакеты можно управлять, членство в Azure AD и Office 365 группы, назначения ролей в разделе корпоративных приложений и назначений ролей для сайтов SharePoint Online. Дополнительные сведения о управление правами на [Общие сведения об управлении Azure AD правах](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Дополнительные сведения о спектр функций управления удостоверениями Azure AD, включая управление привилегированными пользователями "," проверки доступа "и" условия использования, см. в разделе [новые возможности управления удостоверениями Azure AD?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Настройка политики именования групп Office 365 на портале Azure AD (Предварительная версия)

**Тип:** Новая функция  
**Категория службы.** Управление группами  
**Возможности продукта:** Совместная работа

Администраторы теперь могут настроить политики именования групп Office 365, с помощью портала Azure AD. Это изменение позволяет применить соответствующие соглашения об именовании групп Office 365 созданных или измененных пользователей в вашей организации. 

Политика именования групп Office 365 можно настроить двумя способами:

- Определите префиксы или суффиксы, которые автоматически добавляются имя группы.

- Загрузите настраиваемый набор запрещенных слов для вашей организации, которые не допускаются в именах группы (например, «ГЕНДИР, Зарплата, Отдел_кадров»).

Дополнительные сведения см. в разделе [принудительно применять политику именования групп Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Журналы действий AD Azure теперь доступны в Azure Monitor (общедоступная)

**Тип:** Новая функция  
**Категория службы:** Отчеты  
**Возможности продукта:** мониторинг и создание отчетов

Для решения ваших отзывов о визуализации с помощью журналов действий Azure AD, мы представляем новый функция аналитики в службе Log Analytics. Эта функция помогает получить сведения о ресурсах Azure AD с помощью наших интерактивных шаблонов, называется книгами. К этим готовым книг содержат сведения для приложений или пользователей и включают:

- **Входы в систему.** Подробные сведения о приложениях и пользователях, включая расположение входа, используемой операционной системы или браузера клиента и версии и количество успешных или неудачных входов.

- **Устаревшая проверка подлинности и условного доступа.** Подробные сведения о приложениях и пользователей с помощью проверки подлинности, включая использование многофакторной проверки подлинности, запускаемой политики условного доступа, приложений с помощью политик условного доступа и т. д.

- **Анализ сбоя входа в систему.** Позволяет определить, если ошибки входа происходят из-за действий пользователя, проблем с политикой или инфраструктуры.

- **Пользовательские отчеты.** Можно создавать новые или изменить существующие книги для помощи в индивидуальной функция аналитики для вашей организации.

Дополнительные сведения см. в разделе [способы использования книг Azure Monitor для отчетов Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Новые федеративные приложения доступны в коллекции приложений Azure AD - апреля 2019 г.

**Тип:** Новая функция  
**Категория службы:** корпоративные приложения  
**Возможности продукта:** интеграция сторонних продуктов

В апреля 2019 г. мы добавили поддержку этих 21 новые приложения с федерацией в коллекцию приложений:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol--tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [ Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [Benchling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio Подключение](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [подключения Mitel](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba Cloud (единый вход на основе ролей)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent собственных средств управления](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Диспетчера сертификатов Sectigo](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [ SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Новая модель доступа рассматриваются параметр частоты и множественный выбор роли

**Тип:** Новая функция  
**Категория службы.** Проверки доступа  
**Возможности продукта:** Функции управления удостоверениями

Новые обновления в Azure AD проверки доступа позволяют:

- Изменение частоты вашего доступа, используя запрос **частично annually**, в дополнение к ранее существующие параметры еженедельно, ежемесячно, ежеквартально и один раз в год.

- Выберите несколько Azure AD и просмотрите ролей ресурсов Azure при создании единого доступа. В этом случае все роли настраиваются с теми же параметрами, и все рецензенты уведомляются в то же время.

Дополнительные сведения о том, как создать проверку доступа см. в разделе [Создание проверки доступа групп или приложений в Azure AD проверок доступа](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Оповещения системы электронной почты Azure AD Connect переходят, отправка нового отправителя электронной почты для некоторых клиентов

**Тип:** Измененная функция  
**Категория службы:** AD Sync  
**Возможности продукта.** платформа

Azure AD Connect находится в процессе перехода наших серверов оповещения по электронной почте, потенциально, показывающий некоторые клиенты нового сообщения электронной почты отправителя. Чтобы решить эту проблему, необходимо добавить `azure-noreply@microsoft.com` список разрешений для вашей организации, или вы не сможете продолжить получать важные оповещения из Office 365, Azure или служб синхронизации.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Изменения суффикса имени участника-пользователя стали успешно между федеративные домены в Azure AD Connect

**Тип:** исправление  
**Категория службы:** AD Sync  
**Возможности продукта.** платформа

Теперь можно успешно изменить суффикс имени участника-пользователя пользователя из одного федеративного домена в другой домен федеративные в Azure AD Connect. Это исправление означает, что больше не должны получить сообщение об ошибке FederatedDomainChangeError во время цикла синхронизации или получать уведомления по электронной почте сообщение о том, «не удается обновить этот объект в Azure Active Directory, так как атрибут [ FederatedUser.UserPrincipalName], является недопустимым. Обновите значение в локальных службах каталогов».

Дополнительные сведения см. в разделе [Устранение ошибок синхронизации](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Повышение безопасности с использованием политики условного доступа на основе защиты приложений в Azure AD (Предварительная версия)

**Тип:** Новая функция  
**Категория службы.** условный доступ;  
**Возможности продукта.** безопасность и защита идентификации

Условный доступ на основе защиты приложения теперь доступна с помощью **требуют защиты приложений** политики. Эта новая политика помогает повысить безопасность вашей организации за счет предотвращения:

- Доступа пользователей к приложениям без лицензии Microsoft Intune.

- Пользователи, не смогут получить политику защиты приложений Microsoft Intune.

- Доступа пользователей к приложениям без настроенной политики защиты приложений Microsoft Intune.

Дополнительные сведения см. в разделе [способов принудительного применения политики защиты приложений для доступа к облаку приложения с условным доступом](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Новая поддержка Azure AD единого входа и условного доступа в Microsoft Edge (Предварительная версия)

**Тип:** Новая функция  
**Категория службы.** условный доступ;  
**Возможности продукта.** безопасность и защита идентификации

Мы усовершенствовали поддержке Azure AD для Microsoft Edge, в том числе предоставить новую поддержку для единого входа и условного доступа Azure AD. Если вы ранее использовали управляемый браузер Microsoft Intune, теперь можно использовать Microsoft Edge вместо этого.

Дополнительные сведения о настройке и управления устройствами и приложениями, с помощью условного доступа см. в разделе [требуются управляемые устройства для доступа к облаку приложения с условным доступом](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) и [требуют утвержденных клиентских приложений для облака доступ к приложениям с помощью условного доступа](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Дополнительные сведения о том, как управлять доступом с помощью Microsoft Edge с помощью политик Microsoft Intune см. в разделе [управление доступом в Интернет с помощью браузера Microsoft Intune политики защищенными](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Марта 2019 г.

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Инфраструктура процедур идентификации и настраиваемую политику поддержки в Azure Active Directory B2C теперь является Общедоступным

**Тип:** Новая функция  
**Категория службы.** B2C — управление удостоверениями потребителей  
**Возможности продукта.** B2B или B2C

Теперь можно создать пользовательские политики Azure AD B2C, включая следующие задачи, который поддерживается в масштабе и в СОГЛАШЕНИИ Azure:

- Создание и отправка пути взаимодействия пользователя нестандартной проверки подлинности с помощью пользовательских политик.

- Поэтапно описывать пути взаимодействия пользователя для обмена данными между поставщиками удостоверений.

- Определять условные ветвления путей взаимодействия пользователей.

- Преобразование и сопоставить утверждения для использования в решения в реальном времени, а также взаимодействия.

- Используйте службы с поддержкой REST API в путях взаимодействия пользователей нестандартной проверки подлинности. Например с помощью поставщиков электронной почты, диспетчеры CRM и систем собственности авторизации.

- Создать федерацию с поставщиками удостоверений, которые являются совместимыми с помощью протокола OpenIDConnect. Например с несколькими клиентами Azure AD, поставщиков социальных сетей учетной записи или поставщики двухфакторной проверки.

Дополнительные сведения о создании пользовательских политик, см. в разделе [примечания для разработчиков для настраиваемых политик в Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) и чтение [Алекс Simon записи блога, включая примеры внедрения](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Новые федеративные приложения доступны в коллекции приложений Azure AD - марта 2019 г.

**Тип:** Новая функция  
**Категория службы:** корпоративные приложения  
**Возможности продукта:** интеграция сторонних продуктов

В марта 2019 г. мы добавили поддержку этих 14 новых приложений с федерацией в коллекцию приложений:

[Делегат Exchange Mobile ISEC7](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [опоры](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [Система аудита на основе объяснение](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Lean](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [: вопросы и ответы производительности Powerschool](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [Iris Интрасети](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [горизонты Confirmit](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [задач](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Новые Zscaler и Atlassian подготовки соединители в коллекции Azure AD — марта 2019 г.

**Тип:** Новая функция  
**Категория службы:** подготовка приложений  
**Возможности продукта:** интеграция сторонних продуктов

Автоматизируйте создание, обновление и удаление учетных записей пользователей для следующих приложений:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler One](https://aka.ms/ZscalerOneProvisioning), [Zscaler Two](https://aka.ms/ZscalerTwoProvisioning), [Zscaler три](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud ](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

Дополнительные сведения о том, как улучшить защиту вашей организации с помощью автоматической подготовке учетных записей см. в разделе [автоматизировать подготовку пользователей для приложений SaaS в Azure AD](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Восстановление и управление удаленных групп Office 365 на портале Azure AD

**Тип:** Новая функция  
**Категория службы.** Управление группами  
**Возможности продукта:** Совместная работа

Теперь можно просматривать и управлять вашей удаленных групп Office 365 с помощью портала Azure AD. Это изменение поможет вам узнать, какие группы доступны для восстановления, а также позволяет окончательно удалить любые группы, которые не требуются вашей организации.

Дополнительные сведения см. в разделе [восстановления истек или удаленные группы](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Единый вход теперь доступна для защищенного Azure AD SAML локальным приложениям через прокси приложения (Предварительная версия)

**Тип:** Новая функция  
**Категория службы:** Прокси приложения  
**Возможности продукта:** Контроль доступа

Теперь можно предоставить возможность единого входа (SSO) для локальных, прошедших проверку подлинности SAML приложения, как и удаленного доступа к этим приложениям через прокси приложения. Дополнительные сведения о настройке единого входа SAML с приложениями на предприятии, см. в разделе [SAML единого входа для локальных приложений с помощью прокси приложения (Предварительная версия)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Клиентские приложения в циклах запрос будет прерван для повышения надежности и взаимодействия с пользователем

**Тип:** Новая функция  
**Категория службы.** аутентификация (вход)  
**Возможности продукта:** Аутентификация пользователей

Клиентские приложения неправильно может выдавать сотни же запросов на вход за короткий период времени. Эти запросы ли они успешны, или нет, участвуют плохое впечатление на пользователей и повышенную рабочих нагрузок для поставщика Удостоверений, увеличение задержки для всех пользователей и снижая доступности поставщика удостоверений.

Это обновление отправляет `invalid_grant` ошибка: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` к клиентским приложениям, которые создают запросы, повторяющийся несколько раз за короткий период времени, выходит за рамки обычной работы. Клиентские приложения, в которых возникает эта проблема должна показать интерактивное приглашение, требующее от пользователя снова выполнить вход. Дополнительные сведения об этом изменении и о том, как исправить приложения при возникновении этой ошибки, см. в разделе [новые возможности для проверки подлинности?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Журналы аудита опыта пользователей теперь доступен

**Тип:** Измененная функция  
**Категория службы:** Отчеты  
**Возможности продукта:** мониторинг и создание отчетов

Мы создали новый Azure AD **журналы аудита** страницы для повышения удобства чтения и поиска информации. Чтобы увидеть новые **журналы аудита** выберите **журналы аудита** в **действия** разделе Azure AD.

![Новая страница журналы аудита, сведения о примере](media/whats-new/audit-logs-page.png)

Дополнительные сведения о новом **журналы аудита** странице, см. в разделе [отчеты о действиях на портале Azure Active Directory аудита](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Новые предупреждения и рекомендации для предотвращения случайного администратора блокировки из неправильной настройкой политики условного доступа

**Тип:** Измененная функция  
**Категория службы.** условный доступ;  
**Возможности продукта.** безопасность и защита идентификации

Чтобы администраторы не блокировали случайно себя за пределы их собственными клиентами через неправильной настройкой политики условного доступа, мы создали новые предупреждения и обновленных рекомендаций на портале Azure. Дополнительные сведения о новое руководство, см. в разделе [Каковы зависимости служб в условном доступе Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Условия конечного пользователя улучшенные возможности использования на мобильных устройствах

**Тип:** Измененная функция  
**Категория службы:** Условия использования  
**Возможности продукта:** Система управления

Мы обновили наши существующие условия опыт использования, чтобы помочь улучшить способ просмотра и согласие с условиями использования на мобильных устройствах. Теперь можно увеличивать и уменьшать, вернуться назад, сведения о загрузке и выберите гиперссылки. Дополнительные сведения о обновленные условия использования, см. в разделе [Azure Active Directory условия возможности](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Удобство работы скачайте новые журналы действий Azure AD

**Тип:** Измененная функция  
**Категория службы:** Отчеты  
**Возможности продукта:** мониторинг и создание отчетов

Большие объемы журналов действий теперь можно загрузить непосредственно с портала Azure. Это обновление позволяет:

- Скачайте до 250 000 строк.

- Получение уведомления после завершения загрузки.

- Настроить имя файла.

- Определите формат вывода, CSV или JSON.

Дополнительные сведения об этой функции см. в разделе [краткое руководство: Загрузить отчет аудита с помощью портала Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Критическое изменение. Обновления для вычисления условия с Exchange ActiveSync (EAS)

**Тип:** план изменений  
**Категория службы:** условный доступ;  
**Возможности продукта:** Контроль доступа

Мы обновляем как Exchange ActiveSync (EAS) вычисляет следующих условий:

- Расположение пользователя, в зависимости от страны, области или IP-адрес

- Риск при входе

- Платформа устройства

Если вы ранее использовали эти условия в политиках условного доступа, имейте в виду, что условие поведение могут измениться. Например если вы ранее использовали условия расположения пользователя в политике, возможно, пропущено теперь политики на основе расположения пользователя.

---

## <a name="february-2019"></a>Февраль 2019 г.

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Можно настроить шифрование маркеров Azure AD SAML (Предварительная версия) 

**Тип:** Новая функция  
**Категория службы:** корпоративные приложения  
**Возможности продукта:** Единый вход

Теперь можно настроить любого поддерживаемого приложения SAML, для получения зашифрованных маркеров SAML. Если настроен и используется с приложением, Azure AD шифрует порожденную проверочные утверждения SAML, с помощью открытого ключа, полученный из сертификата, хранящегося в Azure AD.

Дополнительные сведения о настройке шифрования маркера SAML, см. в разделе [шифрования маркеров SAML Azure AD настроить](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Создать проверку доступа для групп или приложений с помощью проверок доступа Azure AD

**Тип:** Новая функция  
**Категория службы.** Проверки доступа  
**Возможности продукта.** Система управления

Теперь можно включить несколько групп или проверку членства в группе или назначение приложения доступа приложений в одном экземпляре Azure AD. Проверки доступа с несколькими группами или приложений настраиваются с теми же параметрами, и все включенные рецензенты уведомляются в то же время.

Дополнительные сведения о том, как создать проверку доступа, с помощью проверок доступа Azure AD, см. в разделе [Создание проверки доступа групп или приложений при проверке доступа Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Новые федеративные приложения доступны в коллекции приложений Azure AD - февраля 2019 г.

**Тип:** Новая функция  
**Категория службы:** корпоративные приложения  
**Возможности продукта:** интеграция сторонних продуктов
 
В февраля 2019 г. мы добавили поддержку этих 27 приложений с федерацией в коллекцию приложений:

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [FAT ПАЛЕЦ](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [ IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Щелкните разрешение [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [сейсмические ](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [Мечты общего ресурса](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [webMethods облачной интеграции](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [LMS знаний повсеместного](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [Campus Подразделение](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [данных Periscope](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [портала Netop](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud по Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp платформы для повышения производительности](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Улучшенные объединенный регистрации MFA или SSPR

**Тип:** Измененная функция  
**Категория службы:** Самостоятельный сброс пароля  
**Возможности продукта:** Аутентификация пользователей
 
В ответ на отзывы клиентов мы усовершенствовали объединенный MFA или SSPR регистрации предварительную версию, помогая пользователям быстро регистрировать данные безопасности для многофакторной проверки Подлинности и SSPR. 

**Чтобы включить дополнительные возможности для пользователей уже сегодня, выполните следующие действия.**

1. Как глобальный администратор или администратор пользователей, войдите на портал Azure и перейдите к **Azure Active Directory > пользовательские параметры > Управление параметрами для компонентов предварительной версии панель доступа**. 

2. В **пользователей, которые можно использовать предварительную версию функции для регистрации и управления ими сведения о безопасности — обновление** выберите для включения функций для **выбранную группу пользователей** или **всех пользователей** .

Над через несколько недель мы будем удаление возможность включения на старый объединенный MFA или SSPR регистрации предварительную версию для клиентов, которые еще не сделали это включено.

**Чтобы увидеть, если элемент управления будет удален для вашего клиента, выполните следующие действия.**

1. Как глобальный администратор или администратор пользователей, войдите на портал Azure и перейдите к **Azure Active Directory > пользовательские параметры > Управление параметрами для компонентов предварительной версии панель доступа**.  

2. Если **пользователей, которые можно использовать функции предварительной версии для регистрации и управления ими сведения о безопасности** параметру присваивается **None**, параметр будет удален из вашего клиента.

Независимо от того, ли вы ранее включили старый объединенный регистрации MFA или SSPR предварительного просмотра для пользователей, или нет, старый интерфейс будет отключена в будущем. Поэтому настоятельно рекомендуется перейти на новый, расширенный интерфейс как можно скорее.

Дополнительные сведения об интерфейсе улучшенные регистрации см. в разделе ["холодного" улучшения в Azure AD в сочетании многофакторной проверки Подлинности и регистрации сброса пароля](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Обновленная политика интерфейс управления для маршруты пользователей

**Тип:** Измененная функция  
**Категория службы:** B2C — управление удостоверениями потребителей  
**Возможности продукта.** B2B или B2C

Мы обновили процесс создания и управления ими политики для маршруты пользователей (ранее известной как, встроенные политики) проще. Эта возможность сейчас доступна по умолчанию для всех ваших клиентов Azure AD.

Можно предоставить дополнительные отзывы и предложения с помощью одобрения или неодобрения значки в **отправьте нам отзыв** область в верхней части окна портала.

Дополнительные сведения о новых возможностях управления политики см. в разделе [настройки JavaScript и многие другие возможности Azure AD B2C теперь имеет](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) блога.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Выберите элемент определенную страницу, доступные в Azure AD B2C

**Тип:** Новая функция  
**Категория службы.** B2C — управление удостоверениями потребителей  
**Возможности продукта.** B2B или B2C

Теперь можно выбрать определенную версию элементов страницы, предоставляемые Azure AD B2C. Выбрав конкретную версию, вы можете проверить обновления, прежде чем они отображаются на странице и могут вести себя предсказуемо. Кроме того вы теперь можете применять версий определенную страницу, чтобы разрешить JavaScript настроек. Чтобы включить эту функцию, перейдите к статье **свойства** страницы в своих последовательностях пользователя.

Дополнительные сведения о выборе конкретных версий элементов страницы см. в разделе [настройки JavaScript и многие другие возможности Azure AD B2C теперь имеет](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) блога.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Требования к паролю можно настроить конечных пользователей для B2C (GA)

**Тип:** Новая функция  
**Категория службы.** B2C — управление удостоверениями потребителей  
**Возможности продукта.** B2B или B2C

Теперь можно настроить сложность пароля вашей организации для конечных пользователей, вместо использования вашего собственного политика паролей Azure AD. Из **свойства** колонка пользователя потоков (ранее известный как встроенные политики), вы можете выбрать сложность пароля **простой** или **строгих**, или вы можете Создание **Custom** набор требований.

Дополнительные сведения о конфигурации требование сложности пароля, см. в разделе [настроить требования к сложности паролей в Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Новые шаблоны по умолчанию для работы в пользовательской фирменной символикой проверки подлинности

**Тип:** Новая функция  
**Категория службы.** B2C — управление удостоверениями потребителей  
**Возможности продукта.** B2B или B2C

Можно использовать наши новые шаблоны по умолчанию, расположенных на **макеты страниц** колонке потоков пользователей (ранее известный как встроенные политики), можно создавать пользовательские типизированной процесс проверки подлинности для пользователей.

Дополнительные сведения об использовании шаблонов см. в разделе [настройки JavaScript и многие другие возможности Azure AD B2C теперь имеет](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Январь 2019 г.

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Совместная работа в службе Active Directory B2B с использованием аутентификации на основе одноразового секретного кода (общедоступная предварительная версия)

**Тип:** Новая функция  
**Категория службы:** B2B  
**Возможности продукта:** B2B или B2C

Мы реализовали функцию аутентификации на основе одноразового секретного кода (OTP) для гостевых пользователей B2B, которые не могут пройти аутентификацию с помощью других средств, таких как Azure AD, учетная запись Майкрософт (MSA) или федерация с Google. Этот новый метод аутентификации означает, что гостевым пользователям не нужно создавать новую учетную запись Майкрософт. Вместо этого при активации приглашения или доступе к общему ресурсу гостевой пользователь может запросить временный код, который будет отправлен на его адрес электронной почты. Гостевой пользователь может войти, используя этот временный код.

Дополнительные сведения см. в статье [Проверка подлинности с отправкой одноразового секретного кода по почте (предварительная версия)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) и в записи блога [Azure AD makes sharing and collaboration seamless for any user with any account](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949) (Azure AD упрощает совместную работу для любого пользователя с любой учетной записью).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Новые параметры файлов cookie Azure Active Directory Application Proxy

**Тип:** Новая функция  
**Категория службы:** Прокси приложения  
**Возможности продукта:** Контроль доступа

Добавлено три новых параметра файлов cookie, доступных для приложений, которые опубликованы с помощью Application Proxy.

- **Использовать cookie-файлы HTTP-Only.** Устанавливает флаг **HTTPOnly** для файлов cookie доступа и сеанса на Application Proxy. Применение этого параметра обеспечивает дополнительные преимущества безопасности, например, помогает предотвратить копирование или изменение файлов cookie с помощью написания скриптов на стороне клиента. Рекомендуется включить этот флаг (выберите **Да**) для дополнительных преимуществ.

- **Использовать безопасный файл cookie.** Устанавливает флаг **Безопасный** для файлов cookie доступа и сеанса в прокси приложении. Применение этого параметра обеспечивает дополнительные преимущества безопасности, обеспечивая передачу файлов cookie только по безопасным каналам TLS, например HTTPS. Рекомендуется включить этот флаг (выберите **Да**) для дополнительных преимуществ.

- **Сохранять файлы сookie.** Предотвращает файлы cookie доступа от истечения срока действия, когда веб-браузер закрыт. Эти файлы cookie действуют в течение времени существования маркера доступа. Тем не менее файлы cookie сбрасываются при достижении времени истечения срока действия, или если пользователь вручную удаляет файл cookie. Мы рекомендуем оставить по умолчанию параметр **Нет** и применять его только для более старых приложений, которые не отправляют файлы cookie между процессами.

Дополнительные сведения о новых файлах cookie см. в разделе [Параметры файлов cookie для доступа к локальным приложениям в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Новые федеративные приложения доступные в коллекции приложений Azure AD — январь 2019

**Тип:** Новая функция  
**Категория службы:** корпоративные приложения  
**Возможности продукта:** интеграция сторонних продуктов
 
В январе 2019 года мы добавили 35 новых приложений с поддержкой федерации в коллекцию приложений:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [палитры Talent](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [обобщающий Cisco](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Администратор доступа Internet](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [напоминания](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [просмотра InstaVR](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [глагол](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [закройте Pavaso Digital](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [ Облачные службы PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [Workable](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [Системы единого входа GTNexus](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [ARES for Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 для Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [Visitly](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [ALP переносящий Korn](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas платформы](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Новые улучшения защиты идентификации Azure AD (общедоступная предварительная версия)

**Тип:** Измененная функция  
**Категория службы:** Защита идентификации  
**Возможности продукта.** безопасность и защита идентификации

Рады сообщить, что мы добавили следующие усовершенствования для общедоступной предварительной версии Защиты идентификации Azure AD, включая:

- обновленный и более интегрированный пользовательский интерфейс;

- дополнительные программные интерфейсы;

- улучшенная оценка риска с помощью машинного обучения;

- выравнивание продукта для пользователей, представляющих риск и рискованные входы в систему.

Дополнительные сведения об усовершенствованиях см. статью [Что такое Защита идентификации Azure Active Directory (обновленная)?](https://aka.ms/IdentityProtectionDocs), чтобы узнать больше и поделиться своими мыслями с помощью запросов внутри продукта.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Новая функция блокировки приложения для приложения Microsoft Authenticator на устройствах iOS и Android

**Тип:** Новая функция  
**Категория службы:** Приложение Microsoft Authenticator  
**Возможности продукта:** безопасность и защита идентификации

Чтобы обеспечить большую безопасность ваших одноразовых секретных кодов, информации о приложении и параметров приложения, вы можете включить функцию "Блокировка приложения" в приложении Microsoft Authenticator. Если включить функцию "Блокировка приложения", то каждый раз, когда вы открываете приложение Microsoft Authenticator, вам будет предлагаться использовать PIN-код или биометрические данные для прохождения аутентификации.

Дополнительные сведения можно найти в статье [Часто задаваемые вопросы о приложении Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Улучшенные возможности экспорта Azure AD Privileged Identity Management

**Тип:** Новая функция  
**Категория службы:** Управление привилегированными пользователями (PIM)  
**Возможности продукта:** Управление привилегированными пользователями (PIM)

Администраторы Azure AD Privileged Identity Management теперь могут экспортировать все активные и допустимые назначения ролей для определенного ресурса, включая назначения ролей для всех дочерних ресурсов. Ранее для администраторов возникали трудности при получении полного списка назначения ролей для подписки, и им было необходимо экспортировать назначения ролей для каждого конкретного ресурса.

Дополнительные сведения см. в статье [Просмотр пользователей с ролями службы Azure в PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="novemberdecember-2018"></a>Ноябрь и декабрь 2018 г.

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Пользователи, удаленные из области синхронизации, больше не преобразуются в облачные учетные записи

**Тип:** исправление  
**Категория службы:** Управление пользователями  
**Возможности продукта:** Каталог

>[!Important]
>Мы знаем и понимаем, как вы беспокоитесь на счет этого исправления. Поэтому мы откатили это изменение, пока не сможем упростить реализацию исправления в вашей организации.

Мы исправили ошибку, из-за которой флаг DirSyncEnabled пользователя ошибочно переключался в значение **False**, когда объект доменных служб Active Directory (AD DS) исключался из области синхронизации и затем перемещался в корзину в Azure AD в следующем цикле синхронизации. В результате этого исправления, если пользователь исключается из области синхронизации, а затем восстанавливается из корзины Azure AD, учетная запись пользователя остается синхронизированной из локальной службы AD (как и ожидается) и ею нельзя управлять в облаке, так как ее главным источником (SoA) по-прежнему остается локальная служба AD.

До этого исправления возникала проблема, когда флаг DirSyncEnabled переключался в значение False. Из-за этого создавалось ошибочное впечатление, что такие учетные записи были преобразованы в облачные объекты, которыми можно управлять в облаке. При этом главным источником учетных записей по-прежнему оставалась локальная среда и все синхронизированные свойства (теневые атрибуты) поступали из локальной службы AD. Это условие вызывало несколько проблем в Azure AD и других облачных рабочих нагрузках (таких как Exchange Online), которые должны были обрабатывать эти учетные записи как синхронизированные из AD, хотя их поведение соответствовало облачным учетным записям.

В настоящее время единственным способом по-настоящему преобразовать учетную запись, синхронизированную из AD, в облачную учетную запись является отключение DirSync на уровне клиента, что запускает внутреннюю операцию по переносу главного источника (SoA). Такое изменение SoA требует (в том числе) очистки всех атрибутов, связанных с локальной средой (таких как LastDirSyncTime и теневые атрибуты), и отправки в другие облачные рабочие нагрузки сигнала о преобразовании соответствующего объекта в облачную учетную запись.

Соответственно, это исправление предотвращает прямое изменение атрибута ImmutableID пользователя, синхронизированного из AD, что требовалось в некоторых сценариях в прошлом. По сути атрибут ImmutableID объекта в Azure AD, как и предполагает название, должен быть неизменяемым. Для разрешения таких ситуаций доступны новые функции, реализованные в Azure AD Connect Health и клиенте службы синхронизации Azure AD Connect.

- **Поэтапное крупномасштабное изменение ImmutableID для нескольких пользователей**
  
  Например, вам необходимо выполнить длительную миграцию между лесами AD DS. Решение. Используйте Azure AD Connect для **настройки привязки к источнику** и по мере миграции пользователей копируйте существующие значения ImmutableID из Azure AD в атрибут ms-DS-Consistency-Guid локального пользователя AD DS в новом лесу. Дополнительные сведения см. в статье, посвященной [использованию ms-DS-ConsistencyGuid в качестве sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Крупномасштабное изменение ImmutableID для нескольких пользователей за один раз**

  Например, при реализации Azure AD Connect вы сделали ошибку и теперь необходимо изменить атрибут SourceAnchor. Решение. Отключите DirSync на уровне клиента и очистите все недопустимые значения ImmutableID. Дополнительные сведения см. в статье, посвященной [отключению синхронизации каталогов для Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Повторное сопоставление локального пользователя с существующим пользователем в Azure AD**. Например, повторное создание пользователя AD DS приводит к появлению дубликата учетной записи Azure AD вместо его повторного сопоставления с существующей учетной записью Azure AD (потерянный объект). Решение. Используйте Azure AD Connect Health на портале Azure для повторного сопоставления источника привязки или атрибута ImmutableID. Дополнительные сведения см. в статье [Сценарий с потерянным объектом](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Критическое изменение: обновления схемы журналов аудита и входа в систему для Azure Monitor

**Тип:** Измененная функция  
**Категория службы:** Отчеты  
**Возможности продукта:** мониторинг и создание отчетов

Сейчас мы публикуем потоки журналов аудита и входа в систему с помощью Azure Monitor, поэтому файлы журналов можно легко интегрировать с используемыми инструментами SIEM или Log Analytics. Исходя из отзывов пользователей, а также в рамках подготовки к выпуску общедоступной версии этой функции, мы вносим в схему следующие изменения. Эти изменения будут внесены в первую неделю января. Тогда же будет обновлена сопутствующая документация.

#### <a name="new-fields-in-the-audit-schema"></a>Новые поля в схеме аудита
Мы добавили новое поле **Operation Type** (Тип операции) для указания типа операции, выполняемой с ресурсом. Например, это могут быть операции **Add** (Добавление), **Update** (Обновление) или **Delete** (Удаление).

#### <a name="changed-fields-in-the-audit-schema"></a>Измененные поля в схеме аудита
Следующие поля в схеме аудита будут изменены.

|Имя поля|Изменения|Прежние значения|Новые значения|
|----------|------------|----------|----------|
|Category|Это было поле **Service Name** (Имя службы). Теперь оно называется **Audit Categories** (Категории аудита). Поле **Service Name** (Имя службы) было переименовано в **loggedByService**.|<ul><li>"Account Provisioning" (Подготовка учетных записей).</li><li>"Core Directory" (Основной каталог);</li><li>"Self-service Password Reset" (Самостоятельный сброс пароля);</li></ul>|<ul><li>Управление пользователями</li><li>Управление группами</li><li>"App Management" (Управление приложениями).</li></ul>|
|targetResources|Включает в себя **TargetResourceType** на верхнем уровне.|&nbsp;|<ul><li>Политика</li><li>Приложение</li><li>Пользователь</li><li>Группа</li></ul>|
|loggedByService|Указывает имя службы, создавшей журнала аудита.|Null|<ul><li>"Account Provisioning" (Подготовка учетных записей).</li><li>"Core Directory" (Основной каталог);</li><li>Самостоятельный сброс пароля</li></ul>|
|Результат|Содержит результат журналов аудита. Ранее это значение перечислялось, но теперь отображается фактическое значение.|<ul><li>0</li><li>1</li></ul>|<ul><li>Успешно</li><li>Сбой</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Измененные поля в схеме входа в систему
Следующие поля в схеме входа в систему будут изменены.

|Имя поля|Изменения|Прежние значения|Новые значения|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Ранее это поле называлось **conditionalaccessPolicies**. Теперь оно называется **conditionalaccessPolicies**.|Без изменения.|Без изменения.|
|conditionalAccessStatus|Содержит результат состояния политики условного доступа при входе в систему. Ранее это значение перечислялось, но теперь отображается фактическое значение.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Успешно</li><li>Сбой</li><li>Неприменимо</li><li>Отключено</li></ul>|
|appliedConditionalAccessPolicies: result|Содержит результат состояния отдельной политики условного доступа при входе в систему. Ранее это значение перечислялось, но теперь отображается фактическое значение.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Успешно</li><li>Сбой</li><li>Неприменимо</li><li>Отключено</li></ul>|

Дополнительные сведения о схеме см. в разделе [Интерпретация схемы журналов аудита Azure AD в Azure Monitor (предварительная версия)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema).

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Улучшения защиты идентификации для контролируемой модели машинного обучения и подсистемы оценки риска

**Тип:** Измененная функция  
**Категория службы:** Защита идентификации  
**Возможности продукта:** Оценка рисков

Улучшения подсистемы оценки риска пользователя и риска при входе, связанные с защитой идентификации, могут повысить точность и охват при оценке риска пользователя. Администраторы могут заметить, что уровень риска пользователя больше не связан напрямую с уровнем риска определенных случаев обнаружения, а число и уровень рискованных событий входа в систему возросли.

Теперь обнаруженные риски оцениваются посредством контролируемой модели машинного обучения, которая вычисляет риск пользователя с помощью дополнительных характеристик входа пользователя в систему и шаблона обнаружения. На основании этой модели администратор может найти пользователей с высоким уровнем риска, даже если уровень риска для случаев обнаружения, связанных с этими пользователями, не превышает низкий или средний уровень. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Администраторы могут сбрасывать свой пароль с помощью приложения Microsoft Authenticator (предварительная версия)

**Тип:** Измененная функция  
**Категория службы:** Самостоятельный сброс пароля  
**Возможности продукта:** Аутентификация пользователей

Администраторы Azure AD теперь могут сбросить свой пароль с помощью уведомлений приложения Microsoft Authenticator, кода из любого мобильного приложения Authenticator или аппаратного токена. Чтобы сбросить свой пароль, администраторы теперь могут использовать два из следующих методов:

- уведомление приложения Microsoft Authenticator;

- другое мобильное приложение Authenticator или код аппаратного токена;

- Email

- Телефонный звонок

- Текстовое сообщение

Дополнительные сведения об использовании приложения Microsoft Authenticator для сброса паролей см. в разделе [Мобильное приложение и SSPR (предварительная версия)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr-preview).

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Новая роль администратора облачных устройств в Azure AD (предварительная версия)

**Тип:** Новая функция  
**Категория службы:** регистрация устройств и управление ими  
**Возможности продукта:** управление доступом;

Администраторы могут назначать пользователям новую роль "Администратор облачных устройств" для выполнения операций администрирования облачных устройств. Пользователи с этой ролью могут включать, отключать и удалять устройства в Azure AD, а также считывать ключи BitLocker в Windows 10 (при наличии) на портале Azure.

Дополнительные сведения о ролях и разрешениях см. в статье [Назначение ролей администратора в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Управление устройствами с помощью новой метки времени действия в Azure AD (предварительная версия)

**Тип:** Новая функция  
**Категория службы:** регистрация устройств и управление ими  
**Возможности продукта:** управление жизненным циклом устройств

Мы понимаем, что со временем необходимо обновить и снятие с учета устройств вашей организации в Azure AD, чтобы избежать устаревших устройств в вашей среде. Чтобы упростить этот процесс, теперь служба Azure AD обновляет устройства с помощью новой метки времени действия, помогая управлять жизненным циклом устройств.

Дополнительные сведения о том, как получить и использовать эту метку времени, см. в разделе [Практическое руководство. Управление устаревшими устройствами в Azure AD](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices).

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Администраторы могут потребовать от пользователей принять условия использования на каждом устройстве

**Тип:** Новая функция  
**Категория службы:** Условия использования  
**Возможности продукта:** Система управления
 
Администраторы теперь могут включить параметр **Требовать согласие пользователей для каждого устройства**, чтобы требовать от ваших пользователей принять условия использования на каждом устройстве, которое они используют с клиентом.

Дополнительные сведения см. в разделе [Функция "Условия использования Azure Active Directory"](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Администраторы могут настроить срок действия условий использования согласно расписанию с периодическим повторением

**Тип:** Новая функция  
**Категория службы:** Условия использования  
**Возможности продукта:** Система управления
 

Администраторы теперь могут включить параметр **Истечение срока действия согласия**, чтобы установить срок действия условий использования для всех пользователей на основе указанного расписания с периодическим повторением. Расписание может повторяться один раз в год, два раза в год, ежеквартально или ежемесячно. После истечения срока действия условия использования, пользователи должны повторно приняли.

Дополнительные сведения см. в разделе [Добавление условий использования](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Администраторы могут настроить срок действия условий использования согласно расписанию каждого пользователя

**Тип:** Новая функция  
**Категория службы:** Условия использования  
**Возможности продукта:** Система управления

Администраторы теперь могут указать срок, по истечении которого пользователь должен повторно принять условия использования. Например, администраторы могут указать, что пользователи должны повторно принимать условия использования каждые 90 дней.

Дополнительные сведения см. в разделе [Добавление условий использования](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Новые электронные сообщения Azure AD Privileged Identity Management (PIM) для ролей Azure Active Directory

**Тип:** Новая функция  
**Категория службы:** Управление привилегированными пользователями (PIM)  
**Возможности продукта:** Управление привилегированными пользователями (PIM)
 
Клиенты, использующие Azure AD Privileged Identity Management (PIM), теперь могут получать еженедельные дайджесты по электронной почте, включая приведенные ниже сведения за последние семь дней:

- общие сведения о наиболее используемых разрешенных и постоянных назначений ролей;

- число пользователей, активирующих роли;

- число пользователей, назначенных для ролей в PIM;

- число пользователей, назначенных для ролей вне PIM;

- число пользователей с постоянными ролями в PIM.

Дополнительные сведения о PIM и доступных уведомлениях по электронной почте см. в разделе [Уведомления по электронной почте в PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications).

---

### <a name="group-based-licensing-is-now-generally-available"></a>Выпущена общедоступная версия группового лицензирования

**Тип:** Измененная функция  
**Категория службы:** Другие  
**Возможности продукта:** Каталог

Период предоставления общедоступной предварительной версии группового лицензирования завершен, и теперь выпущена общедоступная версия этой функции. В рамках данного общедоступного выпуска мы сделали эту функцию более масштабируемой. Кроме того, мы добавили возможность повторной обработки назначений группового лицензирования для отдельного пользователя и возможность использовать групповое лицензирование с лицензиями E3 или A3 в Office 365.

Дополнительные сведения о групповом лицензировании см. в разделе [Что такое лицензии групп в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>В коллекции приложений Azure AD доступны новые федеративные приложения (ноябрь 2018 г.)

**Тип:** Новая функция  
**Категория службы:** корпоративные приложения  
**Возможности продукта:** интеграция сторонних продуктов
 
В ноябре 2018 года мы добавили в коллекцию приложений следующие 26 новых приложений с поддержкой федерации:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [Gra-Pe](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://www.drivedollar.com/Account/Login), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [Infinite Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [Drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [Everbridge Member Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [Plex Apps - Classic Test](https://test.plexonline.com/signon), [Plex Apps – Classic](https://www.plexonline.com/signon), [Plex Apps - UX Test](https://test.cloud.plex.com/sso), [Plex Apps – UX](https://cloud.plex.com/sso), [Plex Apps – IAM](https://accounts.plex.com/), [CRAFTS - Childcare Records, Attendance, & Financial Tracking System](https://getcrafts.ca/craftsregistration). 

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

## <a name="october-2018"></a>Октябрь 2018 г.

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Возможность работать с журналами Azure AD в Azure Log Analytics (предварительная версия)

**Тип:** Новая функция  
**Категория службы:** Отчеты  
**Возможности продукта:** мониторинг и создание отчетов

Мы рады сообщить, что теперь вы можете пересылать журналы Azure AD в Azure Log Analytics! Эта функция, на которую поступило больше всего запросов, даст вам еще более удобный доступ к данным аналитики для бизнеса, операций и безопасности, а также поможет в мониторинге инфраструктуры. Дополнительные сведения см. в записи блога [Azure Active Directory Activity logs in Azure Log Analytics now available](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) (В Azure Log Analytics теперь доступны журналы активности Azure Active Directory).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>В коллекции приложений Azure AD доступны новые федеративные приложения (октябрь 2018 г.)

**Тип:** Новая функция  
**Категория службы:** корпоративные приложения  
**Возможности продукта:** интеграция сторонних продуктов
 
В октябре 2018 года мы добавили в коллекцию приложений 14 новых приложений с поддержкой федерации:

[My Award Points](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Dialpad, [ON24 Virtual Environment](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscaler Three](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Workspot Control](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial).

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Уведомления по электронной почте для доменных служб Azure AD

**Тип:** Новая функция  
**Категория службы:** Доменные службы Azure AD  
**Возможности продукта:** Доменные службы Azure AD

Доменные службы Azure AD отображают на портале Azure оповещения об ошибках в настройках или проблемах с управляемым доменом. Эти оповещения содержат пошаговые инструкции, с помощью которых можно попытаться устранить проблемы без обращения в службу поддержки.

Начиная с октября вы сможете настраивать параметры уведомлений для управляемого домена, чтобы при каждом новом оповещении отправлялось сообщение электронной почты назначенной группе пользователей. Таким образом, им не придется постоянно проверять обновления информации на портале.

Дополнительные сведения см. в статье [Параметры уведомлений в доменных службах Azure AD](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Портал Azure AD поддерживает использование API управления доменами ForceDelete для удаления пользовательских доменов 

**Тип:** Измененная функция  
**Категория службы:** Управление каталогом  
**Возможности продукта:** Каталог

Мы рады сообщить, что теперь с помощью API управления доменами ForceDelete можно удалить имя личного домена и асинхронно изменить такое имя (contoso.com) в ссылках на пользователей, группы и приложения на имя домена по умолчанию (contoso.onmicrosoft.com).

Такое изменение позволяет быстрее удалить имя личного домена, которое ваша организация больше не использует или намерена перенести в другую службу Azure AD.

Дополнительные сведения см. в статье [Удаление имени личного домена](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---
