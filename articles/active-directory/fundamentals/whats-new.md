---
title: Новые возможности Заметки о выпуске Azure Active Directory | Документация Майкрософт
description: Узнайте о новых возможностях Azure Active Directory; Например, последние заметки о выпуске, известные проблемы, исправления ошибок, устаревшие функции и предстоящие изменения.
services: active-directory
author: ajburnle
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ff942cdad74c3b8b71a8f1658f13faae021b983
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/09/2020
ms.locfileid: "89567458"
---
# <a name="whats-new-in-azure-active-directory"></a>Новые возможности Azure Active Directory

>Получайте уведомления о том, когда следует повторно посетить эту страницу для обновлений, скопировав и вставив URL-адрес `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` в средство чтения RSS-канала ![значок средства чтения RSS](./media/whats-new/feed-icon-16x16.png).

Azure AD усовершенствуется на постоянной основе. Чтобы вы оставались в курсе последних разработок, в этой статье предоставлены такие сведения:

- Последние выпуски.
- Известные проблемы
- Исправления ошибок
- Нерекомендуемые функции.
- Планы по изменениям.

Эта страница обновляется ежемесячно, поэтому регулярно пересматривайте ее. Если вы ищете элементы старше шести месяцев, их можно найти в [архиве для новых возможностей Azure Active Directory](whats-new-archive.md).

---

## <a name="august-2020"></a>Август 2020 г. 
 
### <a name="updates-to-azure-multi-factor-authentication-server-firewall-requirements"></a>Обновления требований к брандмауэру сервер Многофакторной идентификации Azure

**Тип.** Планирование изменений.  
**Категория службы:** MFA  
**Возможности продукта.** Безопасность и защита удостоверений.
 
Начиная с 1 октября 2020, требования к брандмауэру сервера Azure MFA потребует дополнительных диапазонов IP-адресов.

Если в вашей организации есть исходящие правила брандмауэра, обновите их, чтобы серверы MFA могли обмениваться данными со всеми необходимыми диапазонами IP-адресов. Диапазоны IP-адресов описаны в статье [требования к брандмауэру сервер многофакторной идентификации Azure](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements).

---

### <a name="upcoming-changes-to-user-experience-in-identity-secure-score"></a>Предстоящие изменения в работе пользователей в оценке безопасности идентификации

**Тип.** Планирование изменений.  
**Категория службы:** Возможность защиты идентификации с использованием удостоверений **:** безопасность & защита

Мы обновляем портал безопасности удостоверений, чтобы согласовать изменения, появившиеся в [новом выпуске](/microsoft-365/security/mtp/microsoft-secure-score-whats-new?view=o365-worldwide)Microsoft для оценки безопасности. 

Предварительная версия с изменениями будет доступна в начале сентября. К изменениям в предварительной версии относятся:
- "Оценка безопасного удостоверения" переименована в "Оценка безопасности для удостоверения" для выравнивания торговой марки с рейтингом Microsoft Secure
- Точки нормализованы до стандартного масштаба и отображаются в процентах вместо точек

В этой предварительной версии клиенты могут переключаться между существующим интерфейсом и новым интерфейсом. Эта предварительная версия будет последней до конца ноября 2020. После предварительной версии клиенты автоматически направляются в новый интерфейс UX.

---

### <a name="new-restricted-guest-access-permissions-in-azure-ad---public-preview"></a>Новые ограниченные права гостевого доступа в Azure AD — общедоступная Предварительная версия

**Тип.** Новая функция.  
**Категория службы:** Контроль доступа   
**Возможности продукта:** Управление пользователями

Мы обновили разрешения уровня каталога для гостевых пользователей. Эти разрешения позволяют администраторам требовать дополнительных ограничений и элементов управления для доступа внешних гостевых пользователей. Теперь администраторы могут добавлять дополнительные ограничения на доступ внешних гостевых компьютеров к профилю и сведениям о членстве для пользователей и групп. Благодаря этой общедоступной предварительной версии клиенты могут управлять доступом внешних пользователей при помощи маскировки членства в группах, в том числе запрещая гостевым пользователям видеть членство в группах, в которых они находятся.

Дополнительные сведения см. в разделе [ограниченные разрешения гостевого доступа](../users-groups-roles/users-restrict-guest-permissions.md) и [разрешения пользователей по умолчанию](./users-default-permissions.md).
 
---

### <a name="general-availability-of-delta-queries-for-service-principals"></a>Общая доступность разностных запросов для субъектов-служб

**Тип.** Новая функция.  
**Категория службы:** MS Graph  
**Возможности продукта.** Возможности для разработчиков
 
Microsoft Graph разностный запрос теперь поддерживает тип ресурса в версии 1.0:
- Субъект-служба

Теперь клиенты могут эффективно отслеживание изменений в этих ресурсах и предоставляют лучшее решение для синхронизации изменений в этих ресурсах с локальным хранилищем данных. Сведения о настройке этих ресурсов в запросе см. в разделе [Использование разностного запроса для записи изменений в Microsoft Graph данных](/graph/delta-query-overview).
 
---

### <a name="general-availability-of-delta-queries-for-oauth2permissiongrant"></a>Общая доступность разностных запросов для oAuth2PermissionGrant

**Тип.** Новая функция.  
**Категория службы:** MS Graph  
**Возможности продукта.** Возможности для разработчиков

Microsoft Graph разностный запрос теперь поддерживает тип ресурса в версии 1.0:
- OAuth2PermissionGrant

Теперь клиенты могут эффективно отслеживание изменений в этих ресурсах и предоставляют лучшее решение для синхронизации изменений в этих ресурсах с локальным хранилищем данных. Сведения о настройке этих ресурсов в запросе см. в разделе [Использование разностного запроса для записи изменений в Microsoft Graph данных](/graph/delta-query-overview).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---august-2020"></a>Новые Федеративные приложения, доступные в коллекции приложений Azure AD — 2020 августа

**Тип.** Новая функция.  
**Категория службы:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.

В августе 2020 мы добавили в коллекцию приложений следующие 25 новых приложений с поддержкой федерации:

