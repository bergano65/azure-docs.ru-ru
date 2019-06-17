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
ms.date: 05/23/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f57b0189708f48d1dc403ce778f5f9693b6dc702
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67110382"
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

## <a name="may-2019"></a>Май 2019 г.

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Изменение службы: Дальнейшей поддержке только протоколы TLS 1.2 в службе прокси приложения

**Тип:** план изменений  
**Категория службы:** Прокси приложения  
**Возможности продукта:** Контроль доступа

Для предоставления лучших в своем классе шифрование для наших клиентов, мы ограничиваем доступ к только протоколы TLS 1.2 в службе прокси приложения. Это изменение будет постепенно развертываться для клиентов, которые уже используются только протоколы TLS 1.2, поэтому не должны видеть все изменения.

Об устаревании TLS 1.0 и TLS 1.1 происходит при 31 августа 2019 г., но мы предоставим дополнительные подаваться таким образом вы получаете времени на подготовку это изменение. Для подготовки этого изменения, убедитесь, что сочетания между клиентом и сервером и браузером и сервером, включая любые клиенты, которые пользователи используют для доступа к приложения, опубликованные через прокси приложения, обновляются для использования протокола TLS 1.2 для поддержки подключения к приложению Служба прокси-сервера. Дополнительные сведения см. в разделе [добавления локального приложения для удаленного доступа через прокси приложения в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Отчет об использовании и служит для просмотра связанных с приложениями вход данных

**Тип:** Новая функция  
**Категория службы:** корпоративные приложения  
**Возможности продукта:** мониторинг и создание отчетов

Теперь вы можете использовать отчет об использовании и, расположенный в **корпоративные приложения** области на портале Azure, для получения на уровне приложения представления данных входа в систему, включая сведения о:

- Предложение TOP используется приложениями для вашей организации

- Приложения с количеством неудачных входов в систему

- Самые распространенные ошибки входа в систему, для каждого приложения

Дополнительные сведения об этой функции см. в разделе [отчет об использовании и на портале Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Автоматизация подготовки к облачным приложениям с помощью Azure AD пользователей

**Тип:** Новая функция  
**Категория службы:** корпоративные приложения  
**Возможности продукта:** мониторинг и создание отчетов

Выполните эти новые руководства по использованию служба подготовки Azure AD для автоматизации создания, удаления и обновления учетных записей пользователей для облачных приложений:

- [Comeet](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

Вы также можете выполнить это новое [Dropbox руководстве](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), который предоставляет сведения о том, как подготавливать объектов групповой.

Дополнительные сведения о том, как улучшить защиту вашей организации с помощью автоматической подготовке учетных записей см. в разделе [автоматизировать подготовку пользователей для приложений SaaS в Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Оценка безопасности IDENTITY теперь доступна в Azure AD (общедоступная)

**Тип:** Новая функция  
**Категория службы:** Н/Д  
**Возможности продукта:** безопасность и защита идентификации

Теперь вы можете отслеживать и повысить вашу личность уровень защищенности, используя удостоверение защита оценка функции в Azure AD. Идентификатор защиты использует функцию оценки одной панели мониторинга помогут вам:

- Объективная мер обеспечения безопасности удостоверений на основе оценки от 1 до 223.

- Планирование улучшения в области безопасности для удостоверений

- Проверьте успешность улучшений безопасности

Дополнительные сведения о функции оценки системы безопасности удостоверения, см. в разделе [что такое Оценка безопасности удостоверений в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Новый интерфейс регистрации приложения становится доступен (общедоступная)

**Тип:** Новая функция  
**Категория службы.** аутентификация (вход)  
**Возможности продукта:** Опыт разработчика

Новый [регистрация приложений](https://aka.ms/appregistrations) возможности теперь предоставляются в общедоступной версии. Этот новый интерфейс включает в себя все ключевые функции должны быть знакомы с портала Azure и на портале регистрации приложения и улучшает их с помощью:

- **Более эффективного управления приложения.** Вместо ожидаем увидеть ваши приложения на разных порталах, вы увидите все приложения в одном месте.

- **Упрощенное регистрации приложения.** Интерфейс выбора переработанной разрешение опыту Улучшенная Навигация стала проще для регистрации приложений и управления ими.

- **Более подробные сведения.** Вы найдете дополнительные сведения о приложении, включая краткие руководства и многое другое.

Дополнительные сведения см. в разделе [платформы удостоверений Microsoft](https://docs.microsoft.com/azure/active-directory/develop/) и [работа регистраций приложений теперь общедоступна!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) Объявление о блоге.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Новые возможности, доступные в API рискованные пользователи для защиты идентификации

**Тип:** Новая функция  
**Категория службы:** Защита идентификации  
**Возможности продукта.** безопасность и защита идентификации

Мы рады сообщить, что теперь можно использовать API рискованные пользователи для получения журнала риска пользователей, закрыть пользователей, представляющих риск и для подтверждения пользователей скомпрометирована. Это изменение позволяет более эффективно обновлять состояние риска пользователей и информация об истории их риска.

Дополнительные сведения см. в разделе [справочную документацию по API рискованные пользователи](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Новые федеративные приложения доступны в коллекции приложений Azure AD - мая 2019 г.

**Тип:** Новая функция  
**Категория службы:** корпоративные приложения  
**Возможности продукта:** интеграция сторонних продуктов

В мая 2019 г. мы добавили поддержку этих 21 новые приложения с федерацией в коллекцию приложений:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [реальных связывает](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [простого входа](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [Displayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [привлечь Marketo Sales](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [OutSystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [Meta4 глобального HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [Workplace такта](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [кобальтом](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [веб-методы API Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Управления](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Улучшенные группы Создание и управление ими возможности портала Azure AD

**Тип:** Новая функция  
**Категория службы.** Управление группами  
**Возможности продукта:** Совместная работа

Мы внесли улучшения, связанные с группами компоненты для работы с портала Azure AD. Эти улучшения позволяют администраторам лучше управлять группы списков, элементов списков и укажите параметры создания дополнительных.

Добавлены некоторые улучшения, среди которых:

- Простая фильтрация по типу членства и тип группы.

- Добавление новых столбцов, такие как адрес источника и адрес электронной почты.

- Возможность множественного выбора групп, членов и владелец перечислены для простоты последующего их удаления.

- Возможность выбрать адрес электронной почты и добавление владельцев во время создания группы.

Дополнительные сведения см. в разделе [создания базовой группы и добавлять участников, с помощью Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Настройка политики именования групп Office 365 на портале Azure AD (общедоступная)

**Тип:** Измененная функция  
**Категория службы:** Управление группами  
**Возможности продукта:** Совместная работа

Администраторы теперь могут настроить политики именования групп Office 365, с помощью портала Azure AD. Это изменение позволяет применить соответствующие соглашения об именовании групп Office 365 созданных или измененных пользователей в вашей организации.

Политика именования групп Office 365 можно настроить двумя способами:

- Определите префиксы или суффиксы, которые автоматически добавляются имя группы.

- Загрузите настраиваемый набор запрещенных слов для вашей организации, которые не допускаются в именах группы (например, «ГЕНДИР, Зарплата, Отдел_кадров»).

Дополнительные сведения см. в разделе [принудительно применять политику именования групп Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Конечные точки Microsoft Graph API сейчас доступны для журналов действий Azure AD (общедоступная)

**Тип:** Измененная функция  
**Категория службы:** Отчеты  
**Возможности продукта:** мониторинг и создание отчетов

Мы рады общедоступная версия поддержки конечные точки Microsoft Graph API для Azure AD действия журналы. В этом выпуске теперь можно аудита Azure AD версии 1.0 используйте журналы, а также журналы входа API-интерфейсы.

Дополнительные сведения см. в разделе [Обзор журнала API аудита Azure AD](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Администраторы теперь могут использовать условного доступа для процесса регистрации объединенный (Предварительная версия)

**Тип:** Новая функция  
**Категория службы.** условный доступ;  
**Возможности продукта.** безопасность и защита идентификации  

Администраторы теперь могут создавать политики условного доступа для использования на странице объединенного регистрации. Это включает в себя применение политики, чтобы разрешить регистрацию, если:

- Пользователи находятся в доверенной сети.

- Пользователи, низким уровнем риска входа в систему.

- Пользователи, на управляемом устройстве.

- Пользователи соглашаетесь с условиями использования Продуктов организации.

Дополнительные сведения об условном доступе и сброса пароля вы увидите [многофакторной проверки Подлинности в сочетании условного доступа для Azure AD и сброс пароля регистрации возможности записи блога](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Дополнительные сведения о политиках условного доступа для процесса регистрации объединенный см. в разделе [политики условного доступа для регистрации объединенный](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration). Дополнительные сведения об условиях Azure AD использовать функцию, см. в разделе [условия возможности Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

## <a name="april-2019"></a>Апрель 2019 г.

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-in-refreshed-azure-ad-identity-protection"></a>Новые обнаружения аналитики угроз Azure AD теперь доступна в обновления Azure AD Identity Protection

**Тип:** Новая функция  
**Категория службы:** защиту идентификации Azure AD  
**Возможности продукта:** безопасность и защита идентификации

Обнаружение аналитики угроз Azure AD теперь доступна в обновления Azure AD Identity Protection. Эти новые возможности помогают для указания действий пользователей, является нестандартным для конкретного пользователя или соответствуют известным шаблонам атак по результатам аналитики внутренних и внешних угроз корпорации Майкрософт.

Дополнительные сведения о обновленная версия защиты идентификации Azure AD, см. в разделе [четыре основных улучшения защиты идентификации Azure AD теперь находятся в общедоступной предварительной версии](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) блога и [что такое Azure Active Directory Защита идентификации (Обновить)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) статья. Дополнительные сведения об обнаружении аналитики угроз Azure AD см. в разделе [событий риска защиты идентификации Azure Active Directory](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence) статьи.

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Управление правами Azure AD стала доступна (Предварительная версия)

**Тип:** Новая функция  
**Категория службы:** Система управления удостоверениями  
**Возможности продукта:** Система управления удостоверениями

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

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [ Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [Benchling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio Подключение](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [подключения Mitel](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba Cloud (единый вход на основе ролей)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent собственных средств управления](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Диспетчера сертификатов Sectigo](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [ SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Новая модель доступа рассматриваются параметр частоты и множественный выбор роли

**Тип:** Новая функция  
**Категория службы.** Проверки доступа  
**Возможности продукта:** Система управления удостоверениями

Новые обновления в Azure AD проверки доступа позволяют:

- Изменение частоты вашего доступа, используя запрос **частично annually**, в дополнение к ранее существующие параметры еженедельно, ежемесячно, ежеквартально и один раз в год.

- Выберите несколько Azure AD и просмотрите ролей ресурсов Azure при создании единого доступа. В этом случае все роли настраиваются с теми же параметрами, и все рецензенты уведомляются в то же время.

Дополнительные сведения о том, как создать проверку доступа см. в разделе [Создание проверки доступа групп или приложений в Azure AD проверок доступа](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Оповещения системы электронной почты Azure AD Connect переходят, отправка нового отправителя электронной почты для некоторых клиентов

**Тип:** Измененная функция  
**Категория службы:** AD Sync  
**Возможности продукта.** платформа

Azure AD Connect находится в процессе перехода наших серверов оповещения по электронной почте, потенциально, показывающий некоторые клиенты нового сообщения электронной почты отправителя. Чтобы решить эту проблему, необходимо добавить `azure-noreply@microsoft.com` в список разрешений в вашей организации или вы не сможете продолжить получать важные оповещения из Office 365, Azure или служб синхронизации.

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

Мы усовершенствовали поддержке Microsoft Edge, включая предоставление новой поддержки для Azure AD единого входа и условного доступа Azure AD. Если вы ранее использовали управляемый браузер Microsoft Intune, теперь можно использовать Microsoft Edge вместо этого.

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

Чтобы администраторы не блокировали случайно себя за пределы их собственными клиентами через неправильной настройкой политики условного доступа, мы создали новые предупреждения и обновленных рекомендаций на портале Azure. Дополнительные сведения о новое руководство, см. в разделе [Каковы зависимости служб в Azure Active Directory условного доступа](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Условия конечного пользователя улучшенные возможности использования на мобильных устройствах

**Тип:** Измененная функция  
**Категория службы:** Условия использования  
**Возможности продукта:** Система управления

Мы обновили наши существующие условия использования опытом, чтобы помочь улучшить способ просмотра и согласие с условиями использования на мобильных устройствах. Теперь можно увеличивать и уменьшать, вернуться назад, сведения о загрузке и выберите гиперссылки. Дополнительные сведения о обновленные условия использования, см. в разделе [условия возможности Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

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

**Чтобы включить на новый удобный интерфейс для пользователей уже сегодня, выполните следующие действия:**

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
|Категория|Это было поле **Service Name** (Имя службы). Теперь оно называется **Audit Categories** (Категории аудита). Поле **Service Name** (Имя службы) было переименовано в **loggedByService**.|<ul><li>"Account Provisioning" (Подготовка учетных записей).</li><li>"Core Directory" (Основной каталог);</li><li>"Self-service Password Reset" (Самостоятельный сброс пароля);</li></ul>|<ul><li>Управление пользователями</li><li>Управление группами</li><li>"App Management" (Управление приложениями).</li></ul>|
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
**Возможности продукта:** Управление доступом

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
**Категория службы.** Условия использования  
**Возможности продукта:** Система управления
 
Администраторы теперь могут включать **пользователям необходимо дать согласие на каждом устройстве** параметр, чтобы указать ваших пользователей принять условия использования для каждого устройства, они используют на клиенте.

Дополнительные сведения см. в разделе [каждого устройства условия использования раздела условий Azure Active Directory используйте функцию](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Администраторы могут настроить условия использования, чтобы хранить согласно повторяющемуся расписанию

**Тип:** Новая функция  
**Категория службы.** Условия использования  
**Возможности продукта:** Система управления
 

Администраторы теперь могут включать **истекает согласие** параметр, определяющий условия использования, срока действия для всех пользователей на основе вашего указанного расписания повторения. Расписание может повторяться один раз в год, два раза в год, ежеквартально или ежемесячно. После истечения срока действия условия использования, пользователи должны повторно приняли.

Дополнительные сведения см. в разделе [Добавление условий использования раздела условий Azure Active Directory используйте функцию](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Администраторы могут настроить условия использования, срок действия на основе расписания для каждого пользователя

**Тип:** Новая функция  
**Категория службы.** Условия использования  
**Возможности продукта:** Система управления

Администраторы теперь могут указать длительность этот пользователь должен повторно приняли условия использования. Например Администраторы могут указать, что пользователи должны повторно приняли условия использования каждые 90 дней.

Дополнительные сведения см. в разделе [Добавление условий использования раздела условий Azure Active Directory используйте функцию](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).
 
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

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [Gra-Pe](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [Infinite Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [Drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [Everbridge Member Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [Plex Apps - Classic Test](https://test.plexonline.com/signon), [Plex Apps – Classic](https://www.plexonline.com/signon), [Plex Apps - UX Test](https://test.cloud.plex.com/sso), [Plex Apps – UX](https://cloud.plex.com/sso), [Plex Apps – IAM](https://accounts.plex.com/), [CRAFTS - Childcare Records, Attendance, & Financial Tracking System](https://getcrafts.ca/craftsregistration). 

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---