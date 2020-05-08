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
ms.date: 04/30/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c58c99faa83d6f04ac37d03789202e111f1b06d
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611166"
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

Административные единицы позволяют предоставлять разрешения администратора, которые ограничены подразделением, регионом или другим определенным вами сегментом вашей организации. Административные единицы можно использовать для делегирования разрешений региональным администраторам или для установки политики на детализированном уровне. Например, администратор учетной записи пользователя может обновлять данные профиля, сбрасывать пароли и назначать лицензии только пользователям в административной единице.

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

**Администратор принтера**. пользователи с этой ролью могут регистрировать принтеры и управлять всеми аспектами всех конфигураций принтеров в универсальном решении печати (Майкрософт), включая параметры соединителя универсального принтера. Они могут дать согласие на все делегированные запросы разрешений на печать. Администраторы принтеров также имеют доступ к отчетам печати. 

**Специалист по печати**. пользователи с этой ролью могут регистрировать принтеры и управлять состоянием принтера в универсальном решении печати (Майкрософт). Они также могут читать все сведения о соединителе. Основные задачи. не удается выполнить типографию. устанавливаются разрешения пользователей на принтерах и совместное использование принтеров. [Подробнее.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Встроенная роль администратора гибридной идентификации

**Тип.** Новая функция.

**Категория службы:** RBAC

**Возможность продукта.** Контроль доступа.

Пользователи с этой ролью могут включать, настраивать и администрировать службы и параметры, связанные с включением гибридной идентификации в Azure AD. Эта роль предоставляет возможность настроить Azure AD для использования одного из трех поддерживаемых методов проверки подлинности&#8212;синхронизации хэшей паролей (PHS), сквозной проверки подлинности (PTA) или Федерации (AD FS или стороннего поставщика Федерации) &#8212;и развертывания связанной локальной инфраструктуры для их включения. Локальная инфраструктура включает в себя подготовку и агенты PTA. Эта роль предоставляет возможность простого единого входа (S-SSO) для обеспечения простой проверки подлинности на устройствах, отличных от Windows 10, или на компьютерах, отличных от Windows Server 2016. Кроме того, эта роль предоставляет возможность просматривать журналы входа и получать доступ к службе работоспособности и аналитики для целей мониторинга и устранения неполадок. [Подробнее.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Встроенная роль администратора сети

**Тип.** Новая функция.

**Категория службы:** RBAC

**Возможность продукта.** Контроль доступа.

Пользователи с этой ролью могут просматривать рекомендации по архитектуре периметра сети от корпорации Майкрософт, основанные на данных телеметрии сети из расположений пользователей. Производительность сети для Office 365 полагается на тщательную архитектуру периметра сети клиентов предприятия, которая обычно зависит от местонахождения пользователя. Эта роль позволяет изменять обнаруженные расположения пользователей и настраивать сетевые параметры для этих расположений, чтобы упростить улучшенные измерения телеметрии и рекомендации по проектированию. [Подробнее.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#network-administrator)

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

Мы рады поделиться тем, что теперь обновленный [Защита идентификации Azure AD](https://aka.ms/IdentityProtectionDocs) интерфейс на [портале Microsoft Azure для государственных организаций](https://portal.azure.us/). Дополнительные сведения см. в записи [блога о объявлениях](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667).

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Аварийное восстановление. Скачайте и сохраните конфигурацию подготовки.

**Тип.** Новая функция.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта:** Управление жизненным циклом удостоверений
 
Служба подготовки Azure AD предоставляет широкий набор возможностей настройки. Клиенты должны иметь возможность сохранить конфигурацию, чтобы они могли ссылаться на них позже, или выполнить откат до известной работоспособной версии. Мы добавили возможность загрузки конфигурации подготовки в виде JSON-файла и передачи ее при необходимости. [Дополнительные сведения](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (самостоятельный сброс пароля) теперь требует двух шлюзов для администраторов в Microsoft Azure, обслуживаемых 21Vianet (Azure Китая 21Vianet) 

**Тип.** Измененная функция.  
**Категория службы:** Самостоятельный сброс пароля  
**Возможности продукта.** Безопасность и защита удостоверений.
 
Ранее в Microsoft Azure, обслуживаемом 21Vianet (Azure Китая 21Vianet), администраторы, использующие самостоятельный сброс пароля (SSPR) для сброса собственных паролей, требуют только одной "шлюза" (запроса) для подтверждения их личности. В общедоступных и других национальных облаках администраторы обычно должны использовать два шлюза для подтверждения их подлинности при использовании SSPR. Но так как мы не поддерживали SMS или телефонные звонки в Azure Китая 21Vianet, мы развернули администраторам один шлюз для сброса паролей.

Мы создаем SSPRую четность функций между Azure Китая 21Vianet и общедоступным облаком. В дальнейшем администраторы должны использовать два шлюза при использовании SSPR. Будут поддерживаться SMS, телефонные звонки и уведомления и коды приложений для проверки подлинности. [Дополнительные сведения](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences).

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

Чтобы предоставить клиентам более гибкий способ создания групп на уровне каталога, которые лучше всего соответствуют своим потребностям, мы заменили параметр **группы уровня каталога** из > раздела**Общие** параметры **в портал Azure**со ссылкой на [документацию по динамической группе](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership). Мы улучшили документацию для включения дополнительных инструкций, чтобы администраторы могли создавать группы всех пользователей, включающие или исключающие гостевые пользователи.

---

## <a name="february-2020"></a>Февраль 2020 г.

### <a name="upcoming-changes-to-custom-controls"></a>Предстоящие изменения в пользовательских элементах управления

**Тип.** Планирование изменений.  
**Категория службы:** MFA  
**Возможности продукта.** Безопасность и защита удостоверений.
 
Мы планируем заменить текущий предварительный просмотр настраиваемых элементов управления на подход, обеспечивающий возможность проверки подлинности, предоставляемой партнером, для беспрепятственной работы с Azure Active Directory администратором и конечными пользователями. Сегодня партнерские решения MFA имеют следующие ограничения: они работают только после ввода пароля. они не служат в качестве MFA для поэтапной проверки подлинности в других ключевых сценариях. и не интегрируются с функциями управления учетными данными пользователя или администратора. Новая реализация позволит использовать предоставленные партнером факторы проверки подлинности вместе со встроенными факторами для ключевых сценариев, включая регистрацию, использование, утверждения MFA, пошаговую проверку подлинности, отчеты и ведение журнала. 

Пользовательские элементы управления по прежнему будут поддерживаться в предварительной версии вместе с новой структурой, пока она не станет общедоступной. На этом этапе мы предоставим клиентам время перехода на новую архитектуру. Из-за ограничений текущего подхода мы не будем подключать новые поставщики до тех пор, пока не станет доступен новый проект. Мы тесно работаем с клиентами и поставщиками и будем связываться с временной шкалой по мере приближения. [Дополнительные сведения](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

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
 
Теперь вы можете автоматизировать создание, обновление и удаление учетных записей пользователей для новых интегрированных приложений:

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud от Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

Дополнительные сведения о том, как лучше защитить организацию с помощью автоматизированной подготовки учетных записей пользователей, см. в статье [Автоматизация подготовки пользователей в приложениях SaaS с помощью Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

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
 
Теперь вы можете автоматизировать создание, обновление и удаление учетных записей пользователей для новых интегрированных приложений:

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

Дополнительные сведения о том, как лучше защитить организацию с помощью автоматизированной подготовки учетных записей пользователей, см. в статье [Автоматизация подготовки пользователей в приложениях SaaS с помощью Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

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

---

## <a name="december-2019"></a>Декабрь 2019 г.

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>Интеграция подготовки SAP SuccessFactors в Azure AD и локальную службу AD (общедоступная Предварительная версия)

**Тип.** Новая функция.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта:** Управление жизненным циклом удостоверений

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
**Возможности продукта:** Безопасность и защита удостоверений

В рамках безопасной модели по умолчанию для проверки подлинности удаляются существующие политики базовой защиты для всех клиентов. Это удаление предназначено для завершения в конце февраля. Замена этих политик базовой защиты по умолчанию имеет значение безопасности. Если вы использовали политики базовой защиты, необходимо запланировать переход на новую политику безопасности по умолчанию или на условный доступ. Если вы не использовали эти политики, вам не нужно предпринимать никаких действий.

Дополнительные сведения о новых значениях по умолчанию для системы безопасности см. в разделе [что такое параметры безопасности по умолчанию?](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) Дополнительные сведения о политиках условного доступа см. в разделе [Общие политики условного доступа](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common).

---

## <a name="november-2019"></a>Ноябрь 2019 г.

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>Поддержка атрибута SameSite и Chrome 80

**Тип.** Планирование изменений.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Проверка подлинности пользователя

В рамках безопасной модели по умолчанию для файлов cookie браузер Chrome 80 изменяет, как он обрабатывает файлы cookie без `SameSite` атрибута. Любой файл cookie, не указывающий `SameSite` атрибут, будет рассматриваться так, как если `SameSite=Lax`бы он был установлен в, что приведет к блокированию Chrome некоторых сценариев совместного использования файлов cookie, которые могут зависеть от приложения. Чтобы сохранить старое поведение Chrome, можно использовать `SameSite=None` атрибут и добавить дополнительный `Secure` атрибут, чтобы межсайтовые файлы cookie могли быть доступны только через HTTPS-соединения. Chrome планирует выполнить это изменение на 4 февраля 2020.

Мы рекомендуем, чтобы наши разработчики протестируют свои приложения с помощью этого руководства:

- Задайте для параметра **использовать защищенный файл cookie** значение **Да**.

- Установите значение атрибута **SameSite** по умолчанию равным **None**.

- Добавьте дополнительный `SameSite` атрибут **Secure**.

Дополнительные сведения см. [в разделе предстоящие изменения файлов cookie SameSite в ASP.NET и ASP.NET Core](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) и [возможном нарушении работы веб-сайтов клиентов и продуктов и служб Майкрософт в Chrome версии 79 и более поздних](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79).

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Новое исправление для Microsoft Identity Manager (MIM) 2016 с пакетом обновления 2 (SP2)

**Тип.** Исправление.  
**Категория службы:** Microsoft Identity Manager  
**Возможности продукта:** Управление жизненным циклом удостоверений

Накопительный пакет исправлений (сборка 4.6.34.0) доступен для Microsoft Identity Manager (MIM) 2016 с пакетом обновления 2 (SP2). Этот пакет исправлений устраняет проблемы и добавляет улучшения, описанные в разделе "исправленные проблемы и улучшения, добавленные в этом обновлении".

Дополнительные сведения и Загрузка пакета исправлений см. в разделе [накопительный пакет обновления 2 для Microsoft Identity Manager 2016 (сборка 4.6.34.0)](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r).

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Новый отчет о действиях AD FS приложений для переноса приложений в Azure AD (общедоступная Предварительная версия)

**Тип.** Новая функция.  
**Категория службы:** Корпоративные приложения  
**Возможности продукта.** Единый вход.

С помощью нового отчета о действиях приложений службы федерации Active Directory (AD FS) (AD FS) в портал Azure, чтобы указать, какие приложения могут быть перенесены в Azure AD. Этот отчет оценивает все AD FS приложения на совместимость с Azure AD, проверяет наличие проблем и предоставляет рекомендации по подготовке отдельных приложений к миграции.

Дополнительные сведения см. в статье [Использование отчета об активности приложений AD FS для переноса приложений в Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity).

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Новый рабочий процесс для запроса согласия администратора (общедоступная Предварительная версия)

**Тип.** Новая функция.  
**Категория службы:** Корпоративные приложения  
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
**Возможности продукта:** Проверка подлинности пользователя

Новая поддержка по использованию идентификаторов Google Social (учетных записей Gmail) в Azure AD упрощает совместную работу пользователей и партнеров. У ваших партнеров больше нет необходимости создавать и администрировать новую учетную запись Майкрософт. Теперь Microsoft Teams полностью поддерживает пользователей Google на всех клиентах, а также общие конечные точки проверки подлинности, связанные с клиентом.

Дополнительные сведения см. в статье [Добавление Google в качестве поставщика удостоверений для гостевых пользователей B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Мобильная поддержка Microsoft погранично для условного доступа и единого входа (общая доступность)

**Тип.** Новая функция.  
**Категория службы:** Условный доступ  
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
**Категория службы:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.  

Теперь вы можете автоматизировать создание, обновление и удаление учетных записей пользователей для новых интегрированных приложений:

[SAP Cloud Platform Identity Authentication Service](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial), [рингцентрал](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial), [SpaceIQ](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial), [Миро](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial), [клаудгате](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial), [Инфо клаудсуите](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial), [программное обеспечение officespace Software](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial), [Матрица приоритетов](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

Дополнительные сведения о том, как лучше защитить организацию с помощью автоматизированной подготовки учетных записей пользователей, см. в статье [Автоматизация подготовки пользователей в приложениях SaaS с помощью Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Новые Федеративные приложения, доступные в коллекции Azure AD App — Ноябрь 2019

**Тип.** Новая функция.  
**Категория службы:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.

В ноябре 2019 мы добавили 21 новые приложения с поддержкой Федерации в коллекцию приложений:

[Аиртабле](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial), [Хутсуите](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial), [синий доступ для участников (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial), [Битли](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial), [Рива](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [реслифе Portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [негометрикспортал единый вход (SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial), [теамсчамп](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), [мотус](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial), [мяряка](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial), [блуемаил](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial), [OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial), [foko Retail](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial)&, Qmarkets- [инновации](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial), [Netskope Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial)uniFLOW, [Claromentis Online](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial), [JISC](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial), [избирателей](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial) [, e4enable](https://portal.e4enable.com/)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Новая и улучшенная коллекция приложений Azure AD

**Тип.** Измененная функция.  
**Категория службы:** Корпоративные приложения  
**Возможности продукта.** Единый вход.

Мы обновили коллекцию приложений Azure AD, чтобы упростить поиск предварительно интегрированных приложений, поддерживающих подготовку, OpenID Connect Connect и SAML в клиенте Azure Active Directory.

Дополнительные сведения см. в статье [Добавление приложения в клиент Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal).

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Увеличено ограничение длины определения роли приложения с 120 до 240 символов

**Тип.** Измененная функция.  
**Категория службы:** Корпоративные приложения  
**Возможности продукта.** Единый вход.

Мы слышали о том, что длина значения определения роли приложения в некоторых приложениях и службах слишком мала в 120 символов. В ответ мы увеличили максимальную длину определения значения роли до 240 символов.

Дополнительные сведения об использовании определений ролей для конкретных приложений см. в разделе [Добавление ролей приложения в приложение и их получение в токене](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps).

---
