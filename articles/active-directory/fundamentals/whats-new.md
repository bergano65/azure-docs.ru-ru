---
title: Что нового? Заметки о выпуске Azure Active Directory | Документация Майкрософт
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
ms.date: 12/10/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23e48787162bccbf45e420f2deb002879c72fa09
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978885"
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

[В случае критического: Mobile](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial), [Juno путешествие](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial), [Експоненср](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial), [](https://tact.ai/assistant/)опускапита, [Управление денежными](http://cm1.opuscapita.com/tenantname)средствами, [салестим](https://prd.salestim.io/forms), [Леарнстер](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial), [dynaTrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial), [хунчбузз](https://login.hunchbuzz.com/integrations/azure/process), [фрешворкс](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial), [Екорнелл](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial), [шифазмат](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial), [нетскопе Cloud Security](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial), [contenting](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial), [Bindtuning](https://bindtuning.com/login), [HireVue координировать — ЕС](https://www.hirevue.com/), [HireVue координировать-USOnly](https://www.hirevue.com/), [HireVue координировать-US](https://www.hirevue.com/), [WittyParrot Область знаний](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [клаудморе](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial), [Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial), [камбиум ксиррус еасипасс Portal](https://login.xirrus.com/azure-signup), [Paylocity](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial), ["почта"!](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial), [Группа](https://theteamie.com/), [скорость для групп](https://velocity.peakup.org/teams/login), [SIGNL4](https://account.signl4.com/manage), [еаб навигации по Impl](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial), [Скринмит](https://console.screenmeet.com/), [Омега Point](https://pi.ompnt.com/), [выступление электронной почты для Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [выступление электронной почты для Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ексакткаре SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial), [ихеалсхоме системы навигации](https://ihealthnav.com/account/signin) [ Кубие](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

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

[Счедулелук](https://schedulelook.bbsonlineservices.net/), Microsoft [](https://aec.cintoo.com/login) [Azure SSO Access для есидекс соответствия требованиям Office™ — единый вход](https://docs.microsoft.com/azure/active-directory/saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial), [iServer Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/iserver-portal-tutorial), [скисите](https://docs.microsoft.com/azure/active-directory/saas-apps/skysite-tutorial), [Concur командировок и расходов](https://docs.microsoft.com/azure/active-directory/saas-apps/concur-travel-and-expense-tutorial), [воркбоард](https://docs.microsoft.com/azure/active-directory/saas-apps/workboard-tutorial), [еефлов](https://apps.yeeflow.com/), [Arc](https://docs.microsoft.com/azure/active-directory/saas-apps/arc-facilities-tutorial), [луваре Стратус Team](https://stratus.emea.luware.cloud/login), [широкие идеи](https://wideideas.online/wideideas/), [присма Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial) [, ждлт, ренраку](https://clients.jdlt.co.uk/login) [,](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive)SealPath [, Облако](https://docs.microsoft.com/azure/active-directory/saas-apps/renraku-tutorial) [](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial) [](https://app.penneo.com/) [](https://app.testhtm.com/settings/email-integration) [Whitesource](https://docs.microsoft.com/azure/active-directory/saas-apps/whitesource-tutorial), [размещенный Heritage Online SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-heritage-online-sso-tutorial), [IDC](https://docs.microsoft.com/azure/active-directory/saas-apps/idc-tutorial), [Какехр](https://docs.microsoft.com/azure/active-directory/saas-apps/cakehr-tutorial), [BIS](https://docs.microsoft.com/azure/active-directory/saas-apps/bis-tutorial), [директор по производству Кай Team Build](https://ms-contacts.coo-kai.jp/), [Sonarqube](https://docs.microsoft.com/azure/active-directory/saas-apps/sonarqube-tutorial), [Управление удостоверениями](https://docs.microsoft.com/azure/active-directory/saas-apps/adobe-identity-management-tutorial), возможности [обнаружения, преимущества единого входа](https://docs.microsoft.com/azure/active-directory/saas-apps/discovery-benefits-sso-tutorial), [Амелио](https://app.amelio.co/), [iTask](https://itask.yipinapp.com/)

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

## <a name="august-2019"></a>Август 2019 г.

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>Расширенный поиск, фильтрация и сортировка для групп доступны на портале Azure AD (общедоступная Предварительная версия).

**Тип.** Новая функция.  
**Категория службы.** Управление группами.  
**Возможности продукта.** Совместная работа.

Мы рады сообщить о доступности общедоступной предварительной версии для расширенной работы с группами на портале Azure AD. Эти улучшения помогают лучше управлять группами и списками членов, предоставляя:

- Расширенные возможности поиска, например поиск подстрок в списках групп.
- Расширенные параметры фильтрации и сортировки для списков членов и владельцев.
- Новые возможности поиска для списков членов и владельцев.
- Более точные счетчики групп для больших групп.

Дополнительные сведения см. [в разделе Управление группами в портал Azure](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Для управления регистрацией приложений доступны новые пользовательские роли (общедоступная Предварительная версия)

**Тип.** Новая функция.  
**Категория службы:** RBAC  
**Возможность продукта.** Контроль доступа.

Пользовательские роли (доступные в подписке Azure AD P1 или P2) теперь могут помочь в обеспечении точного доступа, позволяя создавать определения ролей с конкретными разрешениями, а затем назначать эти роли конкретным ресурсам. Сейчас вы создаете пользовательские роли с помощью разрешений для управления регистрацией приложений, а затем назначаете роль конкретному приложению. Дополнительные сведения о пользовательских ролях см. [в разделе роли настраиваемого администратора в Azure Active Directory (Предварительная версия)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview).

Если вам нужны дополнительные разрешения или ресурсы, которые сейчас не отображаются, вы можете отправить отзыв на наш [сайт обратной связи Azure](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) , и мы добавим ваш запрос на карту обновления в дороге.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Новые журналы подготовки могут помочь в мониторинге и устранении неполадок развертывания подготовки приложений (общедоступная Предварительная версия)

**Тип.** Новая функция.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта.** Управление жизненным циклом удостоверений.

Доступны новые журналы подготовки, помогающие отслеживать и устранять неполадки при развертывании пользователей и групп. Эти новые файлы журналов содержат следующие сведения:

- Какие группы были успешно созданы в [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)
- Какие роли были импортированы из [Amazon Web Services (AWS)](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws)
- Какие сотрудники не были импортированы из [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)

Дополнительные сведения см. [в разделе Подготовка отчетов на портале Azure Active Directory (Предварительная версия)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs).

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Новые отчеты о безопасности для всех администраторов Azure AD (общая доступность)

**Тип.** Новая функция.  
**Категория службы.** Защита идентификации.  
**Возможности продукта.** Безопасность и защита удостоверений.

По умолчанию все администраторы Azure AD вскоре получат доступ к современным отчетам о безопасности в Azure AD. До конца сентября вы сможете использовать баннер в верхней части современных отчетов о безопасности, чтобы вернуться к старым отчетам.

Современные отчеты о безопасности предоставляют дополнительные возможности из старых версий, включая:

- Расширенная фильтрация и сортировка
- Групповые действия, такие как отклонение пользователя
- Подтверждение скомпрометированных или защищенных сущностей
- Состояние риска, охватывающее: под угрозой, отклонено, исправлено и подтверждено скомпрометировано
- Новые обнаружения, связанные с рисками (доступны для Azure AD Premium Подписчики)

Дополнительные сведения см. в разделе [рискованные пользователи](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users), [рискованные входы](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins)и [обнаружения рисков](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections).

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>Назначаемое пользователем управляемое удостоверение доступно для виртуальных машин и масштабируемых наборов виртуальных машин (общая доступность)

**Тип.** Новая функция.  
**Категория службы:** Управляемые удостоверения для ресурсов Azure  
**Возможности продукта.** Возможности для разработчиков

Назначенные пользователем управляемые удостоверения теперь общедоступны для виртуальных машин и масштабируемых наборов виртуальных машин. В рамках этого Azure может создать удостоверение в клиенте Azure AD, который является доверенным для используемой подписки, и может быть назначен одному или нескольким экземплярам службы Azure. Дополнительные сведения о назначенных пользователем управляемых удостоверениях см. в статье [что такое управляемые удостоверения для ресурсов Azure?](https://aka.ms/azuremanagedidentity).

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Пользователи могут сбрасывать пароли с помощью мобильного приложения или маркера оборудования (общая доступность).

**Тип.** Измененная функция.  
**Категория службы.** Самостоятельный сброс пароля.  
**Возможности продукта.** Проверка подлинности пользователей.

Пользователи, которые зарегистрировали мобильное приложение в Организации, теперь могут сбросить свой пароль, утверждая уведомление из Microsoft Authenticator приложения или введя код из своего мобильного приложения или маркера оборудования.

Дополнительные сведения см. в статье [как это работает: самостоятельный сброс пароля в Azure AD](https://aka.ms/authappsspr). Дополнительные сведения о пользовательском интерфейсе см. в статье [о сбросе собственного рабочего или учебного пароля](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview).

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET игнорирует общий кэш MSAL.NET для сценариев от имени.

**Тип.** Исправление.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Проверка подлинности пользователей.

Начиная с библиотеки проверки подлинности Azure AD (ADAL.NET) версии 5.0.0-Preview, разработчики приложений должны [сериализовать один кэш на учетную запись для веб-приложений и веб-API](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). В противном случае в некоторых сценариях, использующих [поток «от имени](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow)» и в некоторых случаях использования `UserAssertion`, может возникнуть повышение привилегий. Чтобы избежать этой уязвимости, ADAL.NET теперь игнорирует общий кэш библиотеки проверки подлинности Майкрософт для DotNet (MSAL.NET) для сценариев от имени.

Дополнительные сведения об этой неполадке см. в разделе [Azure Active Directory уязвимость повышения привилегий в библиотеке проверки подлинности](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Новые Федеративные приложения, доступные в коллекции Azure AD App — 2019 августа

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.

В августе 2019 мы добавили 26 новых приложений с поддержкой Федерации в коллекцию приложений:

[Гражданская платформа](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial), [Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial), проновос Operations [Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial), [когнидокс](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial), [виарепорт инатив Portal (Европа)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial), [Azure Databricks](https://azure.microsoft.com/services/databricks), переработайте, [Academy посещение](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial), [Матрица приоритетов](https://sync.appfluence.com/pmwebng/) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial) [каусто MySpace](https://cousto.platformers.be/account/login), [уплоадкаре](https://uploadcare.com/accounts/signup/), [КАРБОНИТЕ резервное копирование](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial), [кпксинк по цинком](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial), [чаржеби](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial), [Доставка. медиа™ Portal](https://portal.deliver.media), [оказался образование](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat AD Подключение](https://www.stashcat.com), [мигание](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial), [воколи](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial), [проновос Analytics](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial), [сигстр](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial), [дарвинбокс](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial), [Просмотр по цветам](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial) [, подпрограмма,](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial) [еаб Навигация по стратегическим](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial) решениям

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>Доступны новые версии модулей PowerShell AzureAD и AzureADPreview PowerShell.

**Тип.** Измененная функция.  
**Категория службы.** Другая.  
**Возможности продукта.** Каталог.

Доступны новые обновления модулей PowerShell для AzureAD и AzureAD Preview:

- В параметр `Get-AzureADDirectoryRole` в модуле AzureAD был добавлен новый параметр `-Filter`. Этот параметр позволяет фильтровать роли каталога, возвращаемые командлетом.
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

**Тип.** Измененная функция.  
**Категория службы.** Управление группами.  
**Возможности продукта.** Совместная работа.

Мы внесли некоторые улучшения пользовательского интерфейса в построитель динамических групп правил, доступный в портал Azure, чтобы упростить настройку нового правила или изменить существующие правила. Это улучшение проектирования позволяет создавать правила с использованием до пяти выражений, а не только одного. Мы также обновили список свойств устройства, чтобы удалить нерекомендуемые свойства устройства.

Дополнительные сведения см. в разделе [Управление правилами динамического членства](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership).

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Новое разрешение Microsoft Graph приложения, доступное для использования с проверками доступа

**Тип.** Измененная функция.  
**Категория службы.** Проверка доступа.  
**Возможности продукта:** Управление удостоверениями

Мы предоставили новое разрешение Microsoft Graph приложения, `AccessReview.ReadWrite.Membership`, которое позволяет приложениям автоматически создавать и получать проверки доступа для членства в группах и назначений приложений. Это разрешение можно использовать в запланированных заданиях или в рамках автоматизации, не требуя контекста пользователя, выполнившего вход.

Дополнительные сведения см. в [примере, посвященном созданию проверок доступа Azure AD с помощью Microsoft Graph разрешений приложения в блоге PowerShell](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Журналы действий Azure AD теперь доступны для облачных экземпляров государственных организаций в Azure Monitor

**Тип.** Измененная функция.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.

Мы рады сообщить, что теперь журналы действий Azure AD доступны для облачных экземпляров государственных организаций в Azure Monitor. Теперь вы можете отправить журналы Azure AD в учетную запись хранения или в концентратор событий, чтобы интегрировать их со средствами SIEM, такими как [Sumologic](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic), [Splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)и [ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight). 

Дополнительные сведения о настройке Azure Monitor см. в статье [журналы действий Azure AD в Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Обновите свои пользователи до новой, расширенной информации о безопасности

**Тип.** Измененная функция.  
**Категория службы:**  Проверки подлинности (имена входа)   
**Возможности продукта.** Проверка подлинности пользователей.

25 сентября 2019 г. Мы будем отключать старые, нерасширенные сведения о безопасности для регистрации и управления сведениями о безопасности пользователей и включать только новую, [расширенную версию](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271). Это означает, что пользователи больше не смогут использовать старый интерфейс.

Дополнительные сведения о расширенной работе со сведениями о безопасности см. в [документации администратора](https://aka.ms/securityinfodocs) и в нашей [документации пользователя](https://aka.ms/securityinfoguide).

#### <a name="to-turn-on-this-new-experience-you-must"></a>Чтобы включить этот новый интерфейс, необходимо выполнить следующие действия.

1. Войдите в портал Azure в качестве глобального администратора или администратора пользователей.

2. Перейдите в раздел **Azure Active Directory > Параметры пользователя > Управление параметрами для функций предварительной версии панели доступа**.

3. В разделе **пользователи могут использовать функции предварительной версии для регистрации и управления сведениями о безопасности — Расширенная** , выберите пункт **выбрано**, а затем выберите группу пользователей или выберите **все** , чтобы включить эту функцию для всех пользователей в клиенте.

4. В разделе * * пользователи могут использовать функции предварительной версии для регистрации и управления областью безопасности * * info * * * *, выберите **нет**.

5. Сохраните параметры.

    После сохранения параметров вы больше не сможете получить доступ к старому интерфейсу сведений о безопасности.

>[!Important]
>Если вы не выполните эти действия до 25 сентября 2019, ваш клиент Azure Active Directory будет автоматически включен для повышения удобства работы. Если у вас есть вопросы, свяжитесь с нами по адресу registrationpreview@microsoft.com.

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Запросы проверки подлинности, использующие POST Logins, будут более строго проверены

**Тип.** Измененная функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Стандартами

Начиная с 2 сентября 2019, запросы проверки подлинности, использующие метод POST, будут более строго проверяться по стандартам HTTP. В частности, пробелы и двойные кавычки (") больше не будут удаляться из значений формы запроса. Эти изменения не приводят к нарушению работы существующих клиентов и позволяют гарантировать, что запросы, отправляемые в Azure AD, будут надежно обрабатываться каждый раз.

Дополнительные сведения см. в статье [уведомления о критических изменениях в Azure AD](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored).

---

## <a name="july-2019"></a>Июль 2019 г.

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Планирование изменений: обновление службы прокси приложения для поддержки только TLS 1,2

**Тип.** Планирование изменений.  
**Категория службы.** Прокси-сервер приложения.  
**Возможность продукта.** Контроль доступа.

Чтобы обеспечить наиболее надежное шифрование, мы начнем ограничить доступ службы прокси приложения только к протоколам TLS 1,2. Это ограничение изначально будет выводиться для клиентов, которые уже используют протоколы TLS 1,2, поэтому вы не сможете увидеть их. Полная нерекомендуемость протоколов TLS 1,0 и TLS 1,1 будет завершена до 31 августа 2019. Клиенты, которые по-прежнему используют TLS 1,0 и TLS 1,1, получат расширенное уведомление о подготовке к этому изменению.

Для поддержания подключения к службе прокси приложения в рамках этого изменения рекомендуется убедиться, что комбинации "клиент-сервер" и "браузер-сервер" обновлены для использования TLS 1,2. Также рекомендуется включить все клиентские системы, используемые сотрудниками, для доступа к приложениям, опубликованным через службу прокси приложения.

Дополнительные сведения см. [в статье Добавление локального приложения для удаленного доступа через прокси приложения в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Планирование изменений: обновления конструктора поступают в коллекцию приложений

**Тип.** Планирование изменений.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Единый вход.

Новые изменения пользовательского интерфейса поступают в структуру **добавления из области коллекции** в колонке **Добавление приложения** . Эти изменения облегчают поиск приложений, поддерживающих автоматическую подготовку, OpenID Connect Connect, язык разметки зявлений системы безопасности (SAML) (SAML) и единый вход по паролю (SSO).

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Планирование изменений: Удаление IP-адреса сервера MFA с IP-адреса Office 365

**Тип.** Планирование изменений.  
**Категория службы:** MFA  
**Возможности продукта.** Безопасность и защита удостоверений.

Мы удалим IP-адрес сервера MFA из [IP-адреса и с URL Office 365](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Если вы в настоящее время используете эти страницы для обновления параметров брандмауэра, необходимо убедиться, что вы также включаете список IP-адресов, описанных в разделе **требования к брандмауэру сервер многофакторной идентификации Azure** статьи [Приступая к работе с сервер многофакторной идентификации Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements) .

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Для токенов только для приложений теперь требуется, чтобы клиентское приложение существовало в клиенте ресурса.

**Тип.** Исправление.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Проверка подлинности пользователей.

26 июля 2019 г. Мы изменили, как мы предоставляем маркеры только для приложений через [предоставление учетных данных клиента](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow). Ранее приложения могли получить токены для вызова других приложений, независимо от того, находилось ли клиентское приложение у клиента. Мы обновили это поведение, так что ресурсы с одним клиентом, иногда называемые веб-API, могут вызываться только клиентскими приложениями, которые существуют в клиенте ресурса.

Если ваше приложение не находится в клиенте ресурсов, вы получите сообщение об ошибке, в котором говорится, `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` исправить эту проблему, необходимо создать субъект-службу клиентского приложения в клиенте с помощью [конечной точки согласия администратора](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) или [PowerShell](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell), которая гарантирует, что клиент предоставил приложению разрешение на работу в клиенте.

Дополнительные сведения см. в разделе [новые возможности проверки подлинности](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Существующее согласие между клиентом и API-интерфейсом остается необязательным. Приложения по-прежнему должны выполнять свои собственные проверки авторизации.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Новый безпарольный вход в Azure AD с помощью ключей безопасности FIDO2

**Тип.** Новая функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Проверка подлинности пользователей.

Теперь клиенты Azure AD могут задавать политики для управления ключами безопасности FIDO2 для пользователей и групп Организации. Конечные пользователи также могут самостоятельно регистрировать свои ключи безопасности, использовать ключи для входа в учетные записи Майкрософт на веб-сайтах на устройствах с поддержкой FIDO, а также входить на устройства Windows 10, присоединенные к Azure AD.

Дополнительные сведения см. [в статье Включение входа без пароля для Azure AD (Предварительная версия)](/azure/active-directory/authentication/concept-authentication-passwordless) для сведений, относящихся к администратору, и Настройка сведений о [безопасности для использования ключа безопасности (Предварительная версия)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) для сведений, относящихся к конечным пользователям.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Новые Федеративные приложения, доступные в коллекции Azure AD App-Июль 2019

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.

В июле 2019 мы добавили 18 новых приложений с поддержкой Федерации в коллекцию приложений:

[Унжербоекк Software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [яркий шаблон омничаннел Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), [Нелли](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [Аккуиреио](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [Луоп](https://www.looop.co/schedule-a-demo/), [продуктбоард](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [Microsoft Azure SSO Access для есидекс соответствия Office™](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [устанавливать ориентиры](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [флипснакк](https://www.flipsnack.com/accounts/sign-in-sso.html), [Вандера](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [HyperAnna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [PharmID WasteWitness](https://www.pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/) [, JFrog](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial) .

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Автоматизация подготовки учетных записей пользователей для новых поддерживаемых приложений SaaS

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Мониторинг и отчетность.

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

**Тип.** Новая функция.  
**Категория службы.** Доменные службы Azure AD.  
**Возможности продукта.** Доменные службы Azure AD.

Если вы устали управлять длинными списками IP-адресов и диапазонов, вы можете использовать новый тег **азуреактиведиректоридомаинсервицес** Network Service в группе безопасности сети Azure для защиты входящего трафика в подсети виртуальной сети доменных служб Azure AD.

Дополнительные сведения об этом новом теге службы см. в статье [группы безопасности сети для доменных служб Azure AD](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Новые Аудиты безопасности для доменных служб Azure AD (общедоступная Предварительная версия)

**Тип.** Новая функция.  
**Категория службы.** Доменные службы Azure AD.  
**Возможности продукта.** Доменные службы Azure AD.

Мы рады сообщить о выпуске аудита безопасности службы домена Azure AD до общедоступной предварительной версии. Аудит безопасности позволяет получить важные сведения о службах проверки подлинности путем потоковой передачи событий аудита безопасности в целевые ресурсы, включая службу хранилища Azure, рабочие области Azure Log Analytics и концентратор событий Azure, с помощью службы домена Azure AD. портала.

Дополнительные сведения см. в статье [Включение аудита безопасности для доменных служб Azure AD (Предварительная версия)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Новые методы проверки подлинности использование & Insights (общедоступная Предварительная версия)

**Тип.** Новая функция.  
**Категория службы.** Самостоятельный сброс пароля.  
**Возможности продукта.** Мониторинг и отчетность.

Новые методы проверки подлинности & отчеты об аналитике позволяют понять, как в организации регистрируются такие функции, как многофакторная идентификация Azure и самостоятельный сброс пароля, включая число зарегистрированных. Пользователи для каждого компонента, частота использования самостоятельного сброса пароля для сброса паролей и метод сброса.

Дополнительные сведения см. в статье [использование методов проверки Подлинности & Insights (Предварительная версия)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Новые отчеты о безопасности доступны для всех администраторов Azure AD (общедоступная Предварительная версия)

**Тип.** Новая функция.  
**Категория службы.** Защита идентификации.  
**Возможности продукта.** Безопасность и защита удостоверений.

Все администраторы Azure AD теперь могут выбрать баннер в верхней части существующих отчетов о безопасности, например отчет " **Пользователи, помеченные для риска** ", чтобы приступить к использованию новых средств обеспечения безопасности, как показано в отчетах о **рискованных пользователях** и событиях **входа в систему** . Со временем все отчеты о безопасности будут перенесены из старых версий в новые, а новые отчеты предоставляют следующие дополнительные возможности:

- Расширенная фильтрация и сортировка

- Групповые действия, такие как отклонение пользователя

- Подтверждение скомпрометированных или защищенных сущностей

- Состояние риска, охватывающее: под угрозой, отклонено, исправлено и подтверждено скомпрометировано

Дополнительные сведения см. в разделе Отчет [о рискованных пользователях](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users) и отчет о событиях [входа с рисками](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Новые Аудиты безопасности для доменных служб Azure AD (общедоступная Предварительная версия)

**Тип.** Новая функция.  
**Категория службы.** Доменные службы Azure AD.  
**Возможности продукта.** Доменные службы Azure AD.

Мы рады сообщить о выпуске аудита безопасности службы домена Azure AD до общедоступной предварительной версии. Аудит безопасности позволяет получить важные сведения о службах проверки подлинности путем потоковой передачи событий аудита безопасности в целевые ресурсы, включая службу хранилища Azure, рабочие области Azure Log Analytics и концентратор событий Azure, с помощью службы домена Azure AD. портала.

Дополнительные сведения см. в статье [Включение аудита безопасности для доменных служб Azure AD (Предварительная версия)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Новая прямая Федерация B2B с использованием SAML/WS-подача (общедоступная Предварительная версия)

**Тип.** Новая функция.  
**Категория службы.** B2B.  
**Возможности продукта.** B2B и B2C.

Прямая Федерация позволяет упростить работу с партнерами, для которых управляемое ИТ-решение не является Azure AD, работая с системами идентификации, поддерживающими стандарты SAML или WS-подач. После настройки прямой связи Федерации с партнером любой новый гостевой пользователь, который вы пригласили от этого домена, сможет совместно работать с вами с помощью существующей учетной записи организации, что делает работу пользователя с вашими гостями более удобной.

Дополнительные сведения см. [в статье прямая Федерация с AD FS и сторонними поставщиками для гостевых пользователей (Предварительная версия)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Автоматизация подготовки учетных записей пользователей для новых поддерживаемых приложений SaaS

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Мониторинг и отчетность.

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

**Тип.** Новая функция.  
**Категория службы.** Управление группами.  
**Возможности продукта.** Совместная работа.

Теперь при создании или обновлении имени группы на портале Azure AD мы продолжим проверку на дублирование существующего имени группы в ресурсе. Если мы определили, что имя уже используется другой группой, вам будет предложено изменить имя.

Дополнительные сведения см. [в статье Управление группами на портале Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD теперь поддерживает статические параметры запроса в URI ответа (Redirect)

**Тип.** Новая функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Проверка подлинности пользователей.

Приложения Azure AD теперь могут регистрировать и использовать URI-идентификаторы ответа (перенаправления) со статическими параметрами запроса (например, `https://contoso.com/oauth2?idp=microsoft`) для запросов OAuth 2,0. Параметр статического запроса подчиняется строковому совпадению для URI ответа, как и любая другая часть URI ответа. Если нет ни одной зарегистрированной строки, соответствующей URL-адресу, декодированному, то запрос отклоняется. Если URI ответа найден, то для перенаправления пользователя используется вся строка, включая параметр статического запроса.

Идентификаторы URI динамических ответов по-прежнему запрещены, так как они представляют угрозу безопасности и не могут использоваться для сохранения сведений о состоянии в запросе проверки подлинности. Для этой цели используйте параметр `state`.

В настоящее время экраны регистрации приложений портал Azure все еще блокируют параметры запроса. Однако можно вручную изменить манифест приложения, чтобы добавить и проверить параметры запроса в приложении. Дополнительные сведения см. в разделе [новые возможности проверки подлинности](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Журналы действий (API-интерфейсы MS Graph) для Azure AD теперь доступны с помощью командлетов PowerShell.

**Тип.** Новая функция.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.

Мы рады сообщить, что журналы действий Azure AD (отчеты для аудита и входа) теперь доступны в модуле Azure AD PowerShell. Ранее можно было создавать собственные сценарии с помощью конечных точек MS API Graph, и теперь мы расширили эту возможность для командлетов PowerShell.

Дополнительные сведения об использовании этих командлетов см. в разделе [командлеты Azure AD PowerShell для создания отчетов](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Обновлены элементы управления фильтра для журналов аудита и входа в Azure AD.

**Тип.** Измененная функция.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.

Отчеты журнала аудита и входа обновлены, поэтому теперь можно применять различные фильтры, не добавляя их в качестве столбцов на экранах отчета. Кроме того, теперь можно решить, сколько фильтров нужно отображать на экране. Все эти обновления работают вместе, чтобы сделать отчеты более удобными для чтения и в большей области с учетом ваших потребностей.

Дополнительные сведения об этих обновлениях см. в разделе [Фильтрация журналов аудита](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) и [Фильтрация действий входа](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities).

---