[Backup365](https://portal.backup365.io/login), [СОАПБОКС](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2), [алма SIS](https://almau.getalma.com/), [Енлифт Dynamics 365](http://enlyft.com/), [серравиев использование пространства программных решений](../saas-apps/serraview-space-utilization-software-solutions-tutorial.md), [Уник](https://web.uniq.app/), [заметно](../saas-apps/visibly-tutorial.md), [Зило](../saas-apps/zylo-tutorial.md), [Едментум-обучающие оценки точный путь](https://auth.edmentum.com/elf/login), [Циберлаб](https://cyberlab.evolvesecurity.com/#/welcome) [Bpanda](https://goto.bpanda.com/login), [Алтамира хрм](../saas-apps/altamira-hrm-tutorial.md), [виревхил](../saas-apps/wirewheel-tutorial.md), [зикс соответствие и отслеживание](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common), [гринлигхт Корпоративная платформа управления](../saas-apps/greenlight-enterprise-business-controls-platform-tutorial.md), [Genetec Clearance пространство](https://www.clearance.network/), [ISAM](../saas-apps/isams-tutorial.md) [, VeraSMART](../saas-apps/verasmart-tutorial.md) [, amiko,](https://amiko.web.rivero.app/) [twingate](https://auth.twingate.com/signup) [Calendar Connect](https://app.vivun.com/dashboard/calendar/connect), [Funnel Leasing](https://nestiolistings.com/sso/oidc/azure/authorize/) [Scalefusion SSL VPN](../saas-apps/fortigate-ssl-vpn-tutorial.md), [Bpanda конечный пользователь](https://www.wandera.com/) [Scalefusion](https://scalefusion.com/users/sign_in/)

Документацию по всем приложениям можно также найти здесь. https://aka.ms/AppsTutorial

Чтобы получить список приложений в коллекции приложений Azure AD, ознакомьтесь со сведениями в этой статье. https://aka.ms/AzureADAppRequest

---

### <a name="resource-forests-now-available-for-azure-ad-ds"></a>Сейчас доступны леса ресурсов для Azure AD DS 

**Тип:** Новая **Категория службы компонентов:** доменные службы Azure AD   
**Возможности продукта.** Доменные службы Azure AD.
 
Возможность использования лесов ресурсов в доменных службах Azure AD теперь общедоступна. Теперь можно включить авторизацию без синхронизации хэшей паролей для использования доменных служб Azure AD, включая авторизацию с помощью смарт-карт. Дополнительные сведения см. в разделе [Основные понятия и функции наборов реплик для Azure Active Directory доменных служб (Предварительная версия)](../../active-directory-domain-services/concepts-replica-sets.md).
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>Теперь доступна поддержка региональной реплики для управляемых доменов Azure AD DS

**Тип.** Новая функция.   
**Категория службы.** Доменные службы Azure AD.  
**Возможности продукта.** Доменные службы Azure AD.
 
Вы можете расширить управляемый домен для использования более одного набора реплик в каждом арендаторе Azure AD. Наборы реплик можно добавить в любую одноранговую виртуальную сеть в любом регионе Azure, поддерживающем доменные службы Azure AD. Дополнительные наборы реплик в разных регионах Azure предоставляют возможности географического аварийного восстановления для устаревших приложений, если регион Azure становится недоступен. Дополнительные сведения см. в разделе [Основные понятия и функции наборов реплик для Azure Active Directory доменных служб (Предварительная версия)](../../active-directory-domain-services/concepts-replica-sets.md).

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Общая доступность Azure AD мои входы в систему

**Тип.** Новая функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Взаимодействие с конечным пользователем
 
Мои входы в Azure AD — это новая функция, которая позволяет корпоративным пользователям просматривать историю входа, чтобы проверить наличие необычных действий. Кроме того, эта функция позволяет конечным пользователям сообщать о подозрительных действиях «это не я» или «это было я». Дополнительные сведения об использовании этой функции см. в статье [Просмотр и поиск по последним действиям входа на странице Мои входы](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity).
 
---

### <a name="sap-successfactors-hr-driven-user-provisioning-to-azure-ad-is-now-generally-available"></a>Теперь в общедоступной базе SAP SuccessFactors управление персоналом в Azure AD

**Тип.** Новая функция.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта:** Управление жизненным циклом удостоверений
 
Теперь вы можете интегрировать SAP SuccessFactors в качестве полномочного источника удостоверений с Azure AD и автоматизировать сквозной жизненный цикл идентификации с помощью событий HR, таких как новые сотрудники и прекращения, для подготовки и отмены подготовки учетных записей в Azure AD. 

Дополнительные сведения о настройке входящей подготовки SAP SuccessFactors в Azure AD см. в руководстве по [настройке SAP SuccessFactors для Active Directory подготовки пользователей](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md).
 
---

### <a name="custom-open-id-connect-ms-graph-api-support-for-azure-ad-b2c"></a>Пользовательский открытый идентификатор Connect поддержка MS API Graph для Azure AD B2C

**Тип.** Новая функция.  
**Категория службы.** B2C — управление удостоверениями клиентов.  
**Возможности продукта.** B2B и B2C.
 
Ранее пользовательские поставщики Connect Open ID можно было добавлять или управлять только с помощью портал Azure. Теперь Azure AD B2C клиенты могут добавлять их и управлять ими с помощью бета-версии Microsoft Graph API. Чтобы узнать, как настроить этот ресурс с помощью API, см. раздел [тип ресурса identityProvider](/graph/api/resources/identityprovider?view=graph-rest-beta).
 
---

### <a name="assign-azure-ad-built-in-roles-to-cloud-groups"></a>Назначение встроенных ролей Azure AD облачным группам

**Тип.** Новая функция.  
**Категория службы:** Роли Azure AD  
**Возможность продукта.** Контроль доступа.

Теперь вы можете назначить облачным группам встроенные роли Azure AD с помощью этой новой функции. Например, роль администратора SharePoint можно назначить Contoso_SharePoint_Admins группе. Вы также можете использовать PIM, чтобы сделать группу подходящим членом роли, а не предоставлять постоянный доступ. Сведения о настройке этой функции см. в статье [Использование облачных групп для управления назначениями ролей в Azure Active Directory (Предварительная версия)](../users-groups-roles/roles-groups-concept.md).
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>Теперь доступна встроенная роль "аналитика для бизнеса"

**Тип.** Новая функция.  
**Категория службы:** Роли Azure AD  
**Возможность продукта.** Контроль доступа.
 
Пользователи в роли "Бизнес-руководитель" Insights могут получить доступ к набору панелей мониторинга и аналитических данных с помощью [приложения M365 Insights](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Сюда входит полный доступ ко всем панелям мониторинга, а также предоставлены аналитические сведения и функции просмотра данных. Однако пользователи этой роли не имеют доступа к параметрам конфигурации продукта, которым отвечает роль администратора Insights. Дополнительные сведения об этой роли см. [в разделе разрешения роли администратора в Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md#insights-business-leader)
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>Встроенная роль администратора Insights теперь доступна

**Тип.** Новая функция.  
**Категория службы:** Роли Azure AD  
**Возможность продукта.** Контроль доступа.
 
Пользователи с ролью администратора Insights могут получить доступ к полному набору административных возможностей [приложения M365 Insights](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Пользователь этой роли может читать данные каталога, наблюдать за работоспособностью службы, обращаться к ним и получать доступ к параметрам администратора Insights. Дополнительные сведения об этой роли см. [в разделе разрешения роли администратора в Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md#insights-administrator)
 
--- 

### <a name="application-admin-and-cloud-application-admin-can-manage-extension-properties-of-applications"></a>Администратор приложения и администратор облачных приложений могут управлять свойствами расширения приложений.

**Тип.** Измененная функция.  
**Категория службы:** Роли Azure AD  
**Возможность продукта.** Контроль доступа.
 
Ранее только глобальный администратор может управлять [свойством расширения](/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http). Теперь вы также включаете эту возможность для администраторов приложений и администратора облачных приложений.
 
---

### <a name="mim-2016-sp2-hotfix-462630-and-connectors-1113010"></a>Пакет обновления MIM 2016 4.6.263.0 и соединители 1.1.1301.0

**Тип.** Измененная функция.  
**Категория службы:** Microsoft Identity Manager  
**Возможности продукта:** Управление жизненным циклом удостоверений

[Накопительный пакет исправлений (сборка 4.6.263.0)](https://support.microsoft.com/help/4576473/hotfix-rollup-package-build-4-6-263-0-is-available-for-microsoft-ident) доступен для Microsoft Identity Manager (MIM) 2016 с пакетом обновления 2 (SP2). Этот пакет исправлений содержит обновления для MIM CM, диспетчера синхронизации MIM и компонентов PAM. Кроме того, универсальные соединители MIM создают 1.1.1301.0, включая обновления для соединителя Graph.

---
 
## <a name="july-2020"></a>Июль 2020 г.

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>Как ИТ – ИТ, я хочу ориентироваться на клиентские приложения, используя условный доступ.

**Тип.** Планирование изменений.   
**Категория службы:** Условный доступ  
**Возможности продукта.** Безопасность и защита удостоверений.
 
В общедоступном выпуске условия "клиентские приложения" в условном доступа новые политики будут применяться по умолчанию ко всем клиентским приложениям. К ним относятся клиенты проверки подлинности прежних версий. Существующие политики останутся без изменений, но переключатель *Настройка да/нет* будет удален из существующих политик, чтобы легко увидеть, какие клиентские приложения применяются политикой. 

При создании новой политики обязательно Исключите пользователей и учетные записи служб, которые по-прежнему используют устаревшую проверку подлинности. в противном случае они будут заблокированы. [Подробнее](https://aka.ms/caclientapps).
 
---

### <a name="upcoming-scim-compliance-fixes"></a>Предстоящие исправления соответствия SCIM

**Тип.** Планирование изменений.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта:** Управление жизненным циклом удостоверений
 
Служба подготовки Azure AD использует стандарт SCIM для интеграции с приложениями. Наша реализация стандарта SCIM развивается, и мы планируем внести изменения в поведение, связанное с выполнением операций исправления, а также установить свойство "Active" для ресурса. [Подробнее](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md).
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>Параметр владельца группы на портале администрирования Azure будет изменен

**Тип.** Планирование изменений.  
**Категория службы.** Управление группами.  
**Возможности продукта.** Совместная работа.

Параметры владельца на странице Общие параметры могут быть настроены для ограничения привилегий назначения владельца ограниченной группой пользователей на портале администрирования Azure и на панели доступа. Скоро мы сможем назначить права владельца группы не только на этих двух порталах UX, но и применить ее к серверной части для обеспечения согласованного поведения в конечных точках, таких как PowerShell и Microsoft Graph. 

Мы начнем отключить текущие настройки для клиентов, которые не используют этот параметр, и предоставит возможность определения области пользователей для прав владельца группы в течение следующих нескольких месяцев. Рекомендации по обновлению параметров группы см. в разделе изменение сведений о группе с помощью [Azure Active Directory](./active-directory-groups-settings-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>Служба регистрации Azure Active Directory завершает поддержку TLS 1,0 и 1,1

**Тип.** Планирование изменений.  
**Категория службы:** Регистрация устройств и управление ими  
**Возможности продукта.** Платформа.

Скоро служба безопасности транспортного уровня (TLS) 1,2 и серверы обновлений и клиенты будут взаимодействовать со службой Регистрация устройств Azure Active Directory. Поддержка TLS 1,0 и 1,1 для связи со службой регистрации устройств Azure AD приведет к снятию с учета:
- 31 августа 2020 во всех облаках независимых (GCC High, DoD и т. д.)
- 30 октября 2020 г. во всех коммерческих облаках

Дополнительные [сведения](../devices/reference-device-registration-tls-1-2.md) о TLS 1,2 для службы регистрации Azure AD.

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>Входы Windows Hello для бизнеса, отображаемые в журналах входа в Azure AD

**Тип.** Исправление.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.
 
Windows Hello для бизнеса позволяет конечным пользователям входить на компьютеры Windows с помощью жеста (например, ПИН-кода или биометрической метрики). Администраторы Azure AD могут захотеть отличать входы Windows Hello для бизнеса от других входов в Windows в рамках Организации для проверки подлинности без пароля. 

Теперь администраторы могут увидеть, используется ли проверка подлинности Windows Hello для бизнеса, проверив вкладку Сведения о проверке подлинности для события входа в Windows в колонке входов Azure AD в портал Azure. Для проверки подлинности Windows Hello для бизнеса в поле метод проверки подлинности будет включен параметр "Виндовшеллофорбусинесс". Дополнительные сведения об интерпретации журналов входа см. в [документации по журналам входа](../reports-monitoring/concept-sign-ins.md).
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>Исправления для поведения удаления групп и улучшения производительности

**Тип.** Исправление.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта:** Управление жизненным циклом удостоверений
 
Ранее, когда группа изменилась с "в области" на "вне области", а администратор щелкнул перезапустить до завершения изменения, объект группы не был удален. Теперь объект группы будет удален из целевого приложения, если он выходит за пределы области (отключен, удален, не назначен или не прошел фильтр области). [Подробнее](../app-provisioning/how-provisioning-works.md#incremental-cycles).
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>Общедоступная Предварительная версия: Администраторы теперь могут добавлять в сообщение электронной почты пользовательское содержимое при создании проверки доступа.

**Тип.** Новая функция.  
**Категория службы:** Проверки доступа  
**Возможности продукта:** Управление удостоверениями
 
При создании новой проверки доступа проверяющий получает сообщение электронной почты с просьбой завершить проверку доступа. Многие наши клиенты попросили добавить к электронной почте пользовательское содержимое, например контактные данные, или другое дополнительное Вспомогательное содержимое для помощи рецензента. 

Теперь в общедоступной предварительной версии администраторы могут указать пользовательское содержимое в сообщении электронной почты, отправляемом рецензентам, добавив содержимое в разделе "Дополнительно" проверки доступа Azure AD. Рекомендации по созданию проверок доступа см. [в статье Создание проверки доступа для групп и приложений в проверках доступа Azure AD](../governance/create-access-review.md).
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>Поток кода авторизации для одностраничных приложений

**Тип.** Новая функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Возможности для разработчиков
 
Из-за современных сторонних ограничений для файлов cookie браузера, таких как Safari ITP, одностраничные приложения придется использовать поток кода авторизации, а не неявный поток для поддержки единого входа, а MSAL.js v 2. x теперь поддерживает поток кода авторизации. 

В портал Azure есть соответствующие обновления, поэтому вы можете обновить соглашение SPA, чтобы оно имело тип "Spa", и использовать поток кода проверки подлинности. Дополнительные сведения см. в статье [Вход пользователей и получение маркера доступа в JavaScript Spa с помощью потока кода проверки подлинности](../develop/quickstart-v2-javascript-auth-code.md) .
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>Azure AD Application Proxy теперь поддерживает веб-клиент службы удаленных рабочих столов

**Тип.** Новая функция.  
**Категория службы.** Прокси-сервер приложения.  
**Возможность продукта.** Контроль доступа.

Azure AD Application Proxy теперь поддерживает веб-клиент службы удаленных рабочих столов (RDS). Веб-клиент RDS позволяет пользователям получать доступ к инфраструктуре удаленный рабочий стол через любой браузер, поддерживающий HTLM5, такой как Microsoft ребр, Internet Explorer 11, Google Chrome и т. д. Пользователи могут взаимодействовать с удаленными приложениями или настольными системами, как и с локальными устройствами из любого места. С помощью Azure AD Application Proxy вы можете повысить уровень безопасности развертывания RDS, установив политики предварительной проверки подлинности и условного доступа для всех типов клиентских приложений с богатыми возможностями. Инструкции см. в статье [публикация удаленный рабочий стол с помощью AD application proxy Azure](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md).
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>Последующее поколение Azure AD B2C пользователей в общедоступной предварительной версии

**Тип.** Новая функция.  
**Категория службы.** B2C — управление удостоверениями клиентов.  
**Возможности продукта.** B2B и B2C.
 
Упрощенный пользовательский интерфейс предлагает функции предварительной версии и является домашним для всех новых функций. Пользователи смогут включать новые функции в рамках одного потока пользователей, уменьшая необходимость создавать несколько версий с каждым новым выпуском компонента. Наконец, новый удобный интерфейс пользователя упрощает выбор и создание потоков пользователей. Попробуйте сейчас, [создав пользовательский поток](../../active-directory-b2c/tutorial-create-user-flows.md). 

Дополнительные сведения о потоках пользователей см. [в разделе версии потока пользователей в Azure Active Directory B2C](../../active-directory-b2c/user-flow-versions.md#:~:text=    User flow  ,account. Usi ...  1 more rows ).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>Новые Федеративные приложения, доступные в коллекции приложений Azure AD — Июль 2020

**Тип.** Новая функция.  
**Категория службы:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
В июле 2020 мы добавили в коллекцию приложений следующие 55 новых приложений с поддержкой федерации:

[Рукоплескания ваши руки](http://www.rmit.com.ar/), [Аппреиз](https://microsoftteams.appreiz.com/), [инекстор Vault](https://inexto.com/inexto-suite/inextor), [бикаст](https://my.beekast.com/), [темплафи OpenID Connect Connect](https://app.templafy.com/), [петерконнектс секретарь](https://msteams.peterconnects.com/), [АЛОХАКЛАУД](https://appfusions.alohacloud.com/auth), [Control Tower](https://bpm.tnxcorp.com/sso/microsoft), [Кокум](https://start.cocoom.com/), [монеты для создания облака](https://sso.coinsconstructioncloud.com/#login/), [медксноте MT](https://task.teamsmain.medx.im/authorization), [Рефлект](https://reflekt.konsolute.com/login), [Ревер](https://app.reverscore.net/access), [MyCompanyArchive](https://login.mycompanyarchive.com/), [GReminders](https://app.greminders.com/o365-oauth), [Titanfile](../saas-apps/titanfile-tutorial.md), [Wootric](../saas-apps/wootric-tutorial.md), [SolarWinds Orion](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US), [OpenText Services Directory](../saas-apps/opentext-directory-services-tutorial.md), [site](../saas-apps/datasite-tutorial.md), [блог](../saas-apps/blogin-tutorial.md), [IntSights](../saas-apps/intsights-tutorial.md), [kpifire](../saas-apps/kpifire-tutorial.md), TextLine [,](../saas-apps/textline-tutorial.md) [Cloud Academy-SSO](../saas-apps/cloud-academy-sso-tutorial.md), [сообщество Spark](../saas-apps/community-spark-tutorial.md), Chatwork [,](../saas-apps/chatwork-tutorial.md) [CloudSign](../saas-apps/cloudsign-tutorial.md), [C3M Cloud Control](../saas-apps/c3m-cloud-control-tutorial.md), [SmartHR](https://smarthr.jp/), [NumlyEngage™](../saas-apps/numlyengage-tutorial.md), [Мичигана единый вход, исходящий](../saas-apps/michigan-data-hub-single-sign-on-tutorial.md), [SendSafely](../saas-apps/sendsafely-tutorial.md), [Egress](../saas-apps/egress-tutorial.md) [Eletive](https://app.eletive.com/), [правый кибербезопасности ADI](https://right-hand.ai/), [Fyde Enterprise Authentication](https://enterprise.fyde.com/), [Verme](../saas-apps/verme-tutorial.md), [lenses.IO](../saas-apps/lensesio-tutorial.md) [, в](https://app.uprise.co/sign-in) [момент](../saas-apps/momenta-tutorial.md)выпуска, [Q](https://q.moduleq.com/login), [CloudCords](../saas-apps/cloudcords-tutorial.md), [Tellme Bot](https://tellme365liteweb.azurewebsites.net/), [вдохновить](https://app.inspiresoftware.com/), [Maverics Identity SAML Connector](https://www.strata.io/identity-fabric/), [Smartschool (система управления школьными](https://smart-schoolapp.com/frmLoginForm)учреждениями), [zepto-Intelligent timekeeping](https://user.zepto-ai.com/signin), [Studi.ly](https://studi.ly/), [Trackplan](http://www.trackplanfm.com/), [Skedda](../saas-apps/skedda-tutorial.md), [WhosOnLocation](../saas-apps/whos-on-location-tutorial.md), [Coggle](../saas-apps/coggle-tutorial.md), [KEMP LoadMaster](https://kemptechnologies.com/cloud-load-balancer/), [BrowserStack единый вход](../saas-apps/browserstack-single-sign-on-tutorial.md)

Документацию по всем приложениям можно также найти здесь. https://aka.ms/AppsTutorial

Чтобы получить список приложений в коллекции приложений Azure AD, ознакомьтесь с подробными сведениями. https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---july-2020"></a>Новые соединители подготовки в коллекции приложений Azure AD — Июль 2020

**Тип.** Новая функция.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.

Теперь вы можете автоматизировать создание, обновление и удаление учетных записей пользователей для вновь интегрированного приложения [LinkedIn Learning](../saas-apps/linkedin-learning-provisioning-tutorial.md).

Дополнительные сведения об усилении защиты организации с помощью автоматизированной подготовки учетных записей пользователей см. в статье [Автоматическая подготовка пользователей для приложений SaaS в Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>Просмотр назначений ролей во всех областях и возможность их загрузки в CSV-файл

**Тип.** Измененная функция.  
**Категория службы:** Роли Azure AD  
**Возможность продукта.** Контроль доступа.
 
Теперь вы можете просматривать назначения ролей во всех областях для роли на вкладке "роли и Администраторы" на портале Azure AD. Вы также можете скачать эти назначения ролей для каждой роли в CSV-файл. Рекомендации по просмотру и добавлению назначений ролей см. [в разделе Просмотр и назначение ролей администратора в Azure Active Directory](../users-groups-roles/directory-manage-roles-portal.md).
 
---

### <a name="azure-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>Нерекомендуемая разработка многофакторной идентификации Azure (пакет SDK Azure MFA)

**Тип.** Прекращение поддержки.  
**Категория службы:** MFA  
**Возможности продукта.** Безопасность и защита удостоверений.
 
Разработка программного обеспечения многофакторной идентификации Azure (пакет SDK Azure MFA) достигла конца жизни в ноябре 14 2018, как было впервые объявлено в ноябре 2017 ноября. Корпорация Майкрософт будет завершать работу службы SDK, действующей 30 сентября 2020. Все вызовы пакета SDK завершатся ошибкой.

Если в вашей организации используется пакет SDK Azure MFA, необходимо выполнить миграцию до 30 сентября 2020:
- Пакет SDK Azure MFA для MIM: Если вы используете пакет SDK с MIM, необходимо выполнить миграцию на сервер Azure MFA и активировать Privileged Access Management (PAM), следуя этим [инструкциям](/microsoft-identity-manager/working-with-mfaserver-for-mim).   
- Пакет SDK Azure MFA для настраиваемых приложений. Рассмотрите возможность интеграции приложения в Azure AD и использование условного доступа для применения MFA. Чтобы приступить к работе, ознакомьтесь с этой [страницей](../manage-apps/plan-an-application-integration.md). 

---

## <a name="june-2020"></a>Июнь 2020 г. 

### <a name="user-risk-condition-in-conditional-access-policy"></a>Условие риска пользователя в политике условного доступа

**Тип.** Планирование изменений.  
**Категория службы:** Условный доступ  
**Возможности продукта.** Безопасность и защита удостоверений.
 

Поддержка пользовательских рисков в политике условного доступа Azure AD позволяет создавать несколько политик на основе рисков. Для разных пользователей и приложений могут потребоваться разные уровни риска для пользователей. В зависимости от рисков пользователей можно создавать политики для блокирования доступа, требовать многофакторную проверку подлинности, защищенную смену пароля или перенаправлять Microsoft Cloud App Security для принудительного применения политики сеанса, например для дополнительного аудита.

Условие риска пользователя требует Azure AD Premium P2, так как в нем используется защита идентификации Azure, которая является предложением P2. Дополнительные сведения об условном доступе см. в [документации по условному доступу Azure AD](../conditional-access/index.yml).

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>Единый вход SAML теперь поддерживает приложения, требующие установки Спнамекуалифиер при запросе

**Тип.** Исправление.  
**Категория службы:** Корпоративные приложения  
**Возможности продукта.** Единый вход.
 
Для некоторых приложений SAML требуется, чтобы Спнамекуалифиер возвращался в субъекте утверждения по запросу. Теперь Azure AD правильно реагирует на запрос Спнамекуалифиер в политике NameID запросов. Это также работает для входа, инициированного поставщиком услуг, и IdP выполняет процедуру входа.  Дополнительные сведения о протоколе SAML в Azure Active Directory см. в разделе [протокол SAML единого входа](../develop/single-sign-on-saml-protocol.md).

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Служба совместной работы Azure AD B2B поддерживает приглашение пользователей MSA и Google в клиентах Azure для государственных организаций.

**Тип.** Новая функция.  
**Категория службы.** B2B.  
**Возможности продукта.** B2B и B2C.
 

Клиенты Azure для государственных организаций, использующие функции совместной работы B2B, теперь могут приглашать пользователей с учетной записью Microsoft или Google. Чтобы узнать, может ли клиент использовать эти возможности, следуйте инструкциям на странице [как узнать, доступна ли служба совместной работы B2B в моем клиенте Azure для государственных организаций США?](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>Объект User в MS Graph v1 теперь включает свойства Екстерналусерстате и Екстерналусерстатечанжеддатетиме

**Тип.** Новая функция.  
**Категория службы.** B2B.  
**Возможности продукта.** B2B и B2C.
 

Свойства Екстерналусерстате и Екстерналусерстатечанжеддатетиме можно использовать для поиска приглашенных гостей B2B, которые еще не приняли приглашения, а также автоматизации сборки, например удаления пользователей, которые не приняли приглашения через некоторое количество дней. Теперь эти свойства доступны в MS Graph v1. Рекомендации по использованию этих свойств см. в разделе [тип ресурса пользователя](/graph/api/resources/user?view=graph-rest-1.0).
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Управление сеансами аутентификации в условном доступе Azure AD теперь общедоступно

**Тип.** Новая функция.  
**Категория службы:** Условный доступ  
**Возможности продукта.** Безопасность и защита удостоверений.
 
Возможности управления сеансами проверки подлинности позволяют настроить, как часто пользователи должны предоставлять учетные данные для входа и должны ли они предоставлять учетные данные после закрытия и повторного открытия браузеров, чтобы обеспечить более высокую безопасность и гибкость в среде.
 
Кроме того, управление сеансами проверки подлинности применяется только к первой факторной проверке подлинности при присоединении к Azure AD, присоединенной гибридной службе Azure AD и зарегистрированным устройствам Azure AD. Теперь управление сеансами проверки подлинности будет применяться и к MFA. Дополнительные сведения см. [в статье Настройка управления сеансами проверки подлинности с помощью условного доступа](../conditional-access/howto-conditional-access-session-lifetime.md).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Новые Федеративные приложения, доступные в коллекции приложений Azure AD — Июнь 2020

**Тип.** Новая функция.  
**Категория службы:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
В июне 2020 мы добавили в коллекцию приложений следующие 29 новых приложений с поддержкой федерации:

[Shopify Plus](../saas-apps/shopify-plus-tutorial.md), [екарда](../saas-apps/ekarda-tutorial.md), [маилгатес](../saas-apps/mailgates-tutorial.md), [буллсэйетдп](../saas-apps/bullseyetdp-tutorial.md), [ракета](../saas-apps/raketa-tutorial.md), [сегмент](../saas-apps/segment-tutorial.md), [аудитор искусственного интеллекта](https://www.mindbridge.ai/products/ai-auditor/), [побука Connect](https://app.pobu.ca/), [proto.IO](../saas-apps/proto.io-tutorial.md), [привратник](https://www.gatekeeperhq.com/), [планировщик концентратора](../saas-apps/hub-planner-tutorial.md), [ансира — панель элементов «переход на рынке](https://ansira.com/technology/channel-engagement)», [Автоматизация цифрового бизнеса в облаке](../saas-apps/ibm-digital-business-automation-on-cloud-tutorial.md), [Киси физическая безопасность](../saas-apps/kisi-physical-security-tutorial.md), [виевпоинтоне](https://team.viewpoint.com/), [интеллиженцебанк](../saas-apps/intelligencebank-tutorial.md), [Pymetrics](../saas-apps/pymetrics-tutorial.md), [ноль](https://www.teamzero.com/), [станция](https://instation.invillia.com/), [edX для интеграции SAML 2,0](../saas-apps/edx-for-business-saml-integration-tutorial.md), [mooc Office 365](https://mooc.office365-training.com/en/), SmartKargo, [PKIsigning Platform](https://platform.pkisigning.nl/), [SiteIntel](../saas-apps/siteintel-tutorial.md), [идентификатор поля](../saas-apps/field-id-tutorial.md), учебные программы [SAML](../saas-apps/curricula-saml-tutorial.md) [, Perforce](../saas-apps/smartkargo-tutorial.md) [Helix Core-Helix служба проверки подлинности](../saas-apps/perforce-helix-core-tutorial.md), [MyCompliance](https://cloud.metacompliance.com/)- [SSH](https://smallstep.com/sso-ssh/)  

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

Дополнительные сведения обо всех возможностях соединителей API см. в [статьях Использование соединителей API для настройки и расширения самостоятельной регистрации](../external-identities/api-connectors-overview.md)или [Настройка внешних удостоверений самостоятельная регистрация с помощью интеграции веб-API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin).
 
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
 
Новое делегированное разрешение Ентитлементманажемент. Read. все теперь доступно для использования с API управления назначением в бета-версии Microsoft Graph. Дополнительные сведения о доступных API см. в статье [Работа с API управления назначением Azure AD](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta).

---

### <a name="identity-protection-apis-available-in-v10"></a>API-интерфейсы защиты идентификации, доступные в версии 1.0

**Тип.** Новая функция.  
**Категория службы:** Защита идентификации  
**Возможности продукта.** Безопасность и защита удостоверений.
 
Интерфейсы API Microsoft Graph Рискюсерс и Рискдетектионс теперь общедоступны. Теперь, когда они доступны в конечной точке версии 1.0, мы приглашаем вас использовать их в рабочей среде. Дополнительные сведения см. в документации по [Microsoft Graph](/graph/api/resources/identityprotectionroot?view=graph-rest-1.0).
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Метки чувствительности к применению политик к Microsoft 365 группам теперь являются общедоступными

**Тип.** Новая функция.  
**Категория службы.** Управление группами.  
**Возможности продукта.** Совместная работа.
 

Теперь можно создавать метки чувствительности и использовать параметры меток для применения политик к Microsoft 365 группам, включая политику конфиденциальности (общедоступные или частные) и внешние пользователи. Можно создать метку с политикой конфиденциальности "частный", а для политики "внешний доступ пользователей" — запретить добавление гостевых пользователей. Когда пользователь применяет эту метку к группе, группа будет закрыта, а гостевые пользователи не смогут добавляться в эту группу. 

Метки чувствительности важны для защиты критически важных для бизнеса данных и позволяют управлять группами в нужном масштабе в соответствии с требованиями и безопасным способом. Рекомендации по использованию меток конфиденциальности см. в разделе [Назначение меток чувствительности группам Microsoft 365 в Azure Active Directory (Предварительная версия)](../users-groups-roles/groups-assign-sensitivity-labels.md).
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>Обновления для поддержки Microsoft Identity Manager для клиентов Azure AD Premium

**Тип.** Измененная функция.  
**Категория службы:** Microsoft Identity Manager  
**Возможности продукта:** Управление жизненным циклом удостоверений
 
Поддержка Azure теперь доступна для компонентов интеграции Azure AD Microsoft Identity Manager 2016 до окончания расширенной поддержки Microsoft Identity Manager 2016. Дополнительные сведения см. в статье [Поддержка обновлений для Azure AD Premium клиентов с помощью Microsoft Identity Manager](/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers).

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>Увеличивается использование условий членства в группах в конфигурации утверждений единого входа.

**Тип.** Измененная функция.  
**Категория службы:** Корпоративные приложения  
**Возможности продукта.** Единый вход.
 
Ранее количество групп, которые можно было использовать при условии изменения утверждений на основе членства в группе в пределах одной конфигурации приложения, было ограничено 10. Использование условий членства в группах в конфигурации утверждений SSO теперь увеличилось до 50 групп. Дополнительные сведения о настройке утверждений см. в статье [Настройка утверждений единого входа для корпоративных приложений](../develop/active-directory-saml-claims-customization.md#emitting-claims-based-on-conditions). 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>Включение базового форматирования для компонента текста страницы входа в фирменной символике компании.

**Тип.** Измененная функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Проверка подлинности пользователя
 
Функция фирменной символики компании в процессе входа в Azure AD или Microsoft 365 была обновлена, чтобы позволить клиенту добавлять гиперссылки и простое форматирование, включая полужирный шрифт, подчеркивание и курсив. Рекомендации по использованию этой функции см. в статье [Добавление фирменной символики на страницу входа Azure Active Directory вашей организации](./customize-branding.md).

---

### <a name="provisioning-performance-improvements"></a>Повышение производительности при подготовке

**Тип.** Измененная функция.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта:** Управление жизненным циклом удостоверений
 
Служба подготовки была обновлена, чтобы сократить время выполнения [добавочного цикла](../app-provisioning/how-provisioning-works.md#incremental-cycles) . Это означает, что пользователи и группы будут подготавливаться к приложениям быстрее, чем раньше. Все новые задания подготовки, созданные после 6/10/2020, будут автоматически использовать преимущества повышения производительности. Все приложения, настроенные для подготовки до 6/10/2020, потребуется перезапустить один раз после 6/10/2020, чтобы воспользоваться преимуществами повышения производительности. 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>Объявление о нерекомендуемой четности ADAL и MS Graph

**Тип.** Прекращение поддержки.  
**Категория службы:** недоступно  
**Возможности продукта:** Управление жизненным циклом устройств

Теперь, когда доступны библиотеки проверки подлинности Майкрософт (MSAL), мы больше не будем добавлять новые функции в библиотеки Azure Active Directory проверки подлинности (ADAL) и завершать исправления безопасности до 30 июня 2022. Дополнительные сведения о миграции в MSAL см. в статье [Миграция приложений в библиотеку проверки подлинности Майкрософт (MSAL)](../develop/msal-migration.md).

Кроме того, мы завершили работу, чтобы сделать все функции Graph Azure AD доступными через MS Graph. Таким образом, API Graph Azure AD получат только исправления бугфикс и безопасности до 30 июня 2022 г. Дополнительные сведения см. [в статье обновление приложений для использования библиотеки проверки подлинности Майкрософт и Microsoft Graph API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363) .
 
---
 
## <a name="may-2020"></a>Май 2020 г.

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
 

Мы делаем следующие изменения в уведомлениях по электронной почте для Cloud MFA:

Уведомления по электронной почте будут отправляться со следующего адреса: azure-noreply@microsoft.com и msonlineservicesteam@microsoftonline.com . Мы обновляем содержимое сообщений электронной почты с оповещениями о мошенничестве, чтобы лучше указать необходимые действия для разблокирования использования.

---

### <a name="new-self-service-sign-up-for-users-in-federated-domains-who-cant-access-microsoft-teams-because-they-arent-synced-to-azure-active-directory"></a>Новая самостоятельная регистрация для пользователей в федеративных доменах, которые не могут получить доступ к Microsoft Teams, так как они не синхронизированы с Azure Active Directory.

**Тип.** Планирование изменений.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Проверка подлинности пользователя
 

В настоящее время пользователи, которые находятся в доменах, Федеративных в Azure AD, но не синхронизированы с клиентом, не могут получить доступ к командам. Начиная с конца июня, эта новая возможность позволит им сделать это путем расширения существующей функции регистрации электронной почты с проверкой подлинности. Это позволит пользователям, которые могут войти в федеративный IdP, но у кого еще нет объекта пользователя в Azure ID, автоматически создать пользовательский объект и пройти проверку подлинности для команд. Их объект «пользователь» будет помечен как «самостоятельная регистрация». Это расширение существующей возможности для проверки электронной почты с возможностью самостоятельной регистрации, которое могут выполнять пользователи в управляемых доменах и которыми можно управлять с помощью одного и того же флага. Это изменение будет выполнено в течение следующих двух месяцев. Ознакомьтесь с обновлениями документации [здесь](../users-groups-roles/directory-self-service-signup.md).
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>Предстоящее исправление. документ обнаружения OIDC для облака Azure для государственных организаций обновляется для ссылки на правильные конечные точки Graph.

**Тип.** Планирование изменений.  
**Категория службы.** Национальные облака.  
**Возможности продукта:** Проверка подлинности пользователя
 
Начиная с июня, документ OIDC Discovery, [а также протокол OpenID Connect Connect](../develop/v2-protocols-oidc.md) в [облачной конечной точке Azure для государственных организаций](../develop/authentication-national-cloud.md) (login.microsoftonline.US) начинает возвращать корректную конечную точку [национального облака](/graph/deployments) ( https://graph.microsoft.us или, в зависимости от https://dod-graph.microsoft.us) предоставленного клиента).  В настоящее время он предоставляет неправильное поле конечной точки графа (graph.microsoft.com) "msgraph_host".  

Это исправление ошибки будет сведено постепенно в течение приблизительно 2 месяцев.  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Пользователи Azure для государственных организаций больше не смогут входить в login.microsoftonline.com

**Тип:** Планирование изменений  
**Категория службы.** Национальные облака.  
**Возможности продукта:** Проверка подлинности пользователя
 
В 1 июня 2018 г. Официальный центр Azure Active Directory (Azure AD) для государственных организаций изменен с https://login-us.microsoftonline.com на https://login.microsoftonline.us . Если вы владеете приложением в клиенте Azure для государственных организаций, необходимо обновить приложение для входа пользователей в систему в конечной точке. US.

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
 
ИТ – администраторы могут начать использовать новую роль гибридного администратора в качестве наименее привилегированной роли для настройки подготовки облака Azure Адконнект. С этой новой ролью вам больше не нужно использовать роль глобального администратора для настройки и настройки подготовки облака. [Подробнее](../users-groups-roles/roles-delegate-by-task.md#connect).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Новые Федеративные приложения, доступные в коллекции приложений Azure AD — 2020 мая

**Тип.** Новая функция.  
**Категория службы:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
В мае 2020 мы добавили следующие 36 новых приложений в коллекцию приложений с поддержкой федерации:

[Маула](https://moula.com.au/pay/merchants), [сурвэйпал](https://www.surveypal.com/app), [Kbot365](https://www.konverso.ai/virtual-assistant-digital-workplace/), [Такклебокс](http://www.tacklebox.app/), [Пауэл Teams](https://powell-software.com/en/powell-teams-en/), [талентсофт Assistant](https://msteams.talent-soft.com/), [ASC Record Insights](https://teams.asc-recording.app/product), [GO1](https://www.go1.com/), [B-](https://b-engaged.se/)on, [компетелла Contact Center Workgroup](http://www.competella.com/), [Асите](http://www.asite.com/), [удостоверение имажесофт](https://identity.imagesoftinc.com/), [Моя ибисворлд](https://identity.imagesoftinc.com/), [Suite](../saas-apps/insuite-tutorial.md), [Управление процессами](../saas-apps/change-process-management-tutorial.md), [циара CX Assurance](../saas-apps/cyara-cx-assurance-platform-tutorial.md), [интеллектуальное глобальное управление](../saas-apps/smart-global-governance-tutorial.md), [прези](../saas-apps/prezi-tutorial.md), [MAPBOX](../saas-apps/mapbox-tutorial.md), [Корпоративная платформа служб Datava](../saas-apps/datava-enterprise-service-platform-tutorial.md), [замысловатое](../saas-apps/whimsical-tutorial.md), [Trelica](../saas-apps/trelica-tutorial.md), [EasySSO для Confluence](../saas-apps/easysso-for-confluence-tutorial.md), [EasySSO для BitBucket](../saas-apps/easysso-for-bitbucket-tutorial.md), [EasySSO для Bamboo](../saas-apps/easysso-for-bamboo-tutorial.md) [, torii, Axiad](../saas-apps/torii-tutorial.md)Cloud [,, ColorTokens](../saas-apps/humanage-tutorial.md), [ZTNA,](../saas-apps/colortokens-ztna-tutorial.md) [все домашние CRM](../saas-apps/anyone-home-crm-tutorial.md) [ice Contact Center](../saas-apps/ice-contact-center-tutorial.md) , [CCH](../saas-apps/askspoke-tutorial.md) [,](../saas-apps/sharevault-tutorial.md) [Tagetik](../saas-apps/vyond-tutorial.md) [, ShareVault](../saas-apps/textexpander-tutorial.md). [Axiad Cloud](../saas-apps/axiad-cloud-tutorial.md) [CCH Tagetik](../saas-apps/cch-tagetik-tutorial.md)

Документацию по всем приложениям можно также найти здесь https://aka.ms/AppsTutorial .

Чтобы получить список приложений в коллекции приложений Azure AD, ознакомьтесь с подробными сведениями https://aka.ms/AzureADAppRequest .

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>Режим "только отчет" для условного доступа теперь является общедоступным

**Тип.** Новая функция.  
**Категория службы:** Условный доступ  
**Возможности продукта.** Безопасность и защита удостоверений.

[Режим "только отчет" для условного доступа Azure AD](../conditional-access/concept-conditional-access-report-only.md) позволяет оценить результат политики без применения элементов управления доступом. Вы можете протестировать политики только для отчетов в Организации и понять их влияние, прежде чем включать их, делая развертывание более безопасным и легким. В течение последних нескольких месяцев мы видели строгое внедрение режима только для отчетов — пользователи 26M уже находятся в области политики только для отчетов. С объявлением сейчас новые политики условного доступа Azure AD будут создаваться в режиме «только отчет» по умолчанию. Это означает, что вы можете отслеживать влияние политик с момента их создания. А для тех, кто использует API-интерфейсы MS Graph, также можно [управлять политиками только для отчетов программным способом](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta) . 

---

### <a name="self-service-sign-up-for-guest-users"></a>Самостоятельная регистрация для гостевых пользователей

**Тип.** Новая функция.  
**Категория службы.** B2B.  
**Возможности продукта.** B2B и B2C.
 
С помощью внешних удостоверений в Azure AD можно разрешить пользователям за пределами организации получать доступ к вашим приложениям и ресурсам, одновременно обеспечивая им возможность входа с использованием любого предпочтительного идентификатора. При совместном использовании приложения с внешними пользователями вы не всегда можете знать заранее, кому потребуется к нему доступ. С помощью [самостоятельной регистрации](../external-identities/self-service-sign-up-overview.md)вы можете включить гостевых пользователей для регистрации и получения гостевой учетной записи для бизнес-приложений. Последовательность регистрации может быть создана и настроена для поддержки удостоверений Azure AD и социальных сетей. Вы также можете получить дополнительные сведения о пользователе во время регистрации.

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Книга "аналитика и отчеты" условного доступа является общедоступной

**Тип.** Новая функция.  
**Категория службы:** Условный доступ  
**Возможности продукта.** Безопасность и защита удостоверений.

[Книга Insights и Reporting](../conditional-access/howto-conditional-access-insights-reporting.md) предоставляет администраторам сводное представление условного доступа Azure AD в своем клиенте. Возможность выбора отдельной политики позволяет администраторам лучше понять, что делает каждая политика, и отслеживать любые изменения в режиме реального времени. Книга отправляет данные, хранящиеся в Azure Monitor, которые можно настроить через несколько минут, [следуя этим инструкциям](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). Чтобы сделать панель мониторинга более обнаруживаемой, мы перенесли ее на новую вкладку Insights and Reports в меню условного доступа Azure AD.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Колонка сведений о политике для условного доступа в общедоступной предварительной версии

**Тип.** Новая функция.  
**Категория службы:** Условный доступ  
**Возможности продукта.** Безопасность и защита удостоверений.

В [колонке новая информация о политике](../conditional-access/troubleshoot-conditional-access.md) отображаются назначения, условия и элементы управления, удовлетворяющие условиям оценки политики условного доступа. Вы можете получить доступ к колонке, выбрав строку на вкладках условный доступ или только отчет на вкладке сведения о входе.

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
 
Теперь доступна поддержка создания и настройки приложения из коллекции Azure AD с помощью API-интерфейсов MS Graph. Если необходимо настроить единый вход на основе SAML для нескольких экземпляров приложения, сэкономьте время с помощью Microsoft Graph API-интерфейсов для [автоматизации настройки единого входа на основе SAML](/graph/application-saml-sso-configure-api).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Новые соединители подготовки в коллекции приложений Azure AD — Май 2020

**Тип.** Новая функция.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
Теперь вы можете автоматизировать процессы создания, обновление и удаление учетных записей пользователей для следующих новых интегрированных приложений:

* [8x8](../saas-apps/8x8-provisioning-tutorial.md)
* [Juno Journey](../saas-apps/juno-journey-provisioning-tutorial.md)
* [MediusFlow](../saas-apps/mediusflow-provisioning-tutorial.md)
* [Использование New Relic с разбивкой по организацией](../saas-apps/new-relic-by-organization-provisioning-tutorial.md)
* [Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-infratstructure-console-provisioning-tutorial.md)

Дополнительные сведения об усилении защиты организации с помощью автоматизированной подготовки учетных записей пользователей см. в статье [Автоматическая подготовка пользователей для приложений SaaS в Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="saml-token-encryption-is-generally-available"></a>Шифрование токена SAML является общедоступным

**Тип.** Новая функция.  
**Категория службы:** Корпоративные приложения  
**Возможности продукта.** Единый вход.
 
[Шифрование токенов SAML](../manage-apps/howto-saml-token-encryption.md) позволяет настроить приложения для получения зашифрованных утверждений SAML. Теперь эта функция доступна во всех облаках.
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>Утверждения имени группы в маркерах приложений являются общедоступными

**Тип.** Новая функция.  
**Категория службы:** Корпоративные приложения  
**Возможности продукта.** Единый вход.
 
Утверждения группы, выданные в маркере, теперь можно ограничить только теми группами, которые назначены приложению.  Это особенно важно, когда пользователи являются членами большого количества групп, и существует риск превышения предельного размера маркера. Благодаря этой новой возможности возможность [добавлять имена групп в токены](../hybrid/how-to-connect-fed-group-claims.md) общедоступна.
 
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
 
Проверка издателя (Предварительная версия) помогает администраторам и конечным пользователям понять подлинность разработчиков приложений, которые интегрируются с платформой идентификации Майкрософт. Дополнительные сведения см. в статье [Проверка издателя (Предварительная версия)](../develop/publisher-verification-overview.md).
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>Поток кода авторизации для одностраничных приложений

**Тип:** Измененная **Категория службы компонентов:** возможность проверки подлинности **продукта:** взаимодействие с разработчиками

Из-за современных [сторонних ограничений для файлов cookie браузера, таких как Safari ITP](../develop/reference-third-party-cookies-spas.md), одностраничные приложения придется использовать поток кода авторизации, а не неявный поток для поддержки единого входа. MSAL.js версии 2. x теперь будет поддерживать поток кода авторизации. В этом случае соответствующие обновления портал Azure, поэтому вы можете обновить SPA, чтобы он имел тип "Spa", и использовать поток кода проверки подлинности. Инструкции см. в разделе [Краткое руководство. вход пользователей и получение маркера доступа в JavaScript Spa с помощью потока кода проверки подлинности](../develop/quickstart-v2-javascript-auth-code.md).

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>Улучшенная фильтрация для устройств в общедоступной предварительной версии

**Тип:** Измененная функция   
**Категория службы:** **Возможности продукта** для управления устройствами: Управление жизненным циклом устройств
 
Ранее можно было использовать только фильтры "включено" и "Дата действия". Теперь можно [отфильтровать список устройств по большему числу свойств](../devices/device-management-azure-portal.md#device-list-filtering-preview), включая тип ОС, тип объединения, соответствие и многое другое. Эти дополнения должны упростить поиск конкретного устройства.

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

Оценка непрерывного доступа — это новая функция безопасности, которая обеспечивает принудительное применение политик в режиме реального времени к проверяющим сторонам, которые потребляют маркеры доступа Azure AD при возникновении событий в Azure AD (например, удаление учетной записи пользователя). Мы разносим эту функцию в первую очередь для команд и клиентов Outlook. Дополнительные сведения см. в нашем [блоге](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) и  [документации](./concept-fundamentals-continuous-access-evaluation.md).

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>Вход в SMS: работники Фирстлине могут входить в приложения, поддерживающие Azure AD, с помощью номера телефона и пароля.

**Тип.** Новая функция.

**Категория службы.** Проверки подлинности (имена входа).

**Возможности продукта:** Проверка подлинности пользователя

Office запускает серию мобильных бизнес-приложений, предназначенных для нетрадиционных организаций, и сотрудников в крупных организациях, которые не используют электронную почту в качестве основного метода связи. Эти приложения предназначены для сотрудников оказался, сотрудников без службы, агентов полей или сотрудников розничной торговли, которые могут не получить адрес электронной почты от своего работодателя, получить доступ к компьютеру или к нему. Этот проект позволит этим сотрудникам входить в бизнес-приложения, вводя номер телефона и переносе код. Дополнительные сведения см. в документации [администратора](../authentication/howto-authentication-sms-signin.md) и в [документации для конечных пользователей](../user-help/sms-sign-in-explainer.md).

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>Пригласите внутренних пользователей использовать службу совместной работы B2B

**Тип.** Новая функция.

**Категория службы.** B2B.

**Возможности продукта:**

Мы расширяем возможности приглашения B2B, чтобы разрешить приглашать существующие внутренние учетные записи для использования учетных данных службы совместной работы B2B. Это делается путем передачи объекта пользователя в API приглашения, а также стандартных параметров, таких как приглашенный адрес электронной почты. Идентификатор объекта пользователя, имя участника-пользователя, членство в группе, назначение приложения и т. п. остаются неизменными, но в дальнейшем они будут использовать B2B для проверки подлинности с учетными данными домашнего клиента вместо внутренних учетных данных, которые они использовали перед приглашением. Дополнительные сведения см. в [документации](../external-identities/invite-internal-users.md).

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>Режим "только отчет" для условного доступа теперь является общедоступным

**Тип.** Новая функция.

**Категория службы:** Условный доступ

**Возможности продукта.** Безопасность и защита удостоверений.

[Режим "только отчет" для условного доступа Azure AD](../conditional-access/concept-conditional-access-report-only.md) позволяет оценить результат политики без применения элементов управления доступом. Вы можете протестировать политики только для отчетов в Организации и понять их влияние, прежде чем включать их, делая развертывание более безопасным и легким. За последние несколько месяцев мы наблюдали строгое внедрение режима только для отчетов, 26M пользователей уже в области политики только для отчетов. Благодаря этому объявлению новые политики условного доступа Azure AD будут создаваться в режиме «только отчет» по умолчанию. Это означает, что вы можете отслеживать влияние политик с момента их создания. И для тех, кто использует API-интерфейсы MS Graph, можно также [управлять политиками только для отчетов программным способом](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta). 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Книга "аналитика и отчеты" условного доступа является общедоступной

**Тип.** Новая функция.

**Категория службы:** Условный доступ

**Возможности продукта.** Безопасность и защита удостоверений.

[Книга "аналитика и отчеты](../conditional-access/howto-conditional-access-insights-reporting.md) " условного доступа предоставляет администраторам сводное представление условного доступа Azure AD в своем клиенте. Возможность выбора отдельной политики позволяет администраторам лучше понять, что делает каждая политика, и отслеживать любые изменения в режиме реального времени. Книга отправляет данные, хранящиеся в Azure Monitor, которые можно настроить через несколько минут, [следуя этим инструкциям](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). Чтобы сделать панель мониторинга более обнаруживаемой, мы перенесли ее на новую вкладку Insights and Reports в меню условного доступа Azure AD.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Колонка сведений о политике для условного доступа в общедоступной предварительной версии

**Тип.** Новая функция.

**Категория службы:** Условный доступ

**Возможности продукта.** Безопасность и защита удостоверений.

В [колонке новая информация о политике](../conditional-access/troubleshoot-conditional-access.md) отображаются назначения, условия и элементы управления, которые были удовлетворены во время оценки политики условного доступа. Вы можете получить доступ к колонке, выбрав строку на вкладках **Условный доступ** или **только отчет** на вкладке сведения о входе.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Новые Федеративные приложения, доступные в Azure AD App Gallery — Апрель 2020

**Тип.** Новая функция.

**Категория службы:** Корпоративные приложения

**Возможности продукта.** Интеграция с решениями сторонних производителей.

В апреле 2020 мы добавили в коллекцию приложений следующие 31 новые приложения с поддержкой федерации: 

[Приложения синкропул](https://www.sincropool.com/), [смартдб](https://hibiki.dreamarts.co.jp/smartdb/trial/), [float](../saas-apps/float-tutorial.md), [LMS365](https://lms.365.systems/), [IWT](../saas-apps/iwt-procurement-suite-tutorial.md), [лунни](https://lunni.fi/) [,](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/) [еасиссо для JIRA](../saas-apps/easysso-for-jira-tutorial.md), [Virtual Training Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft), [Meraki](https://app.mover.io/login) [,](../saas-apps/meraki-dashboard-tutorial.md)честно говоря, Ally, Дутифлов [Speaker Engage](https://speakerengage.com/login.php) [Trend Micro Web Security(TMWS)](https://review.docs.microsoft.com/azure/active-directory/saas-apps/trend-micro-tutorial) [,](../saas-apps/alertmedia-tutorial.md)алертмедиа [Microsoft 365 люди](../saas-apps/gr8-people-tutorial.md) [,](../saas-apps/honestly-tutorial.md) [gr8](../saas-apps/pendo-tutorial.md), [DutyFlow](https://app.dutyflow.nl/) [пендо,](../saas-apps/ally-tutorial.md) [гармонии](../saas-apps/harmony-tutorial.md), [хигхграунд Solutions](../saas-apps/timetabling-solutions-tutorial.md), тиметаблинг [Nitro Productivity Suite](../saas-apps/nitro-productivity-suite-tutorial.md) [щелкните](../saas-apps/synchronet-click-tutorial.md), [Empower](https://www.made-in-office.com/en/) [, синчронет](../saas-apps/mongodb-cloud-tutorial.md) [изменить облако](../saas-apps/fortes-change-cloud-tutorial.md) [,](../saas-apps/tickitlms-learn-tutorial.md) [Fortes, litmus](../saas-apps/litmus-tutorial.md) [, GroupTalk,](../saas-apps/highground-tutorial.md)Frontify Cloud, MongoDB [, TickitLMS](https://recorder.grouptalk.com/) [, Coco](../saas-apps/frontify-tutorial.md).

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>Microsoft Graph поддержка разностных запросов для oAuth2PermissionGrant, доступной для общедоступной предварительной версии

**Тип.** Новая функция.

**Категория службы:** MS Graph

**Возможности продукта.** Возможности для разработчиков

Разностный запрос для oAuth2PermissionGrant доступен для общедоступной предварительной версии. Теперь можно отслеживаниь изменений без постоянного опроса Microsoft Graph. [Подробнее.](/graph/api/oAuth2PermissionGrant-delta?tabs=http&view=graph-rest-beta)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>Microsoft Graph поддержка разностных запросов для контакта Организации в общем доступе

**Тип.** Новая функция.

**Категория службы:** MS Graph

**Возможности продукта.** Возможности для разработчиков

Разностный запрос для контактов Организации общедоступен! Теперь вы можете отслеживание изменений в рабочих приложениях без постоянного опроса Microsoft Graph. Замените существующий код, который постоянно опрашивает Оргконтакт данные по разностному запросу для значительного повышения производительности. [Подробнее.](/graph/api/orgcontact-delta?tabs=http&view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>Microsoft Graph поддержка разностных запросов для приложения в общем доступе

**Тип.** Новая функция.

**Категория службы:** MS Graph

**Возможности продукта.** Возможности для разработчиков

Разностный запрос для приложений общедоступен! Теперь вы можете отслеживание изменений в рабочих приложениях без постоянного опроса Microsoft Graph. Замените существующий код, который постоянно опрашивает данные приложения по разностному запросу, чтобы значительно повысить производительность. [Подробнее.](/graph/api/application-delta?view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>Microsoft Graph поддержка разностных запросов для административных единиц, доступных для общедоступной предварительной версии

**Тип.** Новая функция.

**Категория службы:** MS Graph

**Возможности продукта:** Для общедоступной предварительной версии доступен разностный запрос разработчиков для административных единиц. Теперь можно отслеживаниь изменений без постоянного опроса Microsoft Graph. [Подробнее.](/graph/api/administrativeunit-delta?tabs=http&view=graph-rest-beta)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>Управление номерами телефонов для проверки подлинности и многое другое в новых Microsoft Graph бета-версиях API

**Тип.** Новая функция.

**Категория службы:** MS Graph

**Возможности продукта.** Возможности для разработчиков

Эти API являются ключевым средством для управления методами проверки подлинности пользователей. Теперь вы можете программными средствами предварительно регистрировать и администрировать средства проверки подлинности, используемые для MFA и самостоятельного сброса пароля (SSPR). Это одна из наиболее востребованных функций в Azure MFA, SSPR и Microsoft Graphных пробелах. Новые интерфейсы API, которые мы выпустили в этой волне, дают вам возможность:

- Чтение, добавление, обновление и удаление телефонов для проверки подлинности пользователя
- Сброс пароля пользователя
- Включение и отключение сервера SMS-Вход

Дополнительные сведения см. в статье [Общие сведения об API методов проверки подлинности Azure AD](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

---

### <a name="administrative-units-public-preview"></a>Общедоступная Предварительная версия административных единиц

**Тип.** Новая функция.

**Категория службы:** Роли Azure AD

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

**Категория службы:** Роли Azure AD

**Возможность продукта.** Контроль доступа.

**Администратор принтера**. пользователи с этой ролью могут регистрировать принтеры и управлять всеми аспектами всех конфигураций принтеров в универсальном решении печати (Майкрософт), включая параметры соединителя универсального принтера. Они могут предоставлять согласие на все запросы делегирования разрешений на печать. Администраторы принтеров также имеют доступ к отчетам о печати. 

**Специалист по печати**. пользователи с этой ролью могут регистрировать принтеры и управлять состоянием принтера в универсальном решении печати (Майкрософт). Им также доступно чтение всей информации о соединителях. Основные задачи. не удается выполнить типографию. устанавливаются разрешения пользователей на принтерах и совместное использование принтеров. [Подробнее.](../users-groups-roles/directory-assign-admin-roles.md#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Встроенная роль администратора гибридной идентификации

**Тип.** Новая функция.

**Категория службы:** Роли Azure AD

**Возможность продукта.** Контроль доступа.

Пользователи с этой ролью могут включать, настраивать и контролировать службы и параметры, связанные с использованием гибридных удостоверений в Azure AD. Эта роль предоставляет возможность настроить Azure AD для использования одного из трех поддерживаемых методов проверки подлинности&#8212;синхронизации хэшей паролей (PHS), сквозной проверки подлинности (PTA) или Федерации (AD FS или стороннего поставщика Федерации) &#8212;и развертывания связанной локальной инфраструктуры для их включения. Локальная инфраструктура включает в себя подготовку и агенты PTA. Эта роль дает возможность использовать простой единый вход (S-SSO), обеспечивающий беспроблемную проверку подлинности на устройствах, работающих не под управлением Windows 10, или на компьютерах не с ОС Windows Server 2016. Кроме того, эта роль предоставляет возможность просматривать журналы входа и получать доступ к службе работоспособности и аналитики для целей мониторинга и устранения неполадок. [Подробнее.](../users-groups-roles/directory-assign-admin-roles.md#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Встроенная роль администратора сети

**Тип.** Новая функция.

**Категория службы:** Роли Azure AD

**Возможность продукта.** Контроль доступа.

Пользователи с этой ролью могут просматривать рекомендации по архитектуре периметра сети от корпорации Майкрософт, основанные на данных телеметрии сети из расположений пользователей. Производительность сети для Microsoft 365 зависит от тщательной архитектуры периметра сети клиентов предприятия, которая обычно зависит от местонахождения пользователя. Данная роль позволяет изменять обнаруженные расположения пользователей, настраивать их сетевые параметры и получать более оптимальную телеметрию и рекомендации по проектированию. [Подробнее.](../users-groups-roles/directory-assign-admin-roles.md#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Групповые операции и загрузки на портале администрирования Azure AD

**Тип.** Новая функция.

**Категория службы:** Управление пользователями

**Возможности продукта:** Каталоги

Теперь вы можете выполнять групповые действия для пользователей и групп в Azure AD, загружая CSV-файл на портале администрирования Azure AD. Вы можете создавать пользователей, удалять пользователей и приглашать гостевых пользователей. Можно добавлять и удалять членов из группы.

Вы также можете загрузить списки ресурсов Azure AD с портала администрирования Azure AD. Вы можете скачать список пользователей в каталоге, список групп в каталоге и членов определенной группы.

Дополнительные сведения см. в следующих примерах.

- [Создание пользователей](../users-groups-roles/users-bulk-add.md) или [приглашение гостевых пользователей](../external-identities/tutorial-bulk-invite.md)
- [Удаление пользователей](../users-groups-roles/users-bulk-delete.md) или [Восстановление удаленных пользователей](../users-groups-roles/users-bulk-restore.md)
- [Скачивание списка пользователей](../users-groups-roles/users-bulk-download.md) или [списка загрузки групп](../users-groups-roles/groups-bulk-download.md)
- [Добавление (импорт) элементов](../users-groups-roles/groups-bulk-import-members.md) или [Удаление участников](../users-groups-roles/groups-bulk-remove-members.md) или [скачивание списка членов](../users-groups-roles/groups-bulk-download-members.md) группы

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

Мы обновили интерфейс рецензента для проверок доступа Azure AD на портале "Мои приложения". В конце апреля ваши рецензенты, вошедшие в систему проверки доступа Azure AD, увидят баннер, который позволит им попробовать обновленный интерфейс в разделе "мой доступ". Обратите внимание, что обновленная версия проверки доступа предоставляет те же функциональные возможности, что и текущий интерфейс, но с улучшенным пользовательским интерфейсом на основе новых возможностей, позволяющих пользователям работать эффективно. [Дополнительные сведения об обновленном интерфейсе](../governance/perform-access-review.md)см. здесь. Эта общедоступная Предварительная версия будет доступна до конца июля 2020. В конце июля рецензенты, которые не выбрали предварительную версию, будут автоматически направлены к моему доступу для выполнения проверок доступа. Если вы хотите, чтобы рецензенты постоянно перешли на предварительный просмотр в моем доступе, выполните [запрос здесь](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>Приложения для входящей подготовки и обратной записи пользователей workday теперь поддерживают новейшие версии API веб-служб Workday.

**Тип.** Измененная функция.

**Категория службы.** Подготовка приложений.

**Возможности продукта:** 

На основе отзывов клиентов мы обновили приложения для входящей подготовки пользователей и обратной записи Workday в коллекции корпоративных приложений для поддержки последних версий API-интерфейса Workday Web Services (WWS). С этим изменением клиенты могут указать версию API WWS, которую нужно использовать в строке подключения. Это дает клиентам возможность получить дополнительные атрибуты HR, доступные в выпусках Workday. Приложение обратной записи Workday теперь использует рекомендованную веб-службу Change_Work_Contact_Info Workday для преодоления ограничений Maintain_Contact_Info.

Если в строке подключения не указана версия, по умолчанию приложения для входящей передачи Workday продолжат использовать WWS v 21.1 для переключения на последние API-интерфейсы Workday для входящей подготовки пользователей. клиентам необходимо обновить строку подключения, как описано [в этом](../saas-apps/workday-inbound-tutorial.md#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles) руководстве, а также обновить выражения, используемые для атрибутов workday, как описано в [руководстве по атрибуту workday](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30). 

Чтобы использовать новый API для обратной записи, в приложении подготовки обратной записи Workday не требуется никаких изменений. На стороне Workday убедитесь, что учетная запись пользователя системы интеграции Workday (ИСУ) имеет разрешения на вызов бизнес-процесса Change_Work_Contact, как описано в разделе "учебник" [Настройка разрешений политики безопасности бизнес-процессов](../saas-apps/workday-inbound-tutorial.md#configuring-business-process-security-policy-permissions). 

Мы обновили это [руководство](../saas-apps/workday-inbound-tutorial.md) , чтобы отразить новую поддержку версии API.

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>Пользователи с ролью доступа по умолчанию теперь находятся в области для подготовки

**Тип.** Измененная функция.

**Категория службы.** Подготовка приложений.

**Возможности продукта:** Управление жизненным циклом удостоверений

Исторически пользователи с ролью доступа по умолчанию выходят за пределы области подготовки. Мы слышали Отзывы о том, что клиенты хотят, чтобы пользователи с этой ролью были в области подготовки. По состоянию на 16 апреля 2020 г. все новые конфигурации подготовки позволяют подготовить пользователей с ролью доступа по умолчанию. Постепенно изменится поведение существующих конфигураций подготовки для поддержки подготовки пользователей с помощью этой роли. [Подробнее.](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

---

### <a name="updated-provisioning-ui"></a>Обновлен пользовательский интерфейс подготовки

**Тип.** Измененная функция.

**Категория службы.** Подготовка приложений.

**Возможности продукта:** Управление жизненным циклом удостоверений

Мы обновили процесс подготовки, чтобы создать более специализированное представление управления. При переходе к колонке подготовка для корпоративного приложения, которое уже было настроено, вы сможете легко отслеживать ход подготовки и управлять такими действиями, как запуск, остановка и перезапуск. [Подробнее.](../app-provisioning/configure-automatic-user-provisioning-portal.md)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>Проверка правила динамической группы теперь доступна для общедоступной предварительной версии

**Тип.** Измененная функция.

**Категория службы.** Управление группами.

**Возможности продукта.** Совместная работа.

Azure Active Directory (Azure AD) теперь предоставляет средства для проверки динамических правил групп. На вкладке **Проверка правил** можно проверить динамическое правило по образцу членов группы, чтобы убедиться, что правило работает должным образом. При создании или обновлении динамических групп правил администраторам необходимо определить, будет ли пользователь или устройство членом группы. Это помогает определить, соответствует ли пользователь или устройство условиям правила, и помогает устранять неполадки, когда членство не ожидается.

Дополнительные сведения см. [в статье Проверка правила членства в динамической группе (Предварительная версия)](../users-groups-roles/groups-dynamic-rule-validation.md).

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>Оценка безопасности удостоверения — обновления по умолчанию и действия улучшения MFA

**Тип.** Измененная функция.

**Категория службы:** недоступно

**Возможности продукта.** Безопасность и защита удостоверений.

**Поддержка параметров безопасности по умолчанию для действий по улучшению Azure AD:** Microsoft Security Score будет обновлять действия по улучшению для [обеспечения безопасности по умолчанию в Azure AD](./concept-fundamentals-security-defaults.md), что упрощает защиту Организации с помощью предварительно настроенных параметров безопасности для распространенных атак. Это повлияет на следующие действия по улучшению:

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

Эти новые действия по улучшению требуют регистрации пользователей или администраторов для многофакторной проверки подлинности (MFA) в каталоге и создания правильного набора политик, соответствующих потребностям Организации. Основная цель — обеспечить гибкость, при этом все пользователи и администраторы могут проходить проверку подлинности с использованием нескольких факторов или запросов на проверку удостоверения на основе рисков. Это может иметь форму с несколькими политиками, которые применяют решения с заданной областью или настройки по умолчанию (согласно состоянию на Март 16 марта), которые позволяют корпорации Майкрософт решать, когда следует выдать запрос MFA пользователям. [Узнайте больше о новых возможностях Microsoft Secure Score](/microsoft-365/security/mtp/microsoft-secure-score?view=o365-worldwide#whats-new).

---

## <a name="march-2020"></a>Март 2020 г.

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>Неуправляемые учетные записи Azure Active Directory в обновлении B2B в марте, 2021

**Тип.** Планирование изменений.  
**Категория службы.** B2B.  
**Возможности продукта.** B2B и B2C.
 
**Начиная с 31 марта 2021**г. Корпорация Майкрософт больше не будет поддерживать погашение приглашений, создавая неуправляемые учетные записи Azure Active Directory (Azure AD) и клиенты для сценариев совместной работы B2B. В процессе подготовки мы рекомендуем использовать [одноразовую проверку подлинности с помощью секретного кода](../external-identities/one-time-passcode.md).

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Пользователи с ролью доступа по умолчанию будут находиться в области подготовки

**Тип.** Планирование изменений.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта:** Управление жизненным циклом удостоверений
 
Исторически пользователи с ролью доступа по умолчанию выходят за пределы области подготовки. Мы слышали Отзывы о том, что клиенты хотят, чтобы пользователи с этой ролью были в области подготовки. Мы работаем над развертыванием изменений, чтобы все новые конфигурации подготовки могли быть подготовлены для пользователей с ролью доступа по умолчанию. Постепенно мы изменим поведение существующих конфигураций подготовки для поддержки подготовки пользователей с помощью этой роли. Никаких действий клиента не требуется. После этого изменения мы будем публиковать обновленную [документацию](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md) .

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Служба совместной работы Azure AD B2B будет доступна в Microsoft Azure, управляемых клиентами 21Vianet (Azure Китая 21Vianet).

**Тип.** Планирование изменений.  
**Категория службы.** B2B.  
**Возможности продукта.** B2B и B2C.
 
Возможности службы совместной работы Azure AD B2B будут доступны в Microsoft Azure, предоставляемых клиентами 21Vianet (Azure для Китая в Китае), что позволяет пользователям в клиенте Azure для Китая в Китае сотрудничать без проблем работать с пользователями в других клиентах Azure Китая (21Vianet). [Дополнительные сведения о службе совместной работы Azure AD B2B](/azure/active-directory/b2b/).

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Перепроектирование приглашения службы совместной работы Azure AD B2B

**Тип.** Планирование изменений.  
**Категория службы.** B2B.  
**Возможности продукта.** B2B и B2C.
 
[Сообщения электронной почты](../external-identities/invitation-email-elements.md) , отправленные службой приглашений службы совместной работы Azure AD B2B для приглашения пользователей в каталог, будут переработаны, чтобы сделать более четкими сведения о приглашении и дальнейшие действия пользователя.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>Изменения политики Хомереалмдисковери будут отображаться в журналах аудита.

**Тип.** Исправление.  
**Категория службы:** Попытк  
**Возможности продукта.** Мониторинг и отчетность.
 
Исправлена ошибка, из-за которой изменения в [политике хомереалмдисковери](../manage-apps/configure-authentication-for-federated-users-portal.md) не включались в журналы аудита. Теперь вы сможете видеть, когда и как была изменена политика и кем. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Новые Федеративные приложения, доступные в коллекции Azure AD App — 2020 марта

**Тип.** Новая функция.  
**Категория службы:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
В марте 2020 мы добавили следующие 51 новых приложений с поддержкой Федерации в коллекцию приложений: 

[Cisco AnyConnect](../saas-apps/cisco-anyconnect.md), [Zoho One Китая](../saas-apps/zoho-one-china-tutorial.md), [Плусплус](https://test.plusplus.app/auth/login/azuread-outlook/), [Profit.co SAML](../saas-apps/profitco-saml-app-tutorial.md), [поставщик услуг ипоинт](../saas-apps/ipoint-service-provider-tutorial.md), [Contexxt.AI Sphere](https://contexxt-sphere.com/login), [знания by Инвиктус](../saas-apps/wisdom-by-invictus-tutorial.md), [блики для цифрового входа](https://spark-dev.pixelnebula.com/login), [Logz.IO-облачное наблюдение за инженерами](../saas-apps/logzio-cloud-observability-for-engineers-tutorial.md), [спектруму](../saas-apps/spectrumu-tutorial.md), [Биззконтакт](https://bizzcontact.app/), [елкано SSO](../saas-apps/elqano-sso-tutorial.md), [Маркетсигншаре](http://www.signshare.com/), [кросскновледже Learning Suite](../saas-apps/crossknowledge-learning-suite-tutorial.md), [Netvision Compas](../saas-apps/netvision-compas-tutorial.md), [концентратор FCM](../saas-apps/fcm-hub-tutorial.md), [RIB A/S Byggeweb мобильный](https://apps.apple.com/us/app/docia/id529058757), [GoLinks](../saas-apps/golinks-tutorial.md), [Datadog](../saas-apps/datadog-tutorial.md) [, Zscaler,](../saas-apps/zscaler-b2b-user-portal-tutorial.md)рабочий процесс, [точность](../saas-apps/lift-tutorial.md), [PlanView Enterprise One](../saas-apps/planview-enterprise-one-tutorial.md) [, WatchTeams, круговая](https://www.devfinition.com/) [,](https://demo.asterapp.io/login) [рабочие процессы](../saas-apps/skills-workflow-tutorial.md), анализ узла [Платформа IP-адресов](../saas-apps/ip-platform-tutorial.md) [,](../saas-apps/invision-tutorial.md) [Pipedrive](../saas-apps/pipedrive-tutorial.md), [демонстрационный семинар](https://app.showcaseworkshop.com/), [платформа интеграции Greenlight](../saas-apps/greenlight-integration-platform-tutorial.md), [Greenlight совместимое управление доступом](../saas-apps/greenlight-compliant-access-management-tutorial.md), [Grok обучение](../saas-apps/grok-learning-tutorial.md), [Miradore Online](https://login.online.miradore.com/), [Khoros внимание](../saas-apps/khoros-care-tutorial.md), [AskYourTeam](../saas-apps/askyourteam-tutorial.md), [TruNarrative](../saas-apps/trunarrative-tutorial.md) [, Smartwaiver, Bizagi](https://www.smartwaiver.com/m/user/sw_login.php?wms_login) [Studio для автоматизации цифровых процессов](../saas-apps/bizagi-studio-for-digital-process-automation-tutorial.md), [insuiteX](https://www.insuite.jp/) [, sybo, Britive](https://www.systexsoftware.com.tw/) [, WhosOffice](../saas-apps/britive-tutorial.md) [,](../saas-apps/whosoffice-tutorial.md) [-Days](../saas-apps/e-days-tutorial.md), Kollective [Sdn](https://portal.kollective.app/login), Witivio [Playvox](https://my.playvox.com/login), Playvox [, Korn паром 360](../saas-apps/korn-ferry-360-tutorial.md), [кафе](../saas-apps/campus-cafe-tutorial.md), [Catchpoint](../saas-apps/catchpoint-tutorial.md) [Новая точка перехвата и Code42](https://app.witivio.com/) [Node Insight](https://admin.nodeinsight.com/AADLogin.aspx) [Code42](../saas-apps/code42-tutorial.md)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Служба совместной работы Azure AD B2B, доступная в клиентах Azure для государственных организаций

**Тип.** Новая функция.  
**Категория службы.** B2B.  
**Возможности продукта.** B2B и B2C.
 
Возможности службы совместной работы Azure AD B2B теперь доступны для некоторых клиентов Azure для государственных организаций.  Чтобы узнать, есть ли у клиента возможность использовать эти возможности, следуйте инструкциям на странице [как узнать, доступна ли служба совместной работы B2B в моем клиенте Azure для государственных организаций США?](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant).

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Интеграция Azure Monitor для журналов Azure теперь доступна в Azure для государственных организаций

**Тип.** Новая функция.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.
 
Интеграция Azure Monitor с журналами Azure AD теперь доступна в Azure для государственных организаций. Вы можете направить журналы Azure AD (журналы аудита и входа) в учетную запись хранения, концентратор событий и Log Analytics. Ознакомьтесь с [подробной документацией](https://aka.ms/aadlogsinamd) , а также с [планами развертывания для создания отчетов и мониторинга](../reports-monitoring/plan-monitoring-and-reporting.md) сценариев Azure AD.

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
 
Служба подготовки Azure AD предоставляет широкий набор возможностей настройки. Клиенты должны иметь возможность сохранить конфигурацию, чтобы они могли ссылаться на них позже, или выполнить откат до известной работоспособной версии. Мы добавили возможность загрузки конфигурации подготовки в виде JSON-файла и передачи ее при необходимости. [Подробнее](../app-provisioning/export-import-provisioning-configuration.md).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (самостоятельный сброс пароля) теперь требует двух шлюзов для администраторов в Microsoft Azure, обслуживаемых 21Vianet (Azure Китая 21Vianet) 

**Тип.** Измененная функция.  
**Категория службы:** Самостоятельный сброс пароля  
**Возможности продукта.** Безопасность и защита удостоверений.
 
Ранее в Microsoft Azure, обслуживаемом 21Vianet (Azure Китая 21Vianet), администраторы, использующие самостоятельный сброс пароля (SSPR) для сброса собственных паролей, требуют только одной "шлюза" (запроса) для подтверждения их личности. В общедоступных и других национальных облаках администраторы обычно должны использовать два шлюза для подтверждения их подлинности при использовании SSPR. Но так как мы не поддерживали SMS или телефонные звонки в Azure Китая 21Vianet, мы развернули администраторам один шлюз для сброса паролей.

Мы создаем SSPRую четность функций между Azure Китая 21Vianet и общедоступным облаком. В дальнейшем администраторы должны использовать два шлюза при использовании SSPR. Будут поддерживаться SMS, телефонные звонки и уведомления и коды приложений для проверки подлинности. [Подробнее](../authentication/concept-sspr-policy.md#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>Длина пароля ограничена 256 символами

**Тип.** Измененная функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Проверка подлинности пользователя
 
Для обеспечения надежности службы Azure AD пароли пользователей теперь ограничены длиной до 256 символов. Пользователям с более длинными паролями будет предложено изменить пароль при последующем входе в систему, обратившись к администратору или с помощью функции самостоятельного сброса пароля.

Это изменение было включено 13 марта 2020 г., в 10:00 PST (18:00 UTC), а ошибка — ААДСТС 50052, Инвалидпассвордексцеедсмаксленгс. Дополнительные сведения см. в разделе [уведомление о критическом изменении](../develop/reference-breaking-changes.md#user-passwords-will-be-restricted-to-256-characters) .

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Журналы входа Azure AD теперь доступны для всех бесплатных клиентов с помощью портал Azure

**Тип.** Измененная функция.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.
 
Начиная с, клиенты, у которых есть бесплатные клиенты, могут получить доступ к [журналам входа Azure AD из портал Azure](../reports-monitoring/concept-sign-ins.md) в течение 7 дней. Ранее журналы входа были доступны только для клиентов с лицензиями Azure Active Directory Premium. После этого изменения все клиенты смогут получить доступ к этим журналам через портал.

> [!NOTE]
> Клиентам по-прежнему нужна лицензия Premium (Azure Active Directory Premium P1 или P2) для доступа к журналам входа через Microsoft Graph API и Azure Monitor.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Нерекомендуемый параметр групп на уровне каталога из групп общие параметры на портал Azure

**Тип.** Прекращение поддержки.  
**Категория службы.** Управление группами.  
**Возможности продукта.** Совместная работа.

Чтобы предоставить клиентам более гибкий способ создания групп на уровне каталога, которые лучше всего соответствуют своим потребностям, мы заменили параметр **группы уровня каталога** из **Groups**  >  раздела**Общие** параметры в портал Azure со ссылкой на [документацию по динамической группе](../users-groups-roles/groups-dynamic-membership.md). Мы улучшили документацию для включения дополнительных инструкций, чтобы администраторы могли создавать группы всех пользователей, включающие или исключающие гостевые пользователи.

---
