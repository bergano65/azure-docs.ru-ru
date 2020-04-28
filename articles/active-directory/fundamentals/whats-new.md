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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80802515"
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

## <a name="march-2020"></a>Март 2020 г.

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>Неуправляемые учетные записи Azure Active Directory в обновлении B2B в марте, 2021

**Тип.** Планирование изменений.  
**Категория службы.** B2B.  
**Возможности продукта.** B2B и B2C.
 
**Начиная с 31 марта 2021**г. Корпорация Майкрософт больше не будет поддерживать погашение приглашений, создавая неуправляемые учетные записи Azure Active Directory (Azure AD) и клиенты для сценариев совместной работы B2B. В процессе подготовки мы рекомендуем использовать [одноразовую проверку подлинности с помощью секретного кода](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode).

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
 
Служба подготовки Azure AD предоставляет широкий набор возможностей настройки. Клиенты должны иметь возможность сохранить конфигурацию, чтобы они могли ссылаться на них позже, или выполнить откат до известной работоспособной версии. Мы добавили возможность загрузки конфигурации подготовки в виде JSON-файла и передачи ее при необходимости. [Подробнее](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (самостоятельный сброс пароля) теперь требует двух шлюзов для администраторов в Microsoft Azure, обслуживаемых 21Vianet (Azure Китая 21Vianet) 

**Тип.** Измененная функция.  
**Категория службы:** Самостоятельный сброс пароля  
**Возможности продукта.** Безопасность и защита удостоверений.
 
Ранее в Microsoft Azure, обслуживаемом 21Vianet (Azure Китая 21Vianet), администраторы, использующие самостоятельный сброс пароля (SSPR) для сброса собственных паролей, требуют только одной "шлюза" (запроса) для подтверждения их личности. В общедоступных и других национальных облаках администраторы обычно должны использовать два шлюза для подтверждения их подлинности при использовании SSPR. Но так как мы не поддерживали SMS или телефонные звонки в Azure Китая 21Vianet, мы развернули администраторам один шлюз для сброса паролей.

Мы создаем SSPRую четность функций между Azure Китая 21Vianet и общедоступным облаком. В дальнейшем администраторы должны использовать два шлюза при использовании SSPR. Будут поддерживаться SMS, телефонные звонки и уведомления и коды приложений для проверки подлинности. [Подробнее](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences).

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

Пользовательские элементы управления по прежнему будут поддерживаться в предварительной версии вместе с новой структурой, пока она не станет общедоступной. На этом этапе мы предоставим клиентам время перехода на новую архитектуру. Из-за ограничений текущего подхода мы не будем подключать новые поставщики до тех пор, пока не станет доступен новый проект. Мы тесно работаем с клиентами и поставщиками и будем связываться с временной шкалой по мере приближения. [Подробнее](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

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

## <a name="october-2019"></a>Октябрь 2019 г.

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Нерекомендуемый API Идентитирискевент для обнаружения рисков защита идентификации Azure AD  

**Тип.** Планирование изменений.  
**Категория службы:** Защита идентификации  
**Возможности продукта.** Безопасность и защита удостоверений.

В ответ на Отзывы разработчиков Azure AD Premium Подписчики P2 теперь могут выполнять сложные запросы к данным обнаружения рисков защита идентификации Azure AD, используя новый API Рискдетектион для Microsoft Graph. Существующая бета-версия [идентитирискевент](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) API перестанет возвращать данные **10 января 2020 г**. Если ваша организация использует API Идентитирискевент, следует перейти на новый API Рискдетектион.

Дополнительные сведения о новом API Рискдетектион см. в [справочной документации по API обнаружения рисков](https://aka.ms/RiskDetectionsAPI).

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Поддержка прокси приложения для атрибута SameSite и Chrome 80

**Тип.** Планирование изменений.  
**Категория службы.** Прокси-сервер приложения.  
**Возможность продукта.** Контроль доступа.

Несколько недель до выпуска браузера Chrome 80 мы планируем обновить, как файлы cookie прокси приложения обрабатывают атрибут **SameSite** . В выпуске Chrome 80 любой файл cookie, не указывающий атрибут **SameSite** , будет рассматриваться так, как если бы `SameSite=Lax`он был установлен в значение.

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

В ближайшем будущем пользователи с учетными записями Azure AD больше не смогут регистрировать приложения с помощью портала регистрации приложений (apps.dev.microsoft.com) и управлять ими, а также регистрировать приложения в Регистрация приложений (устаревших) в портал Azure.

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
**Категория службы:** Корпоративные приложения  
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

Мы добавили новую страницу **«мои входы** » (https://mysignins.microsoft.com) для того, чтобы позволить пользователям вашей организации просматривать последние журналы входа, чтобы проверить любое необычное действие. Эта новая страница позволяет пользователям видеть следующее:

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
**Категория службы:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.

В октябре 2019 мы добавили следующие 35 новых приложений с поддержкой Федерации в коллекцию приложений:

[В случае аварийного перехода на мобильные устройства](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial), [Junoное путешествие](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial), [експоненср](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial), [без изменений](https://tact.ai/assistant/), [опускапита управления денежными](http://cm1.opuscapita.com/tenantname)средствами, [салестим](https://prd.salestim.io/forms), [Леарнстер](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial), [dynaTrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial), [хунчбузз](https://login.hunchbuzz.com/integrations/azure/process), [фрешворкс](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial), [Екорнелл](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial), [шифазмат](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial), [Нетскопе Cloud Security](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial), [contenting](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial), [Bindtuning](https://bindtuning.com/login), [HireVue координировать — Европа](https://www.hirevue.com/), [HireVue координировать-USOnly](https://www.hirevue.com/), [HireVue КООРДИНИРУЕТ-US](https://www.hirevue.com/), [WittyParrot база знаний](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial), [Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial), [Cambium Xirrus EasyPass Portal](https://login.xirrus.com/azure-signup), [Paylocity](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial), позаботиться [!](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial), [Teaming](https://theteamie.com/), Direction [for Teams](https://velocity.peakup.org/teams/login), [SIGNL4](https://account.signl4.com/manage), [EAB навигации Impl](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial), [ScreenMeet](https://console.screenmeet.com/), [Омега Point](https://pi.ompnt.com/), [выступление электронной почты для Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [выступление электронной почты для Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ExactCare SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial), [iHealthHome](https://ihealthnav.com/account/signin), [Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Элемент меню "консолидированная безопасность" на портале Azure AD

**Тип.** Измененная функция.  
**Категория службы:** Защита идентификации  
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
**Возможности продукта:** Управление жизненным циклом удостоверений

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
