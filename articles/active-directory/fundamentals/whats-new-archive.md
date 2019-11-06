---
title: Архивировать для новых возможностей Azure Active Directory? | Документация Майкрософт
description: Заметки о выпуске "Новые возможности" в разделе "Обзор" для конкретного набора содержимого отображаются на протяжении шести месяцев. Через шесть месяцев элементы удаляются из основной статьи и помещаются в статью архива.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fcf1e5966be7708e7c6278839cbfbe5d65cdffa
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73149127"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Архивировать для новых возможностей Azure Active Directory?

В статье [заметки о выпуске](whats-new.md) , посвященной основным новинкам Azure Active Directory? содержатся обновления за последние шесть месяцев, а в этой статье содержатся все более старые сведения.

Новые возможности в Azure Active Directory Заметки о выпуске содержат следующие сведения:

- Последние выпуски.
- Известные проблемы
- Исправления ошибок
- Нерекомендуемые функции.
- Планы по изменениям.

---

## <a name="april-2019"></a>Апрель 2019 г.

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Новое обнаружение аналитики угроз Azure AD теперь доступно в составе защита идентификации Azure AD

**Тип.** Новая функция.  
**Категория службы:** защита идентификации Azure AD  
**Возможности продукта.** Безопасность и защита удостоверений.

Обнаружение аналитики угроз Azure AD теперь доступно в составе обновленной защита идентификации Azure AD функции. Эта новая функция позволяет указать необычные действия пользователя для конкретного пользователя или действия, которые соответствуют известным шаблонам атак, основанным на внутренних и внешних источниках анализа угроз Майкрософт.

Дополнительные сведения о обновленной версии защита идентификации Azure AD см. в блоге, посвященном [четырем усовершенствованиям Защита идентификации Azure AD](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) , а также о том, [что защита идентификации Azure Active Directory (Обновлено)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) рассмотрен. Дополнительные сведения об обнаружении аналитики угроз в Azure AD см. в статье [Защита идентификации Azure Active Directory обнаружения рисков](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence) .

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Теперь доступно управление назначениями Azure AD (общедоступная Предварительная версия)

**Тип.** Новая функция.  
**Категория службы:** Управление удостоверениями  
**Возможности продукта:** Управление удостоверениями

Управление назначением Azure AD теперь в общедоступной предварительной версии помогает клиентам делегировать управление пакетами доступа, которые определяют, как сотрудники и бизнес-партнеры могут запрашивать доступ, кто должен утверждать и как долго они имеют доступ. Пакеты доступа могут управлять членством в группах Azure AD и Office 365, назначениями ролей в корпоративных приложениях и назначениями ролей для сайтов SharePoint Online. Дополнительные сведения об управлении обслуживанием см. в статье Обзор управления назначением [Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Дополнительные сведения о Azure AD Identity Governanceных функциях, в том числе управление привилегированными пользователями, проверках доступа и условиях использования, см. в статье [что такое Azure AD Identity Governance?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Настройка политики именования для групп Office 365 на портале Azure AD (общедоступная Предварительная версия)

**Тип.** Новая функция.  
**Категория службы.** Управление группами.  
**Возможности продукта.** Совместная работа.

Теперь администраторы могут настроить политику именования для групп Office 365 с помощью портала Azure AD. Это изменение помогает обеспечить соответствие соглашений об именовании для групп Office 365, созданных или измененных пользователями в Организации.

Политику именования для групп Office 365 можно настроить двумя разными способами.

- Определите префиксы или суффиксы, которые автоматически добавляются в имя группы.

- Отправка настроенного набора заблокированных слов для Организации, которые не разрешены в именах групп (например, "Исполнительный директор, зарплата, HR").

Дополнительные сведения см. [в разделе принудительная политика именования для групп Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Журналы действий Azure AD теперь доступны в Azure Monitor (общая доступность)

**Тип.** Новая функция.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.

Чтобы помочь устранить ваши отзывы о визуализациях с помощью журналов действий Azure AD, мы представляем новую функцию Insights в Log Analytics. Эта функция позволяет получить представление о ресурсах Azure AD с помощью наших интерактивных шаблонов, называемых книгами. Эти предварительно созданные книги могут предоставлять сведения для приложений и пользователей, а также включать:

- **Входы в систему.** Содержит сведения о приложениях и пользователях, включая расположение для входа, используемую версию операционной системы или клиента браузера, а также количество успешных или неудачных входов.

- **Устаревшая проверка подлинности и условный доступ.** Предоставляет сведения для приложений и пользователей, использующих устаревшую проверку подлинности, включая использование многофакторной идентификации, активируемое политиками условного доступа, приложениями, использующими политики условного доступа, и т. д.

- **Анализ сбоев при входе.** Помогает определить, возникают ли ошибки при входе в систему из-за действий пользователя, проблем с политиками или инфраструктуры.

- **Пользовательские отчеты.** Вы можете создать или изменить существующие книги, чтобы настроить функцию Insights для вашей организации.

Дополнительные сведения см. [в разделе Использование книг Azure Monitor для Azure Active Directory отчетов](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Новые Федеративные приложения доступны в коллекции приложений Azure AD — Апрель 2019

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.

В апреле 2019 мы добавили 21 новые приложения с поддержкой Федерации в коллекцию приложений:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [хрворкс Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [Перколате](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [мобиконтрол](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [Шибуми](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [бенчлинг](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [милеик](https://mileiq.onelink.me/991934284/7e980085), [пажедна](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [едубрите LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio Connect ](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMM](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Мител Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Алибаба Cloud (единый вход на основе ролей)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Управление капиталом цертент](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [сектиго Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [гринорбит](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [воркгрид](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [Monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [индигго](https://indiggolead.com/)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Новый параметр частоты проверок доступа и выбор нескольких ролей

**Тип.** Новая функция.  
**Категория службы.** Проверка доступа.  
**Возможности продукта:** Управление удостоверениями

Новые обновления в проверках доступа Azure AD позволяют:

- Изменяйте частоту проверок доступа в **два раза в год**, помимо ранее существующих параметров еженедельно, ежемесячно, ежеквартально и ежегодно.

- При создании одной проверки доступа выберите несколько ролей ресурсов Azure AD и Azure. В этом случае все роли настроены с одинаковыми параметрами, и все рецензенты будут уведомлены одновременно.

Дополнительные сведения о создании проверки доступа см. [в статье Создание проверки доступа для групп или приложений в проверках доступа Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Системы оповещений по электронной почте Azure AD Connect переносятся, отправляя новые сведения об отправителе по электронной почте для некоторых клиентов.

**Тип.** Измененная функция.  
**Категория службы.** AD Sync.  
**Возможности продукта.** Платформа.

Azure AD Connect находится в процессе переноса системы предупреждений электронной почты, потенциально показывая некоторым клиентам новый отправитель электронной почты. Чтобы решить эту эту проблемы, необходимо добавить `azure-noreply@microsoft.com` в список разрешений вашей организации или продолжить получение важных оповещений из Office 365, Azure или ваших служб синхронизации.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Изменения суффикса имени участника-пользователя теперь успешно выполнены между федеративными доменами в Azure AD Connect

**Тип.** Исправление.  
**Категория службы.** AD Sync.  
**Возможности продукта.** Платформа.

Теперь вы можете успешно изменить суффикс имени участника-пользователя из одного федеративного домена на другой федеративный домен в Azure AD Connect. Это исправление означает, что в течение цикла синхронизации больше не будет появляться сообщение об ошибке FederatedDomainChangeError или вы получите уведомление по электронной почте "не удалось обновить этот объект в Azure Active Directory, поскольку атрибут [ Федератедусер. UserPrincipalName], является недопустимым. Обновите значение в локальных службах каталогов.

Дополнительные сведения см. в разделе [Устранение ошибок во время синхронизации](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Повышенная безопасность с помощью политики условного доступа на основе защиты приложений в Azure AD (общедоступная Предварительная версия)

**Тип.** Новая функция.  
**Категория службы.** Условный доступ.  
**Возможности продукта.** Безопасность и защита удостоверений.

Условный доступ на основе защиты приложений теперь доступен с помощью политики " **требуется политика защиты приложений** ". Эта новая политика позволяет повысить безопасность вашей организации, помогая предотвратить следующее:

- Пользователи получают доступ к приложениям без лицензии Microsoft Intune.

- Пользователи не могут получить политику защиты приложений Microsoft Intune.

- Пользователи получают доступ к приложениям без настроенной политики защиты приложений Microsoft Intune.

Дополнительные сведения см. [в разделе как требовать политику защиты приложений для облачного доступа к приложениям с помощью условного доступа](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Новая поддержка единого входа и условного доступа Azure AD в Microsoft ребр (общедоступная Предварительная версия)

**Тип.** Новая функция.  
**Категория службы.** Условный доступ.  
**Возможности продукта.** Безопасность и защита удостоверений.

Мы улучшили поддержку Azure AD для Microsoft ребра, включая предоставление новой поддержки единого входа и условного доступа Azure AD. Если вы ранее использовали Microsoft Intune Managed Browser, теперь можно использовать Microsoft ребро.

Дополнительные сведения о настройке устройств и приложений, а также об управлении ими с помощью условного доступа см. в статьях использование [управляемых устройств для доступа к облачным приложениям с помощью условного](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) доступа и [требование утвержденных клиентских приложений для доступа к облачным приложениям с помощью условного доступа ](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Дополнительные сведения об управлении доступом с помощью Microsoft ребра с Microsoft Intune политиками см. [в разделе Управление доступом в Интернет с помощью Microsoft Intune браузера, защищенного политикой](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Март 2019 г.

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Инфраструктура процедур идентификации и поддержка настраиваемых политик в Azure Active Directory B2C теперь доступны (GA)

**Тип.** Новая функция.  
**Категория службы.** B2C — управление удостоверениями клиентов.  
**Возможности продукта.** B2B и B2C.

Теперь можно создавать пользовательские политики в Azure AD B2C, включая следующие задачи, которые поддерживаются в масштабе и в рамках соглашения об уровне обслуживания Azure.

- Создайте и отправьте пользовательские пути проверки подлинности с помощью настраиваемых политик.

- Поэтапно описывать пути взаимодействия пользователя для обмена данными между поставщиками удостоверений.

- Определять условные ветвления путей взаимодействия пользователей.

- Преобразуйте и сопоставьте утверждения для использования в решениях и обмене данными в режиме реального времени.

- Используйте службы с поддержкой REST API в пользовательских путях проверки подлинности. Например, с поставщиками электронной почты, Крмс и собственными системами авторизации.

- Федерацию с поставщиками удостоверений, которые соответствуют протоколу OpenIDConnect. Например, с несколькими клиентами Azure AD, поставщиками учетных записей социальных сетей или поставщиками двухфакторной проверки.

Дополнительные сведения о создании настраиваемых политик см. [в статье заметки разработчика для пользовательских политик в Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) и прочитайте [запись блога о Simonах Алекс, включая примеры использования](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Новые Федеративные приложения, доступные в коллекции приложений Azure AD — 2019 марта

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.

В марте 2019 мы добавили 14 новых приложений с поддержкой Федерации в коллекцию приложений:

[Делегат ISEC7 Mobile Exchange](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [система аудита на основе объяснений](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [экономичный](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [Powerschool Performance Matters](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [интрасети Iris](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit Horizons](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [TAS](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Новые соединители подготовки Zscaler и Atlassian в коллекции Azure AD — Март 2019

**Тип.** Новая функция.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.

Автоматизируйте создание, обновление и удаление учетных записей пользователей для следующих приложений:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler 1](https://aka.ms/ZscalerOneProvisioning), [Zscaler 2](https://aka.ms/ZscalerTwoProvisioning), [Zscaler 3](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

Дополнительные сведения о том, как лучше защитить организацию с помощью автоматической подготовки учетных записей пользователей, см. в статье [Автоматизация подготовки пользователей в приложениях SaaS с помощью Azure AD](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Восстановление удаленных групп Office 365 и управление ими на портале Azure AD

**Тип.** Новая функция.  
**Категория службы.** Управление группами.  
**Возможности продукта.** Совместная работа.

Теперь вы можете просматривать удаленные группы Office 365 и управлять ими с помощью портала Azure AD. Это изменение позволяет узнать, какие группы доступны для восстановления, а также как окончательно удалить все группы, которые не нужны в Организации.

Дополнительные сведения см. в разделе [Восстановление просроченных или удаленных групп](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Единый вход теперь доступен для локальных приложений Azure AD, защищенных с помощью SAML, через прокси приложения (общедоступная Предварительная версия)

**Тип.** Новая функция.  
**Категория службы.** Прокси-сервер приложения.  
**Возможность продукта.** Контроль доступа.

Теперь вы можете предоставить возможность единого входа (SSO) для локальных приложений, прошедших проверку подлинности с помощью SAML, а также удаленный доступ к этим приложениям через прокси приложения. Дополнительные сведения о настройке единого входа SAML с помощью локальных приложений см. в статье [единый вход SAML для локальных приложений с помощью прокси приложения (Предварительная версия)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Клиентские приложения в циклах запросов будут прерываться для повышения надежности и удобства работы пользователей.

**Тип.** Новая функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Проверка подлинности пользователей.

Клиентские приложения могут неправильно выдавать сотни одних и тех же запросов на вход в течение короткого периода времени. Эти запросы, будь то успех или нет, все способствуют снижению пользовательского интерфейса и повышенной рабочей нагрузке для IDP, увеличению задержки для всех пользователей и уменьшению доступности IDP.

Это обновление отправляет ошибку `invalid_grant`: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` клиентским приложениям, которые несколько раз выдают дублирующиеся запросы в течение короткого периода времени, помимо области обычной работы. Клиентские приложения, которые сталкиваются с этой проблемой, должны показывать интерактивный запрос, что требует от пользователя повторного входа. Дополнительные сведения об этом изменении и о том, как исправить приложение при возникновении этой ошибки, см. в разделе [новые возможности проверки подлинности](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Теперь доступны новые журналы аудита взаимодействие с пользователем

**Тип.** Измененная функция.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.

Мы создали новую страницу **журналов аудита** Azure AD, которая поможет улучшить удобочитаемость и способ поиска информации. Чтобы открыть страницу "новые **журналы аудита** ", выберите **журналы аудита** в разделе **действие** в Azure AD.

![Страница «Создание журналов аудита» с примерами сведений](media/whats-new/audit-logs-page.png)

Дополнительные сведения о новых **журналах аудита** см. в разделе [отчеты о действиях аудита на портале Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Новые предупреждения и рекомендации по предотвращению случайной блокировки администратора от неправильно настроенных политик условного доступа

**Тип.** Измененная функция.  
**Категория службы.** Условный доступ.  
**Возможности продукта.** Безопасность и защита удостоверений.

Чтобы предотвратить случайную блокирование пользователями собственных клиентов через неправильно настроенные политики условного доступа, мы создали новые предупреждения и обновленные инструкции в портал Azure. Дополнительные сведения о новых руководствах см. [в разделе что такое зависимости служб в Azure Active Directory условном доступе](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Улучшенные условия использования функций для конечных пользователей на мобильных устройствах

**Тип.** Измененная функция.  
**Категория службы.** Условия использования.  
**Возможности продукта.** Система управления.

Мы обновили существующие условия использования, чтобы помочь улучшить способы проверки и согласия на использование на мобильном устройстве. Теперь можно увеличить и уменьшить масштаб, вернуться назад, загрузить сведения и выбрать гиперссылки. Дополнительные сведения об обновленных условиях использования см. в разделе [Azure Active Directory условия использования](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Новые журналы действий Azure AD, доступные для загрузки

**Тип.** Измененная функция.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.

Теперь вы можете загрузить большие объемы журналов действий непосредственно из портал Azure. Это обновление позволяет:

- Загрузите до 250 000 строк.

- Получение уведомлений после завершения скачивания.

- Настройте имя файла.

- Определите формат выходных данных: JSON или CSV.

Дополнительные сведения об этой функции см [. в разделе Краткое руководство. скачивание отчета аудита с помощью портал Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Критическое изменение: обновление оценки условия с помощью Exchange ActiveSync (EAS)

**Тип.** Планирование изменений.  
**Категория службы.** Условный доступ.  
**Возможность продукта.** Контроль доступа.

Мы собираемся обновить, как Exchange ActiveSync (EAS) оценивает следующие условия:

- Расположение пользователя на основе страны, региона или IP-адреса

- Риск при входе

- Платформа устройства

Если вы ранее использовали эти условия в политиках условного доступа, имейте в виду, что поведение условия может измениться. Например, если ранее в политике было использовано условие расположения пользователя, политика будет пропущена в зависимости от расположения пользователя.

---

## <a name="february-2019"></a>Февраль 2019 г.

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Настраиваемое шифрование маркеров SAML Azure AD (общедоступная Предварительная версия) 

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Единый вход.

Теперь вы можете настроить любое поддерживаемое приложение SAML для получения зашифрованных токенов SAML. При настройке и использовании с приложением Azure AD шифрует выпущенные утверждения SAML с помощью открытого ключа, полученного из сертификата, хранящегося в Azure AD.

Дополнительные сведения о настройке шифрования маркеров SAML см. в статье [Настройка шифрования маркеров SAML в Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Создание проверки доступа для групп или приложений с помощью проверок доступа Azure AD

**Тип.** Новая функция.  
**Категория службы.** Проверка доступа.  
**Возможности продукта.** Система управления.

Теперь можно включить несколько групп или приложений в одну проверку доступа Azure AD для назначения членства в группах или приложений. Проверки доступа с несколькими группами или приложениями настроены с использованием одних и тех же параметров, и все входящие рецензенты будут уведомлены одновременно.

Дополнительные сведения о создании проверки доступа с помощью проверок доступа Azure AD см. в статье [Создание проверки доступа для групп или приложений в проверках доступа Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) .

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Новые Федеративные приложения, доступные в коллекции приложений Azure AD — Февраль 2019

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
В феврале 2019 мы добавили 27 новых приложений с поддержкой Федерации в коллекцию приложений:

[Еуромонитор Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [миндтиккле](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [finger FAT](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [аирстакк](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [Идриве](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [скивард кмлатив](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [бригхтидеа](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [алертопс](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [солоинсигхт-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Разрешение Click, [брандфолдер](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [сторегатесмартфиле](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [пексип](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [стормбоард](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [пластового](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [Поделитесь с мечты](https://www.shareadream.org/how-it-works), [бугснаг](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [облаком интеграции](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial)с помощью WebMethod, [База знаний В любой точке LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [кампусе подразделения](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [Periscope данных](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [портале Нетоп](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [Smartvid.IO](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [пуреклауд по женесис](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [платформе производительности кликкуп](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Улучшенная общая регистрация MFA/SSPR

**Тип.** Измененная функция.  
**Категория службы.** Самостоятельный сброс пароля.  
**Возможности продукта.** Проверка подлинности пользователей.

В ответ на Отзывы клиентов мы улучшили общий предварительный просмотр регистрации MFA/SSPR, помогая пользователям быстрее регистрировать свои сведения о безопасности для MFA и SSPR. 

**Чтобы включить улучшенный интерфейс для пользователей, выполните следующие действия.**

1. В качестве глобального администратора или администратора пользователей войдите в портал Azure и перейдите в раздел **Azure Active Directory > Параметры пользователя > Управление параметрами для функций предварительной версии панели доступа**. 

2. В области **Пользователи, которые могут использовать функции предварительной версии для регистрации и управления сведениями о безопасности — обновление** выберите Включение компонентов для **выбранной группы пользователей** или для **всех пользователей**.

В течение следующих нескольких недель мы удалим возможность включить старый общий просмотр регистрации MFA/SSPR для клиентов, которые еще не включены.

**Чтобы узнать, будет ли элемент управления удален для клиента, выполните следующие действия.**

1. В качестве глобального администратора или администратора пользователей войдите в портал Azure и перейдите в раздел **Azure Active Directory > Параметры пользователя > Управление параметрами для функций предварительной версии панели доступа**.  

2. Если **пользователям, которые могут использовать функции предварительной версии для регистрации и управления сведениями о безопасности** , не задано значение **нет**, этот параметр будет удален из клиента.

Независимо от того, был ли ранее включен старый общий просмотр регистрации MFA/SSPR для пользователей или нет, старый интерфейс будет отключен в будущем. Поэтому настоятельно рекомендуется как можно быстрее перейти на новый, расширенный опыт.

Дополнительные сведения о расширенных возможностях регистрации см. [в этой статье](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Обновленные возможности управления политиками для потоков пользователей

**Тип.** Измененная функция.  
**Категория службы.** B2C — управление удостоверениями клиентов.  
**Возможности продукта.** B2B и B2C.

Мы обновили процесс создания и управления политикой для потоков пользователей (ранее назывались встроенными политиками). Этот новый интерфейс теперь используется по умолчанию для всех клиентов Azure AD.

Вы можете предоставить дополнительные отзывы и предложения с помощью значков "смайлик" или "нахмуренный смайлик" в области **Отправить отзыв** в верхней части экрана портала.

Дополнительные сведения о новых возможностях управления политиками см. в разделе [Azure AD B2C теперь есть настройка JavaScript, а также блог о многих новых функциях](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) .

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Выбор конкретных версий элементов страницы, предоставляемых Azure AD B2C

**Тип.** Новая функция.  
**Категория службы.** B2C — управление удостоверениями клиентов.  
**Возможности продукта.** B2B и B2C.

Теперь можно выбрать конкретную версию элементов страницы, предоставляемых Azure AD B2C. Выбрав конкретную версию, вы сможете проверить обновления, прежде чем они появятся на странице, и получить предсказуемое поведение. Кроме того, теперь можно принять участие в принудительном применении конкретных версий страниц, чтобы разрешить настройку JavaScript. Чтобы включить эту функцию, перейдите на страницу **свойств** в потоке пользователя.

Дополнительные сведения о выборе конкретных версий элементов страницы см. в разделе [Azure AD B2C теперь есть настройка JavaScript и еще несколько новых функций](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) .

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Настраиваемые требования к паролю для конечных пользователей для B2C (GA)

**Тип.** Новая функция.  
**Категория службы.** B2C — управление удостоверениями клиентов.  
**Возможности продукта.** B2B и B2C.

Теперь вы можете настроить сложность пароля организации для конечных пользователей, а не использовать собственную политику паролей Azure AD. В колонке **свойств** потоков пользователя (ранее известные как встроенные политики) можно выбрать сложность пароля ( **простой** или **высокий**) или создать **Пользовательский** набор требований.

Дополнительные сведения о конфигурации требования к сложности пароля см. [в разделе Настройка требований к сложности паролей в Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Новые шаблоны по умолчанию для пользовательских интерфейсов проверки подлинности

**Тип.** Новая функция.  
**Категория службы.** B2C — управление удостоверениями клиентов.  
**Возможности продукта.** B2B и B2C.

Вы можете использовать наши новые шаблоны по умолчанию, расположенные в колонке **макеты страниц** пользовательских потоков (ранее известные как встроенные политики), чтобы создать пользовательскую процедуру проверки подлинности для пользователей.

Дополнительные сведения об использовании шаблонов см. в разделе [Azure AD B2C теперь содержит настройки JavaScript и множество новых функций](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Январь 2019 г.

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Совместная работа в службе Active Directory B2B с использованием аутентификации на основе одноразового секретного кода (общедоступная предварительная версия)

**Тип.** Новая функция.  
**Категория службы.** B2B.  
**Возможности продукта.** B2B и B2C.

Мы реализовали функцию аутентификации на основе одноразового секретного кода (OTP) для гостевых пользователей B2B, которые не могут пройти аутентификацию с помощью других средств, таких как Azure AD, учетная запись Майкрософт (MSA) или федерация с Google. Этот новый метод аутентификации означает, что гостевым пользователям не нужно создавать новую учетную запись Майкрософт. Вместо этого при активации приглашения или доступе к общему ресурсу гостевой пользователь может запросить временный код, который будет отправлен на его адрес электронной почты. Гостевой пользователь может войти, используя этот временный код.

Дополнительные сведения см. в статье [Проверка подлинности с отправкой одноразового секретного кода по почте (предварительная версия)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) и в записи блога [Azure AD makes sharing and collaboration seamless for any user with any account](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949) (Azure AD упрощает совместную работу для любого пользователя с любой учетной записью).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Новые параметры файлов cookie Azure Active Directory Application Proxy

**Тип.** Новая функция.  
**Категория службы.** Прокси-сервер приложения.  
**Возможность продукта.** Контроль доступа.

Добавлено три новых параметра файлов cookie, доступных для приложений, которые опубликованы с помощью Application Proxy.

- **Использовать cookie-файлы HTTP-Only.** Устанавливает флаг **HTTPOnly** для файлов cookie доступа и сеанса на Application Proxy. Применение этого параметра обеспечивает дополнительные преимущества безопасности, например, помогает предотвратить копирование или изменение файлов cookie с помощью написания скриптов на стороне клиента. Рекомендуется включить этот флаг (выберите **Да**) для дополнительных преимуществ.

- **Использовать безопасный файл cookie.** Устанавливает флаг **Безопасный** для файлов cookie доступа и сеанса в прокси приложении. Применение этого параметра обеспечивает дополнительные преимущества безопасности, обеспечивая передачу файлов cookie только по безопасным каналам TLS, например HTTPS. Рекомендуется включить этот флаг (выберите **Да**) для дополнительных преимуществ.

- **Сохранять файлы сookie.** Предотвращает файлы cookie доступа от истечения срока действия, когда веб-браузер закрыт. Эти файлы cookie действуют в течение времени существования маркера доступа. Тем не менее файлы cookie сбрасываются при достижении времени истечения срока действия, или если пользователь вручную удаляет файл cookie. Мы рекомендуем оставить по умолчанию параметр **Нет** и применять его только для более старых приложений, которые не отправляют файлы cookie между процессами.

Дополнительные сведения о новых файлах cookie см. в разделе [Параметры файлов cookie для доступа к локальным приложениям в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Новые федеративные приложения доступные в коллекции приложений Azure AD — январь 2019

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
В январе 2019 года мы добавили 35 новых приложений с поддержкой федерации в коллекцию приложений:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [Talent Palette](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco Umbrella](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Internet Access Administrator](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [Expiration Reminder](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR Viewer](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [Verb](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital Close](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [Cloud Service PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [Workable](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [GTNexus SSO System](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [ARES for Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 for Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [Visitly](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Korn Ferry ALP](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Новые улучшения защиты идентификации Azure AD (общедоступная предварительная версия)

**Тип.** Измененная функция.  
**Категория службы.** Защита идентификации.  
**Возможности продукта.** Безопасность и защита удостоверений.

Рады сообщить, что мы добавили следующие усовершенствования для общедоступной предварительной версии Защиты идентификации Azure AD, включая:

- обновленный и более интегрированный пользовательский интерфейс;

- дополнительные программные интерфейсы;

- улучшенная оценка риска с помощью машинного обучения;

- выравнивание продукта для пользователей, представляющих риск и рискованные входы в систему.

Дополнительные сведения об усовершенствованиях см. статью [Что такое Защита идентификации Azure Active Directory (обновленная)?](https://aka.ms/IdentityProtectionDocs), чтобы узнать больше и поделиться своими мыслями с помощью запросов внутри продукта.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Новая функция блокировки приложения для приложения Microsoft Authenticator на устройствах iOS и Android

**Тип.** Новая функция.  
**Категория службы:** Приложение Microsoft Authenticator  
**Возможности продукта.** Безопасность и защита удостоверений.

Чтобы обеспечить большую безопасность ваших одноразовых паролей, информации о приложении и параметров приложения, вы можете включить функцию "Блокировка приложения" в приложении Microsoft Authenticator. Если включить функцию "Блокировка приложения", то каждый раз, когда вы открываете приложение Microsoft Authenticator, вам будет предлагаться использовать PIN-код или биометрические данные для прохождения аутентификации.

Дополнительные сведения можно найти в статье [Часто задаваемые вопросы о приложении Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Улучшенные возможности экспорта Azure AD Privileged Identity Management

**Тип.** Новая функция.  
**Категория службы.** Управление привилегированными пользователями.  
**Возможности продукта.** Управление привилегированными пользователями.

Администраторы Azure AD Privileged Identity Management теперь могут экспортировать все активные и допустимые назначения ролей для определенного ресурса, включая назначения ролей для всех дочерних ресурсов. Ранее для администраторов возникали трудности при получении полного списка назначения ролей для подписки, и им было необходимо экспортировать назначения ролей для каждого конкретного ресурса.

Дополнительные сведения см. в статье [Просмотр пользователей с ролями службы Azure в PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="novemberdecember-2018"></a>Ноябрь и декабрь 2018 г.

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Пользователи, удаленные из области синхронизации, больше не преобразуются в облачные учетные записи

**Тип.** Исправление.  
**Категория службы:** Управление пользователями  
**Возможности продукта.** Каталог.

>[!Important]
>Мы знаем и понимаем, как вы беспокоитесь на счет этого исправления. Поэтому мы откатили это изменение, пока не сможем упростить реализацию исправления в вашей организации.

Мы исправили ошибку, из-за которой флаг DirSyncEnabled пользователя ошибочно переключался в значение **False**, когда объект доменных служб Active Directory (AD DS) исключался из области синхронизации и затем перемещался в корзину в Azure AD в следующем цикле синхронизации. В результате этого исправления, если пользователь исключается из области синхронизации, а затем восстанавливается из корзины Azure AD, учетная запись пользователя остается синхронизированной из локальной службы AD (как и ожидается) и ею нельзя управлять в облаке, так как ее главным источником (SoA) по-прежнему остается локальная служба AD.

До этого исправления возникала проблема, когда флаг DirSyncEnabled переключался в значение False. Из-за этого создавалось ошибочное впечатление, что такие учетные записи были преобразованы в облачные объекты, которыми можно управлять в облаке. При этом главным источником учетных записей по-прежнему оставалась локальная среда и все синхронизированные свойства (теневые атрибуты) поступали из локальной службы AD. Это условие вызывало несколько проблем в Azure AD и других облачных рабочих нагрузках (таких как Exchange Online), которые должны были обрабатывать эти учетные записи как синхронизированные из AD, хотя их поведение соответствовало облачным учетным записям.

В настоящее время единственным способом по-настоящему преобразовать учетную запись, синхронизированную из AD, в облачную учетную запись является отключение DirSync на уровне клиента, что запускает внутреннюю операцию по переносу главного источника (SoA). Такое изменение SoA требует (в том числе) очистки всех атрибутов, связанных с локальной средой (таких как LastDirSyncTime и теневые атрибуты), и отправки в другие облачные рабочие нагрузки сигнала о преобразовании соответствующего объекта в облачную учетную запись.

Соответственно, это исправление предотвращает прямое изменение атрибута ImmutableID пользователя, синхронизированного из AD, что требовалось в некоторых сценариях в прошлом. По сути атрибут ImmutableID объекта в Azure AD, как и предполагает название, должен быть неизменяемым. Для разрешения таких ситуаций доступны новые функции, реализованные в Azure AD Connect Health и клиенте службы синхронизации Azure AD Connect.

- **Поэтапное крупномасштабное изменение ImmutableID для нескольких пользователей**
  
  Например, вам необходимо выполнить длительную миграцию между лесами AD DS. Решение. Используйте Azure AD Connect для **настройки привязки к источнику** и, при миграции пользователя, скопируйте существующие значения ImmutableID из Azure AD в атрибут MS-DS-целостный GUID локального пользователя AD DS. Дополнительные сведения см. в статье, посвященной [использованию ms-DS-ConsistencyGuid в качестве sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Крупномасштабное изменение ImmutableID для нескольких пользователей за один раз**

  Например, при реализации Azure AD Connect вы сделали ошибку и теперь необходимо изменить атрибут SourceAnchor. Решение. Отключите DirSync на уровне клиента и очистите все недопустимые значения ImmutableID. Дополнительные сведения см. в статье, посвященной [отключению синхронизации каталогов для Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Повторное сопоставление локального пользователя с существующим пользователем в Azure AD**. Например, повторное создание пользователя AD DS приводит к появлению дубликата учетной записи Azure AD вместо его повторного сопоставления с существующей учетной записью Azure AD (потерянный объект). Решение. Используйте Azure AD Connect Health в портал Azure для повторного сопоставления исходной привязки или ImmutableID. Дополнительные сведения см. в статье [Сценарий с потерянным объектом](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Критическое изменение: обновления схемы журналов аудита и входа в систему с помощью Azure Monitor

**Тип.** Измененная функция.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.

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

**Тип.** Измененная функция.  
**Категория службы.** Защита идентификации.  
**Возможности продукта:** Оценки риска

Улучшения подсистемы оценки риска пользователя и риска при входе, связанные с защитой идентификации, могут повысить точность и охват при оценке риска пользователя. Администраторы могут заметить, что уровень риска пользователя больше не связан напрямую с уровнем риска определенных случаев обнаружения, а число и уровень рискованных событий входа в систему возросли.

Теперь обнаруженные риски оцениваются посредством контролируемой модели машинного обучения, которая вычисляет риск пользователя с помощью дополнительных характеристик входа пользователя в систему и шаблона обнаружения. На основании этой модели администратор может найти пользователей с высоким уровнем риска, даже если уровень риска для случаев обнаружения, связанных с этими пользователями, не превышает низкий или средний уровень. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Администраторы могут сбрасывать свой пароль с помощью приложения Microsoft Authenticator (предварительная версия)

**Тип.** Измененная функция.  
**Категория службы.** Самостоятельный сброс пароля.  
**Возможности продукта.** Проверка подлинности пользователей.

Администраторы Azure AD теперь могут сбросить свой пароль с помощью уведомлений приложения Microsoft Authenticator, кода из любого мобильного приложения Authenticator или аппаратного токена. Чтобы сбросить свой пароль, администраторы теперь могут использовать два из следующих методов:

- уведомление приложения Microsoft Authenticator;

- другое мобильное приложение Authenticator или код аппаратного токена;

- Email

- телефонный вызов;

- текстовое сообщение;

Дополнительные сведения об использовании приложения Microsoft Authenticator для сброса паролей см. в разделе [Мобильное приложение и SSPR (предварительная версия)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr).

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Новая роль администратора облачных устройств в Azure AD (предварительная версия)

**Тип.** Новая функция.  
**Категория службы:** Регистрация устройств и управление ими  
**Возможности продукта:** Контроль доступа

Администраторы могут назначать пользователям новую роль "Администратор облачных устройств" для выполнения операций администрирования облачных устройств. Пользователи с этой ролью могут включать, отключать и удалять устройства в Azure AD, а также считывать ключи BitLocker в Windows 10 (при наличии) на портале Azure.

Дополнительные сведения о ролях и разрешениях см. в статье [Назначение ролей администратора в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Управление устройствами с помощью новой метки времени действия в Azure AD (предварительная версия)

**Тип.** Новая функция.  
**Категория службы:** Регистрация устройств и управление ими  
**Возможности продукта:** Управление жизненным циклом устройств

Мы понимаем, что с течением времени необходимо обновить и снять с учета устройства вашей организации в Azure AD, чтобы избежать устаревших устройств в вашей среде. Чтобы упростить этот процесс, теперь служба Azure AD обновляет устройства с помощью новой метки времени действия, помогая управлять жизненным циклом устройств.

Дополнительные сведения о том, как получить и использовать эту метку времени, см. в разделе [управление устаревшими устройствами в Azure AD.](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Администраторы могут требовать от пользователей принять условия использования на каждом устройстве.

**Тип.** Новая функция.  
**Категория службы.** Условия использования.  
**Возможности продукта.** Система управления.
 
Теперь администраторы могут включить параметр **требовать согласие пользователей на каждое устройство** , чтобы требовать от пользователей принять условия использования на каждом устройстве, которое они используют в вашем клиенте.

Дополнительные сведения см. в [разделе условия использования для каждого устройства статьи Azure Active Directory условия использования](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Администраторы могут настроить условия использования для истечения срока действия с учетом повторяющегося расписания.

**Тип.** Новая функция.  
**Категория службы.** Условия использования.  
**Возможности продукта.** Система управления.
 

Теперь администраторы могут включить параметр **истечение срока действия** , чтобы срок действия условий использования истек для всех пользователей в зависимости от указанного повторяющегося расписания. Расписание может повторяться один раз в год, два раза в год, ежеквартально или ежемесячно. После истечения срока действия условий использования пользователи должны принять условия.

Дополнительные сведения см. в [разделе Добавление условий использования функции Azure Active Directory условий использования](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Администраторы могут настроить условия использования для истечения срока действия в зависимости от расписания каждого пользователя.

**Тип.** Новая функция.  
**Категория службы.** Условия использования.  
**Возможности продукта.** Система управления.

Теперь администраторы могут указать длительность, в течение которого пользователь должен снова принять условия использования. Например, администраторы могут указать, что пользователи должны снова принимать условия использования каждые 90 дней.

Дополнительные сведения см. в [разделе Добавление условий использования функции Azure Active Directory условий использования](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Новые электронные сообщения Azure AD Privileged Identity Management (PIM) для ролей Azure Active Directory

**Тип.** Новая функция.  
**Категория службы.** Управление привилегированными пользователями.  
**Возможности продукта.** Управление привилегированными пользователями.
 
Клиенты, использующие Azure AD Privileged Identity Management (PIM), теперь могут получать еженедельные дайджесты по электронной почте, включая приведенные ниже сведения за последние семь дней:

- общие сведения о наиболее используемых разрешенных и постоянных назначений ролей;

- число пользователей, активирующих роли;

- число пользователей, назначенных для ролей в PIM;

- число пользователей, назначенных для ролей вне PIM;

- число пользователей с постоянными ролями в PIM.

Дополнительные сведения о PIM и доступных уведомлениях по электронной почте см. в разделе [Уведомления по электронной почте в PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications).

---

### <a name="group-based-licensing-is-now-generally-available"></a>Выпущена общедоступная версия группового лицензирования

**Тип.** Измененная функция.  
**Категория службы.** Другая.  
**Возможности продукта.** Каталог.

Период предоставления общедоступной предварительной версии группового лицензирования завершен, и теперь выпущена общедоступная версия этой функции. В рамках данного общедоступного выпуска мы сделали эту функцию более масштабируемой. Кроме того, мы добавили возможность повторной обработки назначений группового лицензирования для отдельного пользователя и возможность использовать групповое лицензирование с лицензиями E3 или A3 в Office 365.

Дополнительные сведения о групповом лицензировании см. в разделе [Что такое лицензии групп в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>В коллекции приложений Azure AD доступны новые федеративные приложения (ноябрь 2018 г.)

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
В ноябре 2018 года мы добавили в коллекцию приложений следующие 26 новых приложений с поддержкой федерации:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [Gra-Pe](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [Infinite Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [Drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [Everbridge Member Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [Plex Apps - Classic Test](https://test.plexonline.com/signon), [Plex Apps – Classic](https://www.plexonline.com/signon), [Plex Apps - UX Test](https://test.cloud.plex.com/sso), [Plex Apps – UX](https://cloud.plex.com/sso), [Plex Apps – IAM](https://accounts.plex.com/), [CRAFTS - Childcare Records, Attendance, & Financial Tracking System](https://getcrafts.ca/craftsregistration). 

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

## <a name="october-2018"></a>Октябрь 2018 г.

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Возможность работать с журналами Azure AD в Azure Log Analytics (предварительная версия)

**Тип.** Новая функция.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.

Мы рады сообщить, что теперь вы можете пересылать журналы Azure AD в Azure Log Analytics! Эта функция, на которую поступило больше всего запросов, даст вам еще более удобный доступ к данным аналитики для бизнеса, операций и безопасности, а также поможет в мониторинге инфраструктуры. Дополнительные сведения см. в записи блога [Azure Active Directory Activity logs in Azure Log Analytics now available](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) (В Azure Log Analytics теперь доступны журналы активности Azure Active Directory).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>В коллекции приложений Azure AD доступны новые федеративные приложения (октябрь 2018 г.)

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.

В октябре 2018 года мы добавили в коллекцию приложений 14 новых приложений с поддержкой федерации:

[My Award Points](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Dialpad, [ON24 Virtual Environment](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscaler Three](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Workspot Control](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial).

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Уведомления по электронной почте для доменных служб Azure AD

**Тип.** Новая функция.  
**Категория службы.** Доменные службы Azure AD.  
**Возможности продукта.** Доменные службы Azure AD.

Доменные службы Azure AD отображают на портале Azure оповещения об ошибках в настройках или проблемах с управляемым доменом. Эти оповещения содержат пошаговые инструкции, с помощью которых можно попытаться устранить проблемы без обращения в службу поддержки.

Начиная с октября вы сможете настраивать параметры уведомлений для управляемого домена, чтобы при каждом новом оповещении отправлялось сообщение электронной почты назначенной группе пользователей. Таким образом, им не придется постоянно проверять обновления информации на портале.

Дополнительные сведения см. в статье [Параметры уведомлений в доменных службах Azure AD](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Портал Azure AD поддерживает использование API управления доменами ForceDelete для удаления пользовательских доменов 

**Тип.** Измененная функция.  
**Категория службы.** Управление каталогами.  
**Возможности продукта.** Каталог.

Мы рады сообщить, что теперь с помощью API управления доменами ForceDelete можно удалить имя личного домена и асинхронно изменить такое имя (contoso.com) в ссылках на пользователей, группы и приложения на имя домена по умолчанию (contoso.onmicrosoft.com).

Такое изменение позволяет быстрее удалить имя личного домена, которое ваша организация больше не использует или намерена перенести в другую службу Azure AD.

Дополнительные сведения см. в статье [Удаление имени личного домена](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>Сентябрь 2018 г.
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Обновление разрешений роли администратора для динамических групп

**Тип.** Исправление.  
**Категория службы.** Управление группами.  
**Возможности продукта.** Совместная работа.

Мы исправили проблему, которая не позволяла некоторым ролям администратора создавать и обновлять правила динамического членства, не являясь владельцем группы.

Вот эти роли:

- Глобальный администратор.

- Администратор Intune

- Администратор пользователей

Дополнительные сведения см. в статье [Создание динамической группы и проверка состояния](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule).

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Упрощенные параметры конфигурации единого входа (SSO) для некоторых сторонних приложений

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Единый вход.

Мы понимаем, что настройка единого входа (SSO) для приложений SaaS (программное обеспечение как услуга) может создавать трудности из-за уникальных настроек для каждого приложения. Мы создали упрощенную процедуру настройки, при которой автоматически указываются параметры единого входа для следующих приложений SaaS сторонних разработчиков:

- Zendesk

- ArcGis Online;

- Jamf Pro.

Чтобы применить эту упрощенную процедуру, перейдите к странице настройки единого входа для нужного приложения (**Портал Azure** > **Настройка единого входа**). Дополнительные сведения есть в статье [Интеграция приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list).

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Страница "Azure Active Directory - Where is your data located?" (Azure Active Directory — где находятся ваши данные?)

**Тип.** Новая функция.  
**Категория службы.** Другая.  
**Возможности продукта.** GoLocal.

Выберите регион вашей компании на странице **Azure Active Directory - Where is your data located** (Azure Active Directory — где находятся ваши данные?), чтобы узнать, в каком центре обработки данных Azure хранятся ваши данные Azure AD для каждой из служб Azure AD. Здесь вы можете применить фильтр по конкретным службам Azure AD для выбранного региона.

Чтобы использовать эту функцию и (или) получить дополнительные сведения о ней, откройте страницу [Azure Active Directory - Where is your data located](https://aka.ms/AADDataMap) (Azure Active Directory — где находятся ваши данные).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Доступен новый план развертывания для панели доступа "Мои приложения"

**Тип.** Новая функция.  
**Категория службы.** "Мои приложения".  
**Возможности продукта.** Единый вход.

Изучите новый план развертывания, реализованный для панели доступа "Мои приложения" (https://aka.ms/deploymentplans).
Панель доступа "Мои приложения" служит для пользователя единым расположением для поиска приложений и доступа к ним. Этот портал также содержит возможности самообслуживания, например позволяет пользователю запрашивать доступ к приложениям и (или) группам и управлять им от лица других пользователей.

Дополнительные сведения см. в статье [Что такое портал MyApps?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction).

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Новая вкладка "Устранение неполадок и поддержка" на странице журналов входов на портале Azure

**Тип.** Новая функция.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.

Новая вкладка **Устранение неполадок и поддержка** на странице **входа** в портал Azure предназначена для помощи администраторам и инженерам службы поддержки в устранении неполадок, связанных с входом в Azure AD. На этой новой вкладке содержится код ошибки, сообщение об ошибке и рекомендации по исправлению (если они есть) для устранения проблемы. Если с проблемой не удастся справиться, здесь же можно создать запрос в службу поддержки с помощью новой функции **копирования в буфер обмена**, с помощью которой для файла журнала заполняются поля **Идентификатор запроса** и **Дата (UTC)** в запросе в службу поддержки.  

![Журналы входа, отображающие новую вкладку](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Улучшенная поддержка пользовательских свойств расширения, позволяющих создать правила динамического членства

**Тип.** Измененная функция.  
**Категория службы.** Управление группами.  
**Возможности продукта.** Совместная работа.

После этого обновления вы сможете при создании динамического правила членства перейти по ссылке **Get custom extension properties** (Получить пользовательские свойства расширения) из конструктора динамических правил для пользовательских групп, ввести уникальный идентификатор приложения и получить полный список пользовательских свойств расширения. Этот список можно обновить, чтобы просмотреть новые пользовательские свойства расширения для выбранного приложения.

Дополнительные сведения об использовании пользовательских свойств расширения в правилах динамического членства см. в разделе [Свойства расширения и пользовательские свойства расширения](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Новые утвержденные клиентские приложения для условного доступа на основе приложений Azure AD

**Тип.** Планирование изменений.  
**Категория службы.** Условный доступ.  
**Возможности продукта.** Безопасность и защита удостоверений.

Следующие приложения добавлены в список [утвержденных клиентских приложений](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference#approved-client-app-requirement):

- Microsoft To-Do

- Microsoft Stream;

Дополнительные сведения см. здесь:

- [Условный доступ на основе приложений Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Добавлена поддержка самостоятельного сброса пароля с экрана блокировки Windows 7, 8 и 8.1

**Тип.** Новая функция.  
**Категория службы:** SSPR  
**Возможности продукта.** Проверка подлинности пользователей.

Когда вы настроите эту новую функцию, для пользователей отобразится ссылка для сброса пароля на экране **блокировки** любого устройства под управлением Windows 7, Windows 8 или Windows 8.1. Щелкнув эту ссылку, пользователь пройдет тот же процесс сброса пароля, что и в веб-браузере.

Дополнительные сведения см. в статье [How to: Enable password reset from Windows 7, 8, and 8.1](https://aka.ms/ssprforwindows78) (Практическое руководство. Включение функции сброса пароля из Windows 7, 8 и 8.1)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Уведомление об изменении: коды авторизации больше не будут доступны для повторного использования 

**Тип.** Планирование изменений.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Проверка подлинности пользователей.

С 15 ноября 2018 г. в Azure AD прекращается поддержка использованных кодов аутентификации для приложений. Это изменение системы безопасности помогает Azure AD соответствовать спецификации OAuth и будет применяться для конечных точек версии 1 и 2.

Если ваше приложение повторно использует коды проверки подлинности для получения маркеров для нескольких ресурсов, мы рекомендуем использовать код для получения маркера обновления, а затем использовать этот маркер для получения дополнительных маркеров для других ресурсов. Коды проверки подлинности можно использовать только один раз, однако маркеры обновления можно использовать несколько раз для нескольких ресурсов. У приложения, которое пытается повторно использовать код аутентификации в потоке кода OAuth, возникнет ошибка invalid_grant.

Это и другие изменения в работе с протоколами описаны на странице [с полным списком новых возможностей для аутентификации](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>В коллекции приложений Azure AD доступны новые федеративные приложения (сентябрь 2018 г.)

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
В сентябре 2018 года мы добавили в коллекцию приложений следующие 16 новых приложений с поддержкой федерации:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [Comeet Recruiting Software](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [JDA Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), join.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass, [Rackspace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft SSO for Azure, SurveyMonkey, [Convene](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial).

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Поддержка дополнительных методов преобразования утверждений

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Единый вход.

Мы добавили новые методы преобразования утверждений — ToLower() и ToUpper(), — которые можно применять для SAML-маркеров на странице **настройки единого входа** на основе SAML.

Дополнительные сведения см. в статье [Настройка утверждений, выпущенных в токене SAML для корпоративных приложений в AAD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Обновленный пользовательский интерфейс для настройки приложений на основе SAML (предварительная версия)

**Тип.** Измененная функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Единый вход.

В обновленном пользовательском интерфейсе для настройки приложений на основе SAML вы получите следующие возможности:

- новый интерфейс пошаговой настройки приложений на основе SAML;

- больше информации о том, чего не хватает в конфигурации и что настроено неправильно;

- возможность добавлять несколько адресов электронной почты для уведомлений об истечении срока действия сертификата;

- новые методы ToLower() и ToUpper() и другие изменения в механизме преобразования утверждений;

- возможность передачи собственного сертификата для подписи маркера в корпоративных приложениях;

- возможность настроить формат NameID для приложений SAML и указать расширение каталога по идентификатору имени.

Чтобы включить обновленное представление, щелкните ссылку **Попробуйте новый интерфейс** в верхней части страницы **Единый вход**. Дополнительные сведения см. в [руководстве по настройке единого входа на основе SAML для приложения в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal).

---

## <a name="august-2018"></a>Август 2018 г.

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Изменения в диапазонах IP-адресов Azure Active Directory

**Тип.** Планирование изменений.  
**Категория службы.** Другая.  
**Возможности продукта.** Платформа.

Мы вводим большие диапазоны IP-адресов в Azure Active Directory, то есть, если вы настроили диапазоны IP-адресов Azure AD для брандмауэров, маршрутизаторов или групп безопасности сети, вам потребуется их обновить. Мы выполняем это обновление, поэтому вам не придется менять конфигурацию диапазона IP-адресов брандмауэра, маршрутизатора или сети безопасности, когда Azure Active Directory добавляет новые конечные точки. 

Сетевой трафик перейдет в эти новые диапазоны в течение следующих двух месяцев. Чтобы продолжить непрерывное обслуживание, вам необходимо добавить эти обновленные значения в свои IP-адреса до 10 сентября 2018 года:

- 20.190.128.0/18 

- 40.126.0.0/18 

Мы настоятельно рекомендуем не удалять старые диапазоны IP-адресов, пока весь сетевой трафик не переместится на новые диапазоны. Сведения о переносе и о том, когда вы сможете удалить старые диапазоны, см. в разделе [URL-адреса и диапазоны IP-адресов Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Уведомление об изменении: коды авторизации больше не будут доступны для повторного использования 

**Тип.** Планирование изменений.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Проверка подлинности пользователей.

С 15 ноября 2018 г. в Azure AD прекращается поддержка использованных кодов аутентификации для приложений. Это изменение системы безопасности помогает Azure AD соответствовать спецификации OAuth и будет применяться для конечных точек версии 1 и 2.

Если ваше приложение повторно использует коды проверки подлинности для получения маркеров для нескольких ресурсов, мы рекомендуем использовать код для получения маркера обновления, а затем использовать этот маркер для получения дополнительных маркеров для других ресурсов. Коды проверки подлинности можно использовать только один раз, однако маркеры обновления можно использовать несколько раз для нескольких ресурсов. У приложения, которое пытается повторно использовать код аутентификации в потоке кода OAuth, возникнет ошибка invalid_grant.

Это и другие изменения в работе с протоколами описаны на странице [с полным списком новых возможностей для аутентификации](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Конвергентное управление сведениями для защиты для самостоятельного сброса пароля (SSPR) и Многофакторной идентификации (MFA)

**Тип.** Новая функция.  
**Категория службы:** SSPR  
**Возможности продукта.** Проверка подлинности пользователей.
 
Эта новая функция позволяет пользователям управлять своими сведениями для защиты (например, номером телефона, мобильным приложением и т. д.) для SSPR и MFA в одном расположении и интерфейсе (ранее все выполнялось в двух разных расположениях).

Этот конвергентный интерфейс доступен для пользователей, использующих либо SSPR, либо MFA. Кроме того, если организация не требует регистрацию MFA или SSPR в принудительном порядке, пользователи все равно могут зарегистрировать в сведениях для защиты разрешенные организацией методы MFA или SSPR через портал My Apps.

Это общедоступная предварительная версия. Администраторы могут включить новую функцию (при необходимости) для выбранной группы или всех пользователей в клиенте. Дополнительные сведения о конвергентном интерфейсе см. в [этой записи блога](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/).

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Новый параметр "HTTP-Only cookies" в приложениях Azure AD Application Proxy

**Тип.** Новая функция.  
**Категория службы.** Прокси-сервер приложения.  
**Возможность продукта.** Контроль доступа.

В приложениях Application Proxy доступен новый параметр под названием **HTTP-Only Cookies** (Только файлы cookie HTTP). Он позволяет обеспечить дополнительную безопасность, включив флаг HTTPOnly в заголовок ответа HTTP для файлов cookie сеанса и доступа к Application Proxy, запрещая доступ к файлу cookie с клиентского скрипта и предотвращая такие действия, как копирование или изменение файла cookie. Хотя этот флаг ранее не использовался, ваши файлы cookie всегда шифровались и передавались с использованием SSL-соединения, что обеспечивало защиту от неправильных изменений.

Этот параметр несовместим с приложениями, использующими элементы ActiveX, например удаленный рабочий стол. Если вы находитесь в подобной ситуации, рекомендуем отключить этот параметр.

Дополнительные сведения о параметре HTTP-Only Cookies см. в статье [Публикация приложений с помощью Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Управление привилегированными пользователями (PIM) для ресурсов Azure поддерживает типы ресурсов группы управления

**Тип.** Новая функция.  
**Категория службы.** Управление привилегированными пользователями.  
**Возможности продукта.** Управление привилегированными пользователями.
 
Теперь параметры активации и назначения JIT могут применяться к типам ресурсов группы управления так же, как для подписки, групп ресурсов и ресурсов (таких как виртуальные машины, службы приложений и т. д.). Кроме того, любой пользователь с ролью, которая предоставляет доступ с правами администратора к группе управления, может обнаружить этот ресурс и управлять им в PIM.

Дополнительные сведения о PIM и ресурсах Azure см. в статье [Обнаружение ресурсов Azure и управление ими с помощью управления привилегированными пользователями](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources).
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Функция доступа к приложениям (предварительная версия) обеспечивает более быстрый доступ к порталу Azure AD

**Тип.** Новая функция.  
**Категория службы.** Управление привилегированными пользователями.  
**Возможности продукта.** Управление привилегированными пользователями.
 
Сегодня при активации роли с помощью PIM может потребоваться более 10 минут, чтобы разрешения вступили в силу. Если вы решите использовать функцию доступа к приложениям, которая в настоящее время находится в режиме общедоступной предварительной версии, администраторы смогут получить доступ к порталу Azure AD, как только будет выполнен запрос активации.

В настоящее время функция доступа к приложениям поддерживает только интерфейс портала Azure AD и ресурсы Azure. Дополнительные сведения о PIM и доступе к приложениям см. в статье [Что такое Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>В коллекции приложений Azure AD доступны новые федеративные приложения (август 2018 г.)

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
В августе 2018 года мы добавили эти 16 новых приложений с поддержкой федерации в коллекцию приложений:

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [Bridgeline Unbound](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [Sauce Labs - Mobile and Web Testing](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [Meta Networks Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [Way We Do](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [ProMaster (by Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [Dossier](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F - Expense reports](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Comm100 Live Chat](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Теперь в Azure AD Application Proxy доступна собственная поддержка Tableau

**Тип.** Измененная функция.  
**Категория службы.** Прокси-сервер приложения.  
**Возможность продукта.** Контроль доступа.

Благодаря переходу с использования OpenID Connect на протокол предоставления кода OAuth 2.0 для протокола предварительной проверки подлинности вам больше не нужно выполнять дополнительную настройку для использования Tableau с Application Proxy. Кроме того, это изменение протокола позволяет Application Proxy поддерживать более современные приложения, используя только перенаправления HTTP, которые обычно поддерживаются в тегах JavaScript и HTML.

Дополнительные сведения о собственной поддержке Tableau в Azure AD Application Proxy см. в [этой записи блога](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support).

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Поддержка новой возможности: добавление Google в качестве поставщика удостоверений для гостевых пользователей B2B в Azure Active Directory (предварительная версия)

**Тип.** Новая функция.  
**Категория службы.** B2B.  
**Возможности продукта.** B2B и B2C.

Настроив федерацию с Google в своей организации, вы можете разрешить приглашенным пользователям Gmail использовать ваши общедоступные приложения и ресурсы со своей учетной записью Google, то есть им не придется создавать личную учетную запись Майкрософт (MSA) или учетную запись AAD.

Это общедоступная предварительная версия. Дополнительные сведения о федерации Google см. статье [Добавление Google в качестве поставщика удостоверений для гостевых пользователей B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

## <a name="july-2018"></a>Июль 2018 г.

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Усовершенствования уведомлений по электронной почте Azure Active Directory

**Тип.** Измененная функция.  
**Категория службы.** Другая.  
**Возможности продукта.** Управление жизненным циклом удостоверений.
 
В сообщениях электронной почты Azure Active Directory (Azure AD) теперь отображается обновленный дизайн, изменения адреса электронной почты отправителя и отображаемого имени отправителя при их отправке из следующих служб.
 
- Проверки доступа Azure AD
- Azure AD Connect Health, 
- Защита идентификации Azure AD 
- Azure AD Privileged Identity Management
- Уведомления об истечении срока действия корпоративного приложения
- Уведомления об подготовке к работе корпоративного приложения
 
Уведомления будут отправляться по электронной почте со следующего адреса и отображаемого имени.

- Адрес электронной почты: azure-noreply@microsoft.com
- Отображаемое имя: Microsoft Azure
 
Примеры некоторых новых дизайнов писем и дополнительную информацию см. в разделе [Email notifications in Azure AD PIM](https://go.microsoft.com/fwlink/?linkid=2005832) (Уведомления по электронной почте в Azure AD PIM).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Журналы действий Azure AD теперь доступны через Azure Monitor

**Тип.** Новая функция.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.

Журналы действий Azure AD теперь доступны в общедоступной предварительной версии для Azure Monitor (служба мониторинга всей платформы Azure). Azure Monitor предлагает долгосрочное хранение и простую интеграцию, а также следующие улучшения:

- долгосрочное хранение за счет перенаправления файлов журналов в собственную учетную запись хранения Azure;

- простая интеграция SIEM без необходимости написания или сохранения настраиваемых сценариев;

- простая интеграция с собственными настраиваемыми решениями, инструментами аналитики или решениями по управлению инцидентами.

Дополнительные сведения об этих новых возможностях см. в записи блога, посвященной [общедоступной предварительной версии журналов действий Azure AD в системе диагностики Azure Monitor](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/), а также статье [Журналы действий Azure AD в Azure Monitor (предварительная версия)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Сведения о условном доступе добавлены в отчет о входе в Azure AD

**Тип.** Новая функция.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Безопасность и защита удостоверений.
 
Это обновление позволяет увидеть, оценка каких политик выполняется, когда пользователь входит в систему, а также результат политики. Кроме того, в отчет теперь входит тип клиентского приложения, используемого пользователем, поэтому вы можете идентифицировать трафик устаревшего протокола. В записях отчетов теперь также можно выполнить поиск по идентификатору корреляции, который можно найти в сообщении об ошибке пользователя и использовать для идентификации и устранения неполадок соответствующего запроса на вход.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Просмотр устаревших способов аутентификации через журналы действий входа

**Тип.** Новая функция.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.
 
С появлением поля **Клиентское приложение** в журналах действий входа клиенты теперь могут видеть, какие пользователи применяют устаревшие способы аутентификации. Клиенты смогут получить доступ к этим данным с помощью API MS Graph для входов или журналов действий входа на портале Azure AD. Там же можно использовать элемент управления **Клиентское приложение** для фильтрации устаревших способов аутентификации. Дополнительные сведения см. в документации.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Новые федеративные приложения, доступные в коллекции приложений Azure AD (июль 2018 г.)

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
В июле 2018 года мы добавили 16 новых приложений с поддержкой федерации в коллекцию приложений:

[Innovation Hub](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [Certain Admin SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), PSUC Staging, [iPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [Screencast-O-Matic](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial), PowerSchool Unified Classroom, [Eli Onboarding](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [Bomgar Remote Support](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [Nimblex](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [Imagineer WebVision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial), [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [SecureW2 JoinNow Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](https://review.docs.microsoft.com/azure/active-directory/saas-apps/kanbanize-tutorial), [SmartLPA](https://review.docs.microsoft.com/azure/active-directory/saas-apps/smartlpa-tutorial), [Skills Base](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial).

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Новые возможности интеграции для подготовки пользователей с приложениями SaaS — июль 2018 г.

**Тип.** Новая функция.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
Служба Azure AD предоставляет возможность автоматизировать создание, обслуживание и удаление удостоверений пользователей в приложениях SaaS, таких как Dropbox, Salesforce, ServiceNow и т. д. В июле 2018 года мы добавили поддержку подготовки пользователей в следующие приложения в коллекции приложений Azure AD:

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusly](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

Список всех приложений, которые поддерживают подготовку пользователей в коллекции Azure AD, см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health для синхронизации — более простой способ исправить ошибки синхронизации повторяющихся и потерянных атрибутов

**Тип.** Новая функция.  
**Категория службы.** AD Connect.  
**Возможности продукта.** Мониторинг и отчетность.
 
Azure AD Connect Health предоставляет функцию самостоятельного устранения ошибок, позволяющую выявить и исправить ошибки синхронизации. С помощью этой функции можно устранять ошибки синхронизации повторяющихся атрибутов и ошибки потерянных из Azure AD объектов. Этот способ диагностики обеспечивает следующие преимущества:

- Сужает круг ошибок синхронизации повторяющихся атрибутов, предоставляя нужные исправления.

- Применяет исправление для выделенных сценариев Azure AD, устраняя ошибки за одно действие.

- Для включения и использования этой функции не требуется обновление или настройка.

Дополнительные сведения см. в статье [Диагностика и устранение ошибок синхронизации повторяющихся атрибутов](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors).

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Визуальные обновления для событий входа MSA и Azure AD

**Тип.** Измененная функция.  
**Категория службы.** Azure AD.  
**Возможности продукта.** Проверка подлинности пользователей.

Мы обновили пользовательский интерфейс для входа в веб-службы Майкрософт, такие как Office 365 и Azure. Благодаря этому изменению экраны стали менее загроможденными и более понятными. Дополнительные сведения об этом изменении см. в записи блога, посвященной [предстоящим улучшениям интерфейса входа Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/).

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Новый выпуск Azure AD Connect — июль 2018 г.

**Тип.** Измененная функция.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта.** Управление жизненным циклом удостоверений.

Последний выпуск Azure AD Connect включает в себя: 

- исправления ошибок и обновления функций поддержки; 

- общедоступную версию интеграции между Ping и Federate;

- обновления для последней версии клиента SQL 2012. 

Дополнительные сведения об этом обновлении см. в статье [Azure AD Connect. История выпусков версий](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history).

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Обновления условий использования пользовательского интерфейса для конечных пользователей

**Тип.** Измененная функция.  
**Категория службы.** Условия использования.  
**Возможности продукта.** Система управления.

В пользовательском интерфейсе с условиями использования мы обновляем строку принятия.

**Текущий текст.** "Чтобы получить доступ к ресурсам [имя_клиента], вы должны принять условия использования".<br>**Новый текст.** "Чтобы получить доступ к ресурсам [имя_клиента], вы должны прочитать условия использования".

**Текущий текст.** "Выбирая параметр "Принять", вы подтверждаете, что соглашаетесь со всеми вышеупомянутыми условиями использования".<br>**Новый текст.** "Нажмите кнопку "Принять", чтобы подтвердить, что вы прочитали и поняли условия использования".

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Сквозная проверка подлинности поддерживает устаревшие протоколы и приложения

**Тип.** Измененная функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Проверка подлинности пользователей.
 
Сквозная проверка подлинности теперь поддерживает устаревшие протоколы и приложения. Теперь полностью поддерживаются следующие ограничения:

- Входы пользователей в устаревшие клиентские приложения Office (Office 2010 и Office 2013) без необходимости применения современной проверки подлинности.

- Общий доступ к календарю и получение сведений о доступности в гибридных средах Exchange поддерживается только для Office 2010.

- Входы пользователей в клиентские приложения Skype для бизнеса без необходимости применения современной проверки подлинности.

- Вход пользователей в PowerShell версии 1.0.

- Программа регистрации устройств Apple (Apple DEP) с помощью помощника по настройке iOS. 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Управление конвергированными сведениями для защиты для самостоятельного сброса пароля и Многофакторной идентификации

**Тип.** Новая функция.  
**Категория службы:** SSPR  
**Возможности продукта.** Проверка подлинности пользователей.

Эта новая функция позволяет пользователям управлять своими сведениями для защиты (например, номером телефона, адресом электронной почты, мобильным приложением и т. д.) для самостоятельного сброса пароля (SSPR) и Многофакторной идентификации (MFA) в одном интерфейсе. Пользователям больше не нужно регистрировать одни и те же сведения для защиты для SSPR и MFA в двух разных интерфейсах. Этот новый интерфейс также применяется к пользователям с SSPR или MFA.

Если организация не применяет регистрацию MFA или SSPR, пользователи могут зарегистрировать свои сведения для защиты через портал **My Apps**. Оттуда они могут зарегистрировать любые методы, разрешенные для MFA или SSPR. 

Это общедоступная предварительная версия. Администраторы могут включить новую функцию (при необходимости) для выбранной группы пользователей или всех пользователей в клиенте.

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Использование приложения Microsoft Authenticator для проверки идентификации при сбросе пароля

**Тип.** Измененная функция.  
**Категория службы:** SSPR  
**Возможности продукта.** Проверка подлинности пользователей.

Эта функция позволяет пользователям, не являющимся администраторами, подтвердить свою личность при сбросе пароля с помощью уведомления или кода от приложения Microsoft Authenticator (или любого другого приложения проверки подлинности). Когда администратор включит этот метод самостоятельного сброса пароля, пользователи, зарегистрировавшие мобильное приложение через aka.ms/mfasetup или aka.ms/setupsecurityinfo, смогут использовать свое мобильное приложение как метод проверки при сбросе пароля.

Уведомление от мобильного приложения можно включить только как часть политики, которая требует двух методов для сброса пароля.

---

## <a name="june-2018"></a>Июнь 2018 г.

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Уведомление об изменении: исправление безопасности для потока делегированной авторизации для приложений, использующих API журналов действий Azure AD

**Тип.** Планирование изменений.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.

Из-за более строгих требований к обеспечению безопасности нам пришлось внести изменения в разрешения для приложений, использующих поток делегированной авторизации для доступа к [API для журналов действий Azure AD](https://aka.ms/aadreportsapi). Это изменение вступило в силу **26 июня 2018 г**.

Если какое-либо приложение использует API для журналов действий Azure AD, сделайте следующее, чтобы убедиться, что приложение будет работать корректно после внесения изменения.

**Обновление разрешений для приложения**

1. Войдите на портал Azure и выберите **Azure Active Directory**, а затем выберите **Регистрация приложений**.
2. Выберите приложение, использующее API для журналов действий Azure AD, щелкните **Параметры**, а затем — **Необходимые разрешения** и API **Windows Azure Active Directory**.
3. В области **Необходимые разрешения** колонки **Разрешить доступ** установите флажок рядом с пунктом **Read directory** (Чтение каталога), а затем выберите **Сохранить**.
4. Щелкните **Предоставить разрешения** и затем выберите **Да**.
    
    >[!Note]
    >Необходимо быть глобальным администратором для предоставления разрешений приложению.

Дополнительные сведения см. в разделе [Предоставление разрешений](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions) статьи "Предварительные требования для доступа к API отчетов Azure Active Directory".

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Настройка параметров TLS для подключения к службам Azure AD, чтобы соответствовать требованиям стандарта PCI DSS

**Тип.** Новая функция.  
**Категория службы:** недоступно  
**Возможности продукта.** Платформа.

Transport Layer Security (TLS) — это протокол, обеспечивающий конфиденциальность и целостность данных между двумя взаимодействующими приложениями, и наиболее широко используемый сегодня протокол безопасности.

[Совет по стандартам безопасности индустрии платежных карт](https://www.pcisecuritystandards.org/) определил, что ранние версии TLS и SSL нужно перестать использовать в пользу новых более безопасных протоколов приложений, соответствующих требованиям, начиная с **30 июня 2018 года**. Это изменение означает, что при подключении к службам Azure AD и требовании соответствия стандарту PCI DSS необходимо отключить TLS 1.0. Доступно несколько версий TLS, но TLS 1.2 — это последняя версия, доступная для служб Azure Active Directory. Корпорация Майкрософт рекомендует перейти непосредственно на TLS 1.2 для комбинаций "клиент — сервер" и "браузер — сервер".

Устаревшие браузеры могут не поддерживать более новые версии TLS, такие как TLS 1.2. Чтобы узнать, какие версии TLS поддерживаются в веб-браузере, перейдите на сайт [Qualys SSL Labs](https://www.ssllabs.com/) и выберите **Test your browser** (Протестировать браузер). Мы рекомендуем вам перейти на последнюю версию веб-браузера и предпочтительно включить только TLS 1.2.

**Включение TLS 1.2 с помощью браузера**

- **Microsoft Edge и Internet Explorer (оба используют Internet Explorer)**

    1. Откройте Internet Explorer, выберите **Сервис** > **Свойства браузера** > **Дополнительно**.
    2. В области **Безопасность** выберите **Использовать TLS 1.2**, а затем нажмите кнопку **ОК**.
    3. Закройте все окна браузера и перезапустите Internet Explorer. 

- **Google Chrome**

    1. Откройте браузер Google Chrome, введите в адресной строке *chrome://settings/* и нажмите клавишу **ВВОД**.
    2. Разверните параметры **Дополнительные**, перейдите в область **Система** и выберите **Настройки прокси-сервера**.
    3. В области **Свойства: Интернет** выберите вкладку **Дополнительно**, перейдите в область **Безопасность** и выберите **Использовать TLS 1.2**, а затем нажмите кнопку **ОК**.
    4. Закройте все окна браузера и перезапустите Google Chrome.

- **Mozilla Firefox**

    1. Откройте Firefox, введите в адресной строке *about:config*, а затем нажмите клавишу **ВВОД**.
    2. Найдите термин *TLS*, а затем выберите запись **security.tls.version.max**.
    3. Задайте значение **3**, чтобы браузер использовал версию TLS 1.2, а затем нажмите кнопку **ОК**.

        >[!NOTE]
        >Версия Firefox 60.0 поддерживает TLS 1.3, поэтому также можно задать для security.tls.version.max значение **4**.

    4. Закройте все окна браузера и перезапустите Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Новые федеративные приложения, доступные в коллекции приложений Azure AD (июнь 2018 г.)

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
В июне 2018 года мы добавили эти 15 новых приложений с поддержкой федерации в коллекцию приложений:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), [Settling music](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), [бизнес-приложение с включенным токеном SAML 1.1](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [Supermood](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), [Autotask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Endpoint Backup](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Skyhigh Networks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho One](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [Локальная среда SharePoint](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [ForeSee CX Suite](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Защита паролей Azure AD доступна в общедоступной предварительной версии

**Тип.** Новая функция.  
**Категория службы.** Защита идентификации.  
**Возможности продукта.** Проверка подлинности пользователей.

Защиту паролей Azure AD можно использовать, чтобы исключить легко угадываемые пароли из вашей среды. Исключение этих паролей помогает снизить риск компрометации в случае атаки с подбором пароля.

В частности защита паролей Azure AD помогает вам:

- защитить учетные записи вашей организации как в Azure AD, так и в Windows Server Active Directory (AD); 
- предотвратить использование пользователями паролей из списка 500 наиболее часто используемых паролей и более 1 миллиона вариантов замены этих паролей;
- управлять защитой паролей Azure AD из одного расположения на портале Azure AD для Azure AD и локального экземпляра Windows Server AD.

Дополнительные сведения о защите паролей Azure AD см. в статье [Исключение неправильных паролей в организации](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Новый шаблон политики условного доступа "все гости", созданный во время создания условий использования

**Тип.** Новая функция.  
**Категория службы.** Условия использования.  
**Возможности продукта.** Система управления.

При создании условий использования также создается новый шаблон политики условного доступа для "всех гостей" и "все приложения". Этот новый шаблон политики применяет только что созданные условия использования, упрощая создание и применение процесса для гостей.

Дополнительные сведения см. в статье [Функция "Условия использования Azure Active Directory" (предварительная версия)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Новый шаблон "настраиваемый" политики условного доступа, созданный во время создания условий использования

**Тип.** Новая функция.  
**Категория службы.** Условия использования.  
**Возможности продукта.** Система управления.

При создании условий использования также создается новый шаблон "Настраиваемая политика условного доступа". Этот новый шаблон политики позволяет создать условий использования, а затем сразу перейти в колонку создания политики условного доступа без необходимости вручную перемещаться по порталу.

Дополнительные сведения см. в статье [Функция "Условия использования Azure Active Directory" (предварительная версия)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Новое комплексное руководство по развертыванию Многофакторной идентификации Azure

**Тип.** Новая функция.  
**Категория службы.** Другая.  
**Возможности продукта.** Безопасность и защита удостоверений.
 
Мы выпустили новые пошаговые инструкции по развертываю Многофакторной идентификации Azure в организации.

Чтобы просмотреть руководство по развертыванию MFA, перейдите к репозиторию [Identity Deployment Guides](https://aka.ms/DeploymentPlans) в GitHub. Чтобы оставить отзыв о руководстве по развертыванию, используйте [форму обратной связи плана развертывания](https://aka.ms/deploymentplanfeedback). Если у вас возникли вопросы о руководствах по развертыванию, свяжитесь с нами на сайте [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Делегированные роли управления приложением Azure AD в общедоступной предварительной версии

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможность продукта.** Контроль доступа.

Теперь администратор может делегировать задачи управления приложением без присвоения роли глобального администратора. Новые роли и возможности:

- **Новые стандартные роли администратора Azure AD:**

    - **Администратор приложений.** Предоставляет возможность управлять всеми аспектами всех приложений, в том числе регистрацией, параметрами единого входа, назначением приложений и лицензированием, параметрами прокси-сервера приложений и согласием (за исключением ресурсов Azure AD).

    - **Администратор облачных приложений.** Предоставляет все возможности администратора приложений, за исключением управления прокси-сервером приложений, так как он не предоставляет локальный доступ.

    - **Разработчик приложений.** Предоставляет возможность создавать регистрации приложений, даже если отключен параметр, **позволяющий пользователям регистрировать приложения**.

- **Владение (настраивается для регистрации отдельного приложения и для приложения отдельного предприятия, аналогично процессу группового владения:**
 
    - **Владелец регистрации приложения.** Предоставляет возможность управлять всеми аспектами имеющейся регистрации приложения, включая манифест приложения и добавление дополнительных владельцев.

    - **Владелец корпоративного приложения.** Предоставляет возможность управлять многими аспектами собственных корпоративных приложений, в том числе параметрами единого входа, назначением приложений и согласием (за исключением ресурсов Azure AD).

Дополнительные сведения об общедоступной предварительной версии см. в записи блога [Hallelujah! Azure AD delegated application management roles are in public preview!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) (Делегированные роли управления приложением Azure AD в общедоступной предварительной версии) . Дополнительные сведения о ролях и разрешениях см. в статье [Назначение ролей администратора в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

---

## <a name="may-2018"></a>Май 2018 г.

### <a name="expressroute-support-changes"></a>Изменения в поддержке ExpressRoute

**Тип.** Планирование изменений.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Платформа.  

Предложения модели "программное обеспечение как услуга", такие как Azure Active Directory (Azure AD), лучше всего работают напрямую через Интернет, не требуя ExpressRoute или любых других частных VPN-туннелей. По этой причине с **1 августа 2018** мы прекращаем поддержку ExpressRoute для служб Azure AD с помощью общедоступного пиринга Azure и сообществ Azure в пиринге корпорации Майкрософт. Во всех службах, на которые повлияет это изменение, трафик Azure AD будет постепенно переходить от ExpressRoute к Интернету.

Мы изменяем способ предоставления поддержки, но понимаем, что в некоторых ситуациях вам может понадобиться выделенный набор каналов для трафика проверки подлинности. Поэтому в Azure AD будут и дальше поддерживаться ограничения диапазона IP-адресов каждого клиента с использованием ExpressRoute и служб, в которых уже используется пиринг Майкрософт, в сообществе "Другие онлайн-службы Office 365" Если затронуты ваши службы, но вам требуется ExpressRoute, необходимо сделать следующее:

- **Если вы используете общедоступный пиринг Azure.** Необходимо перейти к пирингу Майкрософт и зарегистрироваться в сообществе **Другие онлайн-службы Office 365 (12076:5100)** . Дополнительные сведения см. в статье [Переход с общедоступного пиринга на пиринг Майкрософт](https://docs.microsoft.com/azure/expressroute/how-to-move-peering).

- **Если вы используете пиринг Майкрософт.** Зарегистрируйтесь в сообществе **Другие онлайн-службы Office 365 (12076:5100)** . Дополнительные сведения о требованиях к маршрутизации см. в разделе [Поддержка сообществ BGP](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) статьи о требованиях к маршрутизации ExpressRoute.

Если вам необходимо продолжить использование выделенных каналов, потребуется поговорить со службой технической поддержки учетных записей Майкрософт о том, как получить разрешение на использование сообщества**Другие онлайн-службы Office 365 (12076:5100)** . Контрольный совет MS Office проверит, нужны ли вам эти каналы, и убедится, что вы понимаете технические последствия их использования. Несанкционированные подписки, пытающиеся создать фильтры маршрутов для Office 365, получат сообщение об ошибке. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>API Microsoft Graph для администрирования условий использования

**Тип.** Новая функция.  
**Категория службы.** Условия использования.  
**Возможности продукта.** Возможности для разработчиков
 
Мы добавили Microsoft Graph API для администрирования условий использования Azure AD. Вы можете создавать, обновлять и удалять условия использования объекта.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Добавление мультитенантной конечной точки Azure AD в качестве поставщика удостоверений в Azure AD B2C

**Тип.** Новая функция.  
**Категория службы.** B2C — управление удостоверениями клиентов.  
**Возможности продукта.** B2B и B2C.
 
Теперь с помощью пользовательских политик вы можете добавить обычную конечную точку Azure AD в качестве поставщика удостоверений в Azure AD B2C. Так у вас будет единая точка входа для всех пользователей Azure AD, которые входят в ваши приложения. Дополнительные сведения см. в статье [Azure Active Directory B2C. Предоставление пользователям возможности входить в мультитенантный поставщик удостоверений Azure AD с помощью пользовательских политик](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Использование внутренних URL-адресов для доступа к приложениям из любого расположения с помощью компонента "Расширение защищенного входа в мои приложения" и Azure AD Application Proxy

**Тип.** Новая функция.  
**Категория службы.** "Мои приложения".  
**Возможности продукта.** Единый вход.
 
Теперь пользователи могут получить доступ к приложениям через внутренние URL-адреса даже за пределами корпоративной сети с помощью компонента "Расширение защищенного входа в мои приложения" для Azure AD. Эта возможность поддерживается в любых приложениях, опубликованных с помощью Azure AD Application Proxy, и браузерах, на которых также установлено расширение "Панель доступа". Возможность перенаправления URL-адресов включается автоматически, когда пользователь входит в расширение. Расширение можно скачать для браузеров [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367) и [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory — данные в Европе для европейских клиентов

**Тип.** Новая функция.  
**Категория службы.** Другая.  
**Возможности продукта.** GoLocal.

Для соблюдения законов о конфиденциальности и другого европейского законодательства данные клиентов из Европы должны оставаться в Европе и не реплицироваться за пределы европейских центров обработки данных. См. дополнительные сведения о том, [какая информация об удостоверениях будет храниться в Европе и за ее пределами](https://go.microsoft.com/fwlink/?linkid=872328). 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Новые возможности интеграции для подготовки пользователей с приложениями SaaS — май 2018 г.

**Тип.** Новая функция.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
Служба Azure AD предоставляет возможность автоматизировать создание, обслуживание и удаление удостоверений пользователей в приложениях SaaS, таких как Dropbox, Salesforce, ServiceNow и т. д. В мае 2018 года мы добавили поддержку подготовки пользователей в следующие приложения в коллекции приложений Azure AD:

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [Cornerstone OnDemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Список всех приложений, которые поддерживают подготовку пользователей, в коллекции Azure AD см. в статье [https://aka.ms/appstutorial](https://aka.ms/appstutorial).

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Функция проверки доступа Azure AD для групп и приложений теперь поддерживает повторяющиеся проверки

**Тип.** Новая функция.  
**Категория службы.** Проверка доступа.  
**Возможности продукта.** Система управления.
 
Функция проверки доступа для групп и приложений стала общедоступной в Azure AD Premium P2.  Администраторы смогут настраивать автоматическое повторение операций проверки доступа для членств в группах и назначений приложений с регулярным интервалом, например ежемесячно или ежеквартально.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Журналы действий Azure AD (вход и аудит) теперь доступны через MS Graph

**Тип.** Новая функция.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.
 
Журналы действий Azure AD, в том числе журналы входа и аудита, теперь доступны через MS Graph. Мы предоставили через MS Graph две конечных точки для доступа к этим журналам. Ознакомьтесь с [документацией](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) по программному доступу к API отчетов Azure AD. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Улучшения процедуры активации B2B и возможности покинуть организацию

**Тип.** Новая функция.  
**Категория службы.** B2B.  
**Возможности продукта.** B2B и B2C.

**Оперативная активация.** Когда вы предоставляете гостевому пользователю ресурс для общего доступа с помощью API B2B, вам не нужно отправлять специальное сообщение с приглашением. В большинстве случаев гостевой пользователь может обратиться к ресурсу, после чего выполняется оперативная процедура активации. Больше никаких недоразумений из-за пропущенных сообщений электронной почты. Больше никаких вопросов "А вы щелкнули ссылку активации, предоставленную системой?" Это означает, что когда SPO использует диспетчер приглашений, облачные вложения могут иметь одинаковый канонический URL-адрес для всех пользователей — как внутренних, так и внешних — в любом состоянии активации.

**Современная процедура активации.** Больше никакой целевой страницы активации с разделением экрана. Пользователи увидят современный согласованный интерфейс с заявлением о конфиденциальности приглашающей организации, как это происходит для приложений сторонних разработчиков.

**Гостевые пользователи могут покинуть организацию.** Когда пользователь решает прекратить сотрудничество с организацией, он может самостоятельно покинуть ее. Больше никаких звонков администратору приглашающей организации с просьбой об удалении и никаких запросов в службу поддержки.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Новые федеративные приложения, доступные в коллекции приложений Azure AD — май 2018 г.

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
В мае 2018 года мы добавили 18 новых приложений с поддержкой федерации в нашу коллекцию приложений:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix Data3Sixty Govern, [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [Jamf Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [Envi MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe Captivate Prime](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [Montage Online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial), [まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial), OpenReel, [Arc Publishing - SSO](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [Riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [Flock](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [Reviewsnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial).

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial).

Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Новое пошаговое руководство по развертыванию для Azure Active Directory

**Тип.** Новая функция.  
**Категория службы.** Другая.  
**Возможности продукта.** Каталог.
 
Новые, пошаговые инструкции по развертыванию Azure Active Directory (Azure AD), включая самостоятельный сброс пароля (SSPR), единый вход (SSO), условный доступ (ЦС), прокси приложения, подготовку пользователей службы федерации Active Directory (AD FS) (ADFS) для Сквозная проверка подлинности (PTA) и ADFS для синхронизации хэшей паролей (PHS).

Чтобы просмотреть руководство по развертыванию, перейдите к репозиторию [Identity Deployment Guides](https://aka.ms/DeploymentPlans) в GitHub. Чтобы оставить отзыв о руководстве по развертыванию, используйте [форму обратной связи плана развертывания](https://aka.ms/deploymentplanfeedback). Если у вас возникли вопросы о руководствах по развертыванию, свяжитесь с нами на сайте [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Поиск корпоративных приложений — загрузка дополнительных приложений

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Единый вход.
 
У вас возникают трудности с поиском приложений или субъектов-служб? Теперь вы можете загружать больше приложений в списке всех корпоративных приложений. По умолчанию отображается 20 приложений. Теперь можно щелкнуть **Загрузить больше**, чтобы просмотреть дополнительные приложения. 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>Майский выпуск AADConnect содержит общедоступную предварительную версию функции интеграции с PingFederate, важные обновления для системы безопасности, множество исправлений ошибок и новые средства для устранения неполадок. 

**Тип.** Измененная функция.  
**Категория службы.** AD Connect.  
**Возможности продукта.** Управление жизненным циклом удостоверений.
 
Майский выпуск AADConnect содержит общедоступную предварительную версию функции интеграции с PingFederate, важные обновления для системы безопасности, множество исправлений ошибок и новые средства для устранения неполадок. См. [заметки о выпуске](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Проверки доступа Azure AD: автоматическое применение

**Тип.** Измененная функция.  
**Категория службы.** Проверка доступа.  
**Возможности продукта.** Система управления.

Функция проверки доступа для групп и приложений стала общедоступной в Azure AD Premium P2. Администратор может автоматически применить изменения рецензента к соответствующей группе или соответствующему приложению после завершения проверки доступа. Кроме того, он может указать, что происходит с доступом пользователя, если рецензенты не ответили, а именно: запрет доступа, сохранение доступа или следование рекомендациям системы. 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>Маркеры идентификации теперь невозможно возвращать с помощью режима ответа query для новых приложений. 

**Тип.** Измененная функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Проверка подлинности пользователей.
 
Приложения, созданные 25 апреля 2018 года или позднее, больше не могут запрашивать значение для **id_token** с использованием режима ответа **query**.  Это обеспечивает соответствие Azure AD спецификациям OIDC и помогает снизить уязвимость ваших приложений перед атаками.  Приложения, созданные до 25 апреля 2018 года, могут использовать режим ответа **query** с типом запроса **id_token**.  При запросе значения id_token из AAD возвращается ошибка **AADSTS70007: query не является поддерживаемым значением для режима response_mode при запросе токена**.

Значения **fragment** и **form_post** для режимов ответа по-прежнему доступны при создании новых объектов приложения (например, для использования прокси приложения). Вам следует использовать одно из этих значений response_mode, прежде чем создавать приложения.  

---
 
## <a name="april-2018"></a>Апрель 2018 г. 

### <a name="azure-ad-b2c-access-token-are-ga"></a>Общедоступная версия маркера доступа Azure AD B2C

**Тип.** Новая функция.  
**Категория службы.** B2C — управление удостоверениями клиентов.  
**Возможности продукта.** B2B и B2C. 

Теперь доступ к интерфейсам веб-API, защищенным Azure AD B2C, можно получать с помощью маркеров доступа. Эта функция переходит от общедоступной предварительной версии к общедоступной версии. Улучшен пользовательский интерфейс для настройки приложений Azure AD B2C и веб-API, а также внесены другие незначительные улучшения.
 
Дополнительные сведения см. в статье [Azure AD B2C: запрос маркеров доступа](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens).

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Тестирование конфигурации единого входа для приложений на основе SAML

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Единый вход.

При настройке приложений с функцией единого входа на основе SAML вы можете протестировать интеграцию на странице конфигурации. Если в процессе входа возникнет ошибка, ее можно указать в интерфейсе тестирования, и Azure AD предоставит вам пошаговые инструкции по решению конкретной проблемы.

Дополнительные сведения см. здесь:

- [Настройка единого входа для приложений, которых нет в коллекции приложений Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Отладка единого входа на основе SAML в приложения в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Условия использования Azure AD теперь имеют отчеты "на пользователя"

**Тип.** Новая функция.  
**Категория службы.** Условия использования.  
**Возможности продукта.** Соответствие требованиям.
 
Администраторы теперь могут выбрать заданные условия использования и просмотреть список всех пользователей, которые приняли эти условия, а также дату и время этого события.

Дополнительные сведения см. в статье [Функция "Условия использования Azure Active Directory"](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: обнаружение ненадежных IP-адресов для защиты блокировки экстрасети AD FS 

**Тип.** Новая функция.  
**Категория службы.** Другая.  
**Возможности продукта.** Мониторинг и отчетность.

Connect Health теперь поддерживает обнаружение IP-адресов, для которых превышается пороговое количество неудачных попыток входа с именем пользователя и паролем в час или в день. Эта функция предоставляет следующие возможности:

- Полный отчет, который содержит IP-адрес и число неудачных попыток входа и формируется каждый час или каждый день с настраиваемым пороговым значением.
- Оповещения по электронной почте, которые появляются, когда для конкретного IP-адреса превышается порог неудачных попыток входа с именем пользователя и паролем в час или в день.
- Возможность скачать отчет для подробного анализа данных

Дополнительные сведения см. в разделе об [отчете по ненадежным IP-адресам](https://aka.ms/aadchriskyip).

---
 
### <a name="easy-app-config-with-metadata-file-or-url"></a>Простая настройка приложений с помощью файла метаданных или URL-адреса

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Единый вход.

На странице приложений Enterprise администраторы могут отправить файл метаданных SAML для настройки входа на основе SAML для приложений из коллекции AAD и не входящих в нее.

Кроме того, URL-адрес метаданных федерации приложения Azure AD можно использовать для настройки единого входа в целевом приложении.

Дополнительные сведения см. в статье [Настройка единого входа для приложений, которых нет в коллекции приложений Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Выпущена общедоступная версия функции "Условия использования"

**Тип.** Новая функция.  
**Категория службы.** Условия использования.  
**Возможности продукта.** Соответствие требованиям.
 

Условия использования Azure AD были переведены из общедоступной предварительной версии в общую версию.

Дополнительные сведения см. в статье [Функция "Условия использования Azure Active Directory"](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Предоставление или отзыв приглашений пользователям B2B из отдельных организаций

**Тип.** Новая функция.  
**Категория службы.** B2B.  
**Возможности продукта.** B2B и B2C.
 

Теперь можно указать, с какими партнерскими организациями вы хотите совместно использовать данные и совместно работать в службе совместной работы Azure AD B2B. Для этого можно создать список определенных разрешенных или запрещенных доменов. При блокировке домена с помощью этих возможностей сотрудники больше не смогут отправлять приглашения пользователям из этого домена.

Это помогает контролировать доступ к ресурсам, обеспечивая при этом удобную работу для утвержденных пользователей.

Эта функция совместной работы B2B доступна для всех Azure Active Directory клиентов и может использоваться в сочетании с функциями Azure AD Premium, такими как условный доступ и защита идентификации, для более точного контроля над тем, когда и как осуществляется вход внешних бизнес-пользователей. в и получите доступ.

Дополнительные сведения см. в статье [Предоставление или отзыв приглашений пользователям B2B из отдельных организаций](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Новые федеративные приложения доступны в коллекции приложений Azure AD

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.

В апреле 2018 года мы добавили 13 новых приложений с поддержкой федерации в нашу коллекцию приложений:

Criterion HCM, [FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [Secret Server (локальный)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), [Dynamic Signal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), [OrgChart Now](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [AppNeta Performance Monitor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial), [Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), [Cisco Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), Shelf и [SafetyNet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial).

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial).

Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Предоставление пользователям B2B в Azure AD доступа к локальным приложениям (общедоступная предварительная версия)

**Тип.** Новая функция.  
**Категория службы.** B2B.  
**Возможности продукта.** B2B и B2C.

Теперь организации, использующие возможности совместной работы в Azure Active Directory (AAD) B2B для приглашения гостевых пользователей из партнерских организаций в AAD, могут предоставлять таким пользователям B2B доступ к локальным приложениям. В таких приложениях нужно использовать аутентификацию на основе SAML или встроенную проверку подлинности Windows (IWA) с ограниченным делегированием Kerberos (KCD).

Дополнительные сведения см. в статье [Предоставление пользователям B2B в Azure AD доступа к локальным приложениям](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises).

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Получение руководств по интеграции единого входа из Azure Marketplace

**Тип.** Измененная функция.  
**Категория службы.** Другая.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.

Если приложение, указанное в [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) поддерживает единый вход на основе SAML, то, щелкнув **Получить**, можно открыть руководство по интеграции, связанное с этим приложением. 

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Повышение производительности автоматической подготовки пользователей Azure AD в приложениях SaaS

**Тип.** Измененная функция.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
Ранее клиенты, которые с помощью соединителей подготавливали пользователей Azure Active Directory для приложений SaaS (например, Salesforce, ServiceNow и Box), могли испытывать снижение производительности. Так происходило, если в их клиентах Azure AD содержалось свыше 100 000 совокупных пользователей и групп, и для определения пользователей, которых следует подготовить, они использовали назначения пользователей и групп.

2 апреля 2018 года мы развернули значительные улучшения производительности для службы подготовки Azure AD, которые существенно сокращают время, необходимое на выполнение начальной синхронизации Azure Active Directory с целевыми приложениями SaaS.

В результате многие клиенты, у которых начальная синхронизация с приложениями занимала много дней или никогда не завершалась, сейчас завершают ее в течение нескольких минут или часов.

Дополнительные сведения см. в разделе [Что происходит при подготовке](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning#what-happens-during-provisioning).

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Самостоятельный сброс пароля с экрана блокировки Windows 10 для компьютеров, присоединенных к гибридной среде Azure AD

**Тип.** Измененная функция.  
**Категория службы.** Самостоятельный сброс пароля.  
**Возможности продукта.** Проверка подлинности пользователей.
 
Мы обновили компонент Windows 10 SSPR, добавив поддержку компьютеров, присоединенных к гибридной среде Azure AD. Эта функция доступна в Windows 10 RS4 и дает пользователям возможность сбрасывать пароль с экрана блокировки на компьютере с Windows 10. Эту функцию могут применять пользователи, которые включены и зарегистрированы для самостоятельного сброса пароля.

Дополнительные сведения см. в статье [Сброс пароля Azure AD на экране входа](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows).

---

## <a name="march-2018"></a>Март 2018 г.
 
### <a name="certificate-expire-notification"></a>Уведомление об истечении срока действия сертификата

**Тип.** Исправление.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Единый вход.
 
Azure AD отправляет уведомление о сертификатах с истекающим сроком действия для приложений из коллекции или не из коллекции. 

Некоторые пользователи не получают уведомления о корпоративных приложениях, для которых настроен единый вход для на основе SAML. Эта проблема была устранена. Azure AD отправляет уведомление для сертификатов, срок действия которых истекает через 7, 30 и 60 дней. Это событие отображается в журналах аудита. 

Дополнительные сведения см. здесь:

- [Управление сертификатами для федеративного единого входа в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Отчеты о действиях аудита на портале Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
 
---
 
### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Поставщики удостоверений Twitter и GitHub в Azure AD B2C

**Тип.** Новая функция.  
**Категория службы.** B2C — управление удостоверениями клиентов.  
**Возможности продукта.** B2B и B2C.
 
Теперь в Azure AD B2C можно добавить Twitter или GitHub в качестве поставщика удостоверений. От общедоступной предварительной версии Twitter переходит к общедоступной версии. Для GitHub выпускается общедоступная предварительная версия.

Дополнительные сведения см. в статье [Что такое служба совместной работы Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
 
---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Ограничение доступа к браузеру с помощью Intune Managed Browser с условным доступом на основе приложений Azure AD для iOS и Android

**Тип.** Новая функция.  
**Категория службы.** Условный доступ.  
**Возможности продукта.** Безопасность и защита удостоверений.
 
**Сейчас на этапе общедоступной предварительной версии**

**Единый вход в Intune Managed Browser.** Ваши сотрудники могут использовать единый вход через собственные клиенты (например, Microsoft Outlook) и Intune Managed Browser для всех подключенных приложений Azure AD.

**Intune Managed Browser поддержка условного доступа:** Теперь вы можете требовать от сотрудников использовать управляемый браузер Intune с помощью политик условного доступа на основе приложений.

Дополнительные сведения об этом см. в нашем [блоге](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Дополнительные сведения см. здесь:

- [Настройка условного доступа на основе приложений](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Управление доступом в Интернет с помощью политик Managed Browser в Microsoft Intune](https://aka.ms/managedbrowser)  

---
 
### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Командлеты для прокси приложения в общедоступной версии модуля PowerShell

**Тип.** Новая функция.  
**Категория службы.** Прокси-сервер приложения.  
**Возможность продукта.** Контроль доступа.
 
Теперь в общедоступной версии модуля PowerShell поддерживаются командлеты для прокси приложения. Для этого требуется своевременно обновлять модули PowerShell. Если используемая версия модуля не будет обновляться более одного года, то некоторые командлеты могут перестать работать. 

Дополнительные сведения см. в разделе [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Поддержка собственных клиентов Office 365 службой простого единого входа с помощью неинтерактивного протокола

**Тип.** Новая функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Проверка подлинности пользователей.
 
Пользователь, применяющий собственные клиенты Office 365 (версии 16.0.8730.xxxx и более поздней версии), может использовать автоматический вход с помощью простого единого входа. Поддержка этой возможности обеспечивается за счет добавления в Azure AD неинтерактивного протокола (WS-Trust).

Дополнительные сведения см. в разделе [Как работает вход с функцией простого единого входа в собственном клиенте?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)
 
---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Пользователи могут использовать автоматический вход с помощью простого единого входа, если приложение отправляет запросы на вход на клиентские конечные точки Azure AD

**Тип.** Новая функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Проверка подлинности пользователей.
 
Пользователи могут использовать автоматический вход с помощью простого единого входа, если приложение (например, `https://contoso.sharepoint.com`) отправляет запросы на вход на клиентские конечные точки Azure AD, т. е. `https://login.microsoftonline.com/contoso.com/<..>` или `https://login.microsoftonline.com/<tenant_ID>/<..>`, вместо обычной конечной точки Azure AD (`https://login.microsoftonline.com/common/<...>`).

Дополнительные сведения см. в разделе [Устранение неполадок с простым единым входом Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 

---
 
### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Необходимо добавить в параметры зоны интрасети пользователей только один URL-адрес Azure AD вместо двух (как было раньше) для развертывания простого единого входа

**Тип.** Новая функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Проверка подлинности пользователей.
 
Чтобы развернуть простой единый вход для пользователей, необходимо добавить только один URL-адрес Azure AD в параметры зоны интрасети пользователей с помощью групповой политики Active Directory: `https://autologon.microsoftazuread-sso.com`. Ранее клиентам требовалось добавить два URL-адреса.

Дополнительные сведения см. в разделе [Устранение неполадок с простым единым входом Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 
 
---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Новые федеративные приложения доступны в коллекции приложений Azure AD

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.

В марте 2018 года мы добавили 15 новых приложений с поддержкой федерации в нашу коллекцию приложений:

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), Assistant by FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), Vtiger CRM, inwink, [Amplitude](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [Trisotech Digital Enterprise Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial).

Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="pim-for-azure-resources-is-generally-available"></a>Выпущена общедоступная версия PIM для ресурсов Azure

**Тип.** Новая функция.  
**Категория службы.** Управление привилегированными пользователями.  
**Возможности продукта.** Управление привилегированными пользователями.
 
Теперь при использовании управления привилегированными пользователями Azure AD для ролей каталога можно применять возможности временного ограничения доступа и назначения для таких ролей ресурсов Azure, как подписки, группы ресурсов, виртуальные машины, а также любого другого ресурса, поддерживаемого Azure Resource Manager. Вы можете применять Многофакторную идентификацию при JIT-активации ролей и планировать активацию в соответствии с утвержденными периодами изменений. Кроме того, в этом выпуске добавлены усовершенствования, отсутствующие в общедоступной предварительной версии, включая обновленный пользовательский интерфейс, рабочие процессы утверждения и возможность расширения ролей с истекающим сроком действия и обновления ролей с истекшим сроком действия.

Дополнительные сведения см. в статье [PIM для ресурсов Azure (предварительная версия)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).
 
---
 
### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Добавление необязательных утверждений для токенов приложений (предварительная версия)

**Тип.** Новая функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Проверка подлинности пользователей.
 
Теперь приложение Azure AD может запрашивать пользовательские или необязательные утверждения в токенах JWT или SAML.  Это утверждения о пользователе или клиенте, которые не включены по умолчанию в токен из-за ограничения размера или применимости.  В настоящий момент доступна общедоступная предварительная версия этой функции для приложений Azure AD в конечных точках версии 1.0 и версии 2.0.  Ознакомьтесь с соответствующей документацией, чтобы получить сведения о том, какие утверждения могут быть добавлены и как изменить манифест приложения, чтобы запросить их.  

Дополнительные сведения см. в статье [Optional claims in Azure AD (preview)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims) (Необязательные утверждения в Azure AD (предварительная версия)).
 
---
 
### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD поддерживает PKCE для более безопасных потоков OAuth

**Тип.** Новая функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Проверка подлинности пользователей.
 
Документация по Azure AD была обновлена с учетом поддержки PKCE, что обеспечивает более безопасную связь во время потока предоставления кода авторизации OAuth 2.0.  S256 и code_challenge без шифрования поддерживаются в конечных точках версии 1.0 и версии 2.0. 

Дополнительные сведения см. в разделе [Запрос кода авторизации](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code). 
 
---
 
### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Поддержка подготовки всех значений атрибутов пользователя, доступных в API Get_Workers Workday

**Тип.** Новая функция.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
Общедоступная предварительная версия функции подготовки входящих данных из Workday в Active Directory и Azure AD теперь поддерживает возможность извлечения и подготовки всех значений атрибутов в API Get_Workers Workday. Добавлена поддержка сотен дополнительных стандартных и пользовательских атрибутов, не входящих в состав первоначальной версии соединителя подготовки входящих данных Workday.

Дополнительные сведения см. в разделе [Настройка списка атрибутов пользователя Workday](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes).

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Изменение динамического членства в группе на статическое (и наоборот)

**Тип.** Новая функция.  
**Категория службы.** Управление группами.  
**Возможности продукта.** Совместная работа.
 
Можно изменить способ управления членством в группе. Это удобно, если требуется сохранить имя и идентификатор группы в системе, чтобы все существующие ссылки на эту группу по-прежнему были действительны. При создании новой группы эти ссылки потребовалось бы обновить.
Мы обновили центр администрирования Azure AD, чтобы поддержать эту функцию. Теперь клиенты могут изменять динамическое членство в существующих группах на статическое (и наоборот). Существующие командлеты PowerShell по-прежнему доступны.

Дополнительные сведения см. [в разделе правила динамического членства для групп в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Улучшенный выход при простом едином входе

**Тип.** Измененная функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Проверка подлинности пользователей.
 
Ранее, если пользователи даже после явного выхода из приложения, защищенного Azure AD, пытались получить доступ к приложению Azure AD в своей корпоративной сети с собственных устройств, присоединенных к домену, они автоматически повторяли вход с помощью простого единого входа. Благодаря данному изменению поддерживается выход.  Это позволяет пользователям выбрать другую или ту же учетную запись Azure AD для входа вместо того, чтобы автоматически входить с помощью простого единого входа.

Дополнительные сведения см. в разделе [Устранение неполадок с простым единым входом Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso).
 
---
 
### <a name="application-proxy-connector-version-154020-released"></a>Выпущен соединитель прокси приложения версии 1.5.402.0

**Тип.** Измененная функция.  
**Категория службы.** Прокси-сервер приложения.  
**Возможности продукта.** Безопасность и защита удостоверений.
 
Эта версия соединителя постепенно разворачивалась в ноябре. Данная новая версия соединителя включает в себя следующие изменения.

- Теперь соединитель задает файлы cookie уровня домена, а не уровня поддомена. Это обеспечивает более удобный единый вход и позволяет избежать избыточных запросов аутентификации.
- Поддержка запросов на поблочное кодирование.
- Улучшенный мониторинг работоспособности соединителей. 
- Устранено несколько ошибок и повышена стабильность.

Дополнительные сведения см. в разделе [Сведения о соединителях прокси приложения Azure AD](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).
 
---

## <a name="february-2018"></a>Февраль 2018 г.
 
### <a name="improved-navigation-for-managing-users-and-groups"></a>Улучшенная навигация для управления пользователями и группами

**Тип.** Планирование изменений.  
**Категория службы.** Управление каталогами.  
**Возможности продукта.** Каталог.

Упрощена навигация для управления пользователями и группами. Теперь вы можете переходить из обзора каталога непосредственно к списку всех пользователей с удобным доступом к списку удаленных пользователей, а также к списку всех групп с удобным доступом к параметрам управления группами. Кроме того, на странице обзора каталога можно выполнить поиск пользователя, группы, корпоративного приложения или регистрацию приложения. 

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Доступность журналов входов в систему и отчетов об аудите в Microsoft Azure, обслуживаемой 21Vianet (21Vianet в Azure для Китая)

**Тип.** Новая функция.  
**Категория службы.** Azure Stack  
**Возможности продукта.** Мониторинг и отчетность.

Отчеты журнала действий Azure AD теперь доступны в Microsoft Azure, обслуживаемой экземплярами 21Vianet (21Vianet в Azure для Китая). Сюда относятся следующие журналы:

- **Журналы действий входов в систему.** Журналы всех входов в систему, связанные с вашим клиентом.

- **Журналы самостоятельного аудита паролей.** Все журналы аудита SSPR.

- **Журналы аудита управления каталогами.** Все журналы аудита, связанные с управлением каталогами, например управлением пользователями, управлением приложениями и другими видами управления.

Из этих журналов можно получить важные сведения о том, как работает ваша среда. Полученные данные позволят вам выполнять следующее:

- определять, как приложения и службы используются пользователями;

- устранить неполадки, влияющие на работу пользователей.

Дополнительные сведения об использовании этих отчетов см. в статье [Отчеты Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Просмотр отчетов об активности Azure AD с использованием роли "Читатель отчетов" (без прав администратора роли)

**Тип.** Новая функция.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.

Прислушиваясь к предложениям клиентов предоставить ролям без прав администратора доступ к журналам действий Azure AD, мы предоставили пользователям, которым назначена роль "Читатель отчетов", доступ к действиям, связанным с входом и аудитом, на портале Azure, а также с помощью наших API Graph. 

Дополнительные сведения об использовании этих отчетов см. в статье [Отчеты Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Утверждение EmployeeID, доступное в виде атрибута пользователя и идентификатора пользователя

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Единый вход.
 
Утверждение **EmployeeID** можно настроить как идентификатор пользователя и атрибут пользователя для пользователей-участников и гостей B2B в приложении с входом на основе SAML из пользовательского интерфейса корпоративного приложения.

Дополнительные сведения см. в статье [Настройка утверждений, выпущенных в токене SAML для корпоративных приложений в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Упрощенное управление приложениями с использованием подстановочных знаков в Azure AD Application Proxy

**Тип.** Новая функция.  
**Категория службы.** Прокси-сервер приложения.  
**Возможности продукта.** Проверка подлинности пользователей.
 
Чтобы облегчить процесс развертывания приложений и сократить административные расходы, мы теперь поддерживаем возможность публикации приложений с использованием подстановочных знаков. Чтобы опубликовать приложение с подстановочным знаком, можно выполнить стандартный поток публикации приложения, но во внутренних и внешних URL-адресах использовать подстановочный знак.

Дополнительные сведения см. в статье [Приложения с подстановочным знаком в прокси приложения Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard).

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Новые командлеты для поддержки конфигурации прокси-сервера приложения

**Тип.** Новая функция.  
**Категория службы.** Прокси-сервер приложения.  
**Возможности продукта.** Платформа.

В последнем выпуске модуля предварительной версии AzureAD PowerShell содержатся новые командлеты, позволяющие клиентам настраивать приложения прокси-сервера приложения с помощью PowerShell.

Ниже перечислены новые командлеты. 

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup

---
 
### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Новые командлеты для поддержки конфигурации групп

**Тип.** Новая функция.  
**Категория службы.** Прокси-сервер приложения.  
**Возможности продукта.** Платформа.

В последнем выпуске модуля AzureAD PowerShell содержатся командлеты для управления группами в Azure AD. Эти командлеты ранее были доступны в модуле AzureADPreview и теперь добавлены в модуль AzureAD

К командлетам группы, которые теперь входят в общедоступную версию, относятся: 

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup   
- Get-AzureADMSLifecyclePolicyGroup

---
 
### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Доступна новая версия Azure AD Connect

**Тип.** Новая функция.  
**Категория службы.** AD Sync.  
**Возможности продукта.** Платформа.
 
Azure AD Connect предпочтительней использовать для синхронизации данных между Azure AD и локальными источниками данных, включая Windows Server Active Directory и LDAP.

>[!Important]
>В этой сборке представлены изменения схем и правил синхронизации. После обновления служба синхронизации Azure AD Connect запустит действия полного импорта и полной синхронизации. Сведения о том, как изменить эту реакцию на событие, см. в разделе [Как отложить полную синхронизацию после обновления](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

Этот выпуск содержит следующие обновления и изменения.

**Исправленные проблемы**

- Устранены ошибки с временным окном выполнения фоновых задач для страницы фильтрации разделов при переходе на следующую страницу.

- Устранена ошибка, вызывавшая нарушение прав доступа во время выполнения настраиваемого действия ConfigDB.

- Устранена ошибка восстановления после превышения времени ожидания подключения к SQL.

- Устранена ошибка, из-за которой сертификаты с подстановочными знаками SAN не проходили предварительную проверку.

- Устранена ошибка, из-за которой выполнение файла miiserver.exe завершалось сбоем при экспорте соединителя AAD.

- Устранена ошибка, когда при вводе неправильного пароля выполнялся вход в контроллер домена при запуске мастера AAD Connect для изменения конфигурации.

**Новые функции и внесенные улучшения**
 
- Телеметрия приложений. Администратор может включать и отключать этот класс данных.

- Данные о работоспособности Azure AD. Для управления параметрами работоспособности администратору необходимо зайти на портал работоспособности. После изменения политики службы агенты прочитают и применят ее.

- Добавлены действия настройки обратной записи устройств и индикатор выполнения для инициализации страницы.

- Усовершенствована общая диагностика, предоставляемая в отчете HTML, и полный сбор данных, предоставляемых в ZIP-файле или отчете HTML.

- Усовершенствована надежность автоматического обновления и добавлена дополнительная телеметрия для определения работоспособности сервера.

- Ограничены доступные разрешения привилегированных учетных записей в учетной записи соединителя AD. Мастер ограничивает разрешения привилегированных учетных записей для новых установок после создания учетной записи MSOL. Изменения влияют на быстрые установки и выборочные установки с автоматически создаваемой учетной записью.

- Изменен установщик. Теперь ему не нужно разрешение сопоставления безопасности для чистой установки AAD Connect.

- Новая служебная программа для устранения неполадок с синхронизацией определенного объекта. В настоящее время служебная программа проверяет следующие вещи:

    - Несоответствие UserPrincipalName между синхронизированными объектом пользователя и учетной записью пользователя в клиенте Azure AD.
  
    - Фильтруется ли объект после синхронизации при фильтрации домена.
  
    - Фильтруется ли объект после синхронизации при фильтрации организационной единицы.

- Новая служебная программа для синхронизации текущего хэша паролей, хранящегося в локальном каталоге Active Directory для конкретной учетной записи пользователя. Служебная программа не требует изменения пароля. 

---
 
### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Приложения, поддерживающие политики Защита приложений Intune, добавленные для использования с условным доступом на основе приложений Azure AD

**Тип.** Измененная функция.  
**Категория службы.** Условный доступ.  
**Возможности продукта.** Безопасность и защита удостоверений.

Мы добавили дополнительные приложения, поддерживающие условный доступ на основе приложений. Теперь вы можете получить доступ к Office 365 и другим облачным приложениям, подключенным к Azure AD, с помощью этих клиентских приложений.

К концу февраля будут добавлены следующие приложения:

- Microsoft Power BI

- Microsoft Launcher;

- Microsoft Invoicing.

Дополнительные сведения см. здесь:

- [Требование утвержденного клиентского приложения](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Условный доступ на основе приложений Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Условия использования обновление в мобильном интерфейсе 

**Тип.** Измененная функция.  
**Категория службы.** Условия использования.  
**Возможности продукта.** Соответствие требованиям.

Когда будут отображаться условия использования, можно щелкнуть, чтобы устранить **неполадки при просмотре? Щелкните здесь**. При выборе этой ссылки условия использования откроются, адаптировавшись под ваше устройство. Независимо от размера шрифта в документе или размера экрана устройства, документ можно увеличить, чтобы прочитать нужный фрагмент. 

---
 
## <a name="january-2018"></a>Январь 2018 г.
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Новые федеративные приложения доступны в коллекции приложений Azure AD 

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.

В январе 2018 года в коллекцию приложений были добавлены следующие новые приложения с поддержкой федерации:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [программное обеспечение управления конфиденциальностью OneTrust](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), федеративный каталог IriusRisk и [Fidelity NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial).

Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="sign-in-with-additional-risk-detected"></a>Вход в систему с обнаруженным дополнительным риском

**Тип.** Новая функция.  
**Категория службы.** Защита идентификации.  
**Возможности продукта.** Безопасность и защита удостоверений.

Аналитика, полученная для обнаружения обнаруженных рисков, связана с подпиской Azure AD. Выпуск Azure AD Premium P2 позволяет получать самые подробные сведения обо всех основных обнаружениях.

В выпуске Azure AD Premium P1 обнаружения, не охваченные лицензией, отображаются в виде входа с обнаружением рисков с обнаруженным дополнительным риском.

Дополнительные сведения см. в статье об [обнаружении рисков Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 
---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Скрытие приложений Office 365 на панели доступа пользователя

**Тип.** Новая функция.  
**Категория службы.** "Мои приложения".  
**Возможности продукта.** Единый вход.

Теперь вы можете лучше управлять тем, как приложения Office 365 отображаются на панелях доступа пользователя, с помощью нового пользовательского параметра. Он позволяет уменьшить количество приложений, которые отображаются на панелях доступа пользователей, если вы предпочитаете, чтобы приложения Office отображались только на портале Office. Этот параметр находится в разделе **параметров пользователя** и называется **Пользователи могут видеть приложения Office 365 только на портале Office 365**.

Дополнительные сведения см. в статье [Скрытие приложения в пользовательском интерфейсе Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 
### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Простой вход в приложения, поддерживающие единый вход с помощью пароля, непосредственно из URL-адреса приложения 

**Тип.** Новая функция.  
**Категория службы.** "Мои приложения".  
**Возможности продукта.** Единый вход.

Расширение браузера "Мои приложения" теперь доступно с помощью удобного инструмента, который дает возможность выполнять единый вход в "Мои приложения" через ярлык в вашем браузере. После установки появится значок в виде вафли в браузере, который предоставляет быстрый доступ к приложениям. Теперь пользователи могут воспользоваться следующими преимуществами:

- Выполнять прямой вход в приложения с единым входом на основе пароля на странице входа в приложение.
- Запускать любое приложение с помощью функции быстрого поиска.
- Использовать ярлыки для недавно использовавшихся приложений из расширения.
- Расширение доступно для браузеров Microsoft Edge, Chrome и Firefox.
 
Дополнительные сведения см. в разделе [Расширение защищенного входа на страницу "Мои приложения"](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Интерфейс администрирования Azure AD на классическом портале Azure больше не поддерживается

**Тип.** Прекращение поддержки.   
**Категория службы.** Azure AD.  
**Возможности продукта.** Каталог.

Начиная с 8 января 2018 года интерфейс администрирования Azure AD на классическом портале Azure больше не поддерживается. Это произошло в связи с прекращением поддержки самого классического портала Azure. В будущем следует использовать [Центр администрирования Azure AD](https://aad.portal.azure.com) для задач администрирования Azure AD, выполняемых с помощью портала.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>Начиная с 8 января 2018 года прекращена поддержка веб-портала.

**Тип.** Прекращение поддержки.  
**Категория службы.** Azure AD.  
**Возможности продукта.** Каталог.
 
Начиная с 8 января 2018 года прекращена поддержка веб-портала PhoneFactor. Этот портал использовался для администрирования сервера MFA, но эти функции были перенесены на портал Azure по адресу portal.azure.com. 

Чтобы перейти к настройке MFA, выберите **Azure Active Directory \> Сервер MFA**.
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Неподдерживаемые отчеты Azure AD

**Тип.** Прекращение поддержки.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Управление жизненным циклом удостоверений.  


С выходом общедоступной версии новой консоли администрирования Azure Active Directory и новых API-интерфейсов, доступных для отчетов о действиях и безопасности, API-интерфейсы отчетов в конечной точке /reports были удалены 31 декабря 2017 года.

**Доступные возможности**

В рамках перехода на новую консоль администратора мы создали 2 новых программных интерфейса для извлечения журналов действий Azure AD. В новом наборе программных интерфейсов расширены функции фильтрации, сортировки, аудита и действий входа. Доступ к данным, ранее доступным через отчеты о безопасности, теперь можно получить с помощью API обнаружения рисков для защиты идентификации в Microsoft Graph.

Дополнительные сведения см. здесь:

- [Приступая к работе с API отчетов Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Начало работы с защитой идентификации Azure Active Directory и Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>Декабрь 2017 г.

### <a name="terms-of-use-in-the-access-panel"></a>Условия использования на панели доступа

**Тип.** Новая функция.  
**Категория службы.** Условия использования.  
**Возможности продукта.** Соответствие требованиям.
 
Теперь можно перейти к панели доступа и просмотреть условия использования, которые были приняты ранее.

Выполните следующие действия.

1. Перейдите на [портал MyApps](https://myapps.microsoft.com) и войдите в систему.

2. В правом верхнем углу щелкните свое имя, а затем в списке выберите **Профиль**. 

3. В **профиле** выберите **Ознакомьтесь с условиями использования**. 

4. Здесь можно просмотреть принятые вами условия использования. 

Дополнительные сведения см. в статье [Функция "Условия использования Azure Active Directory" (предварительная версия)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---
 
### <a name="new-azure-ad-sign-in-experience"></a>Новый вход в систему Azure AD

**Тип.** Новая функция.  
**Категория службы.** Azure AD.  
**Возможности продукта.** Аутентификация пользователей.
 
Пользовательские интерфейсы системы идентификации пользователей Azure AD и Майкрософт были перепроектированы таким образом, чтобы они имели единый внешний вид. Кроме того, страница входа в Azure AD сначала собирает имя пользователя, а затем учетные данные на втором экране.

Дополнительные сведения см. в записи блога [The new Azure AD Signin Experience is now in Public Preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/) (Новый интерфейс входа в Azure AD теперь находится в общедоступной предварительной версии).
 
---
 
### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Меньше запросов для входа. Новая функция "оставаться в системе" для входа в Azure AD

**Тип.** Новая функция.  
**Категория службы.** Azure AD.  
**Возможности продукта.** Аутентификация пользователей.
 
Флажок **Оставаться в системе** на странице входа в Azure AD был заменен новым запросом, который появляется после прохождения проверки подлинности. 

Если на такой запрос ответить **Да**, служба предоставит постоянный токен обновления. То же самое происходило при установке флажка **Оставаться в системе** в старой версии. Для федеративных клиентов этот запрос отображается после выполнения проверки подлинности в службе федерации.

Дополнительные сведения см. в записи блога [Fewer login prompts: The new “Keep me signed in” experience for Azure AD is in preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/) (Меньшее число запросов пароля. Новая функция "Оставаться в системе" в Azure AD). 

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Добавление конфигурации для требования развертывания условий использования перед их принятием

**Тип.** Новая функция.  
**Категория службы.** Условия использования.  
**Возможности продукта.** Соответствие требованиям.
 
Функция для администраторов, позволяющая требовать развертывания условий использования перед их принятием пользователем.

**Включите** или **отключите** параметр "Требовать, чтобы пользователи развернули условия использования". Если выбран вариант **Вкл.** , чтобы принять соглашение, пользователям сначала нужно просмотреть условия.

Дополнительные сведения см. в статье [Функция "Условия использования Azure Active Directory" (предварительная версия)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Активация только соответствующих назначений ролей

**Тип.** Новая функция.  
**Категория службы.** Управление привилегированными пользователями.  
**Возможности продукта.** Управление привилегированными пользователями.
 
Вы можете использовать ограниченную активацию для активации подходящих назначений ролей ресурсов Azure с меньшей автономией, чем исходные настройки по умолчанию. Предположим, вы назначены владельцем подписки в своем клиенте. С помощью ограниченной активации вы можете активировать роль владельца для максимум пяти ресурсов, содержащихся в рамках подписки (например, группы ресурсов и виртуальные машины). Ограничение активации может снизить вероятность выполнения нежелательных изменений в критических ресурсах Azure.

Дополнительные сведения см. в статье [Что такое Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)
 
---
 
### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Новые федеративные приложения в коллекции приложений Azure AD

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.

В декабре 2017 года мы добавили эти новые приложения с поддержкой федерации в нашу коллекцию приложений:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [EFI Digital StoreFront](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [IMAGE WORKS](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron Azure AD integration](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO for Bamboo by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO for Bitbucket by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zenegy Azure AD Integration.

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial).

Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 
 
---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Рабочий процесс утверждения ролей каталога Azure AD

**Тип.** Измененная функция.  
**Категория службы.** Управление привилегированными пользователями.  
**Возможности продукта.** Управление привилегированными пользователями.
 
Рабочий процесс утверждения ролей каталога Azure AD доступен в общедоступной версии.

С помощью рабочего процесса утверждения администраторы привилегированной роли могут требовать от кандидатов на участников роли запрашивать активацию привилегированной роли, прежде чем они смогут ее использовать. Многим пользователям и группам могут быть делегированы полномочия на утверждение. Кандидаты на участников роли получат уведомления, когда они будут утверждены и их роль будет активирована.

---
 
### <a name="pass-through-authentication-skype-for-business-support"></a>Сквозная проверка подлинности. Поддержка Skype для бизнеса.

**Тип.** Измененная функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Аутентификация пользователей.

Сквозная проверка подлинности теперь поддерживает вход пользователей в клиентские приложения Skype для бизнеса, которые поддерживают современную проверку подлинности, включая сетевые и гибридные топологии. 

Дополнительные сведения см. в статье [Топологии Skype для бизнеса, поддерживаемые современной проверкой подлинности](https://technet.microsoft.com/library/mt803262.aspx).
 
---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Обновления Azure AD Privileged Identity Management для Azure RBAC (предварительная версия)

**Тип.** Измененная функция.  
**Категория службы.** Управление привилегированными пользователями.  
**Возможности продукта.** Управление привилегированными пользователями.
 
Теперь, когда вышло обновление общедоступной предварительной версии Azure AD Privileged Identity Management (PIM) для управления доступом на основе ролей (RBAC) Azure, вы можете:

* Использовать Just Enough Administration (JEA).
* Требовать утверждения для активации ролей ресурсов.
* Планировать активацию ролей в будущем, которая требует одобрения как для ролей Azure AD, так и для ролей Azure RBAC.
 
Дополнительные сведения см. в статье [PIM для ресурсов Azure (предварительная версия)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---
 
## <a name="november-2017"></a>Ноябрь 2017 г.
 
### <a name="access-control-service-retirement"></a>Прекращение поддержки службы контроля доступа

**Тип.** Планирование изменений.  
**Категория службы.** Служба проверки доступа.  
**Возможность продукта.** Служба контроля доступа. 

Использование службы контроля доступа Azure Active Directory (также называется просто службой контроля доступа) будет прекращено в конце 2018 г. Дополнительные сведения, включая подробное расписание и общее руководство по переходу, будут предоставлены в ближайшие недели. Вы можете оставить на этой странице комментарии с любыми вопросами, касающимися службы контроля доступа, и член нашей команды ответит вам.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Предоставление доступа через браузер к управляемому браузеру Intune 

**Тип.** Планирование изменений.  
**Категория службы.** Условный доступ.  
**Возможности продукта.** Безопасность и защита удостоверений.

Вы можете предоставлять доступ через браузер к Office 365 и другим облачным приложениям, подключенным к Azure AD, используя Intune Managed Browser в качестве утвержденного приложения. 

Теперь можно настроить следующие условия для условного доступа на основе приложений.

**Клиентские приложения.** Браузер.

**Каков эффект изменения?**

В настоящее время при использовании этого условия доступ заблокирован. Если доступна предварительная версия, для всех операций доступа потребуется приложение управляемого браузера. 

Ищите сведения об этой возможности в предстоящих записях в блогах и заметках о выпуске. 

Дополнительные сведения см. [в статье условный доступ в Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Новые утвержденные клиентские приложения для условного доступа на основе приложений Azure AD

**Тип.** Планирование изменений.  
**Категория службы.** Условный доступ.  
**Возможности продукта.** Безопасность и защита удостоверений.

Следующие приложения добавлены в список [утвержденных клиентских приложений](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/);
- Microsoft StaffHub

Дополнительные сведения см. здесь:

- [Требование утвержденного клиентского приложения](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Условный доступ на основе приложений Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Поддержка условий использования для нескольких языков

**Тип.** Новая функция.    
**Категория службы.** Условия использования.  
**Возможности продукта.** Соответствие требованиям.

Теперь администраторы могут создавать новые условия использования, содержащие несколько документов в формате PDF. Вы можете отметить эти документы соответствующими языками. Пользователям будут показаны PDF-файлы на том языке, который соответствует их предпочтениям. Если документа на их языке нет, отображается язык по умолчанию.

---
 
### <a name="real-time-password-writeback-client-status"></a>Состояние обратной записи паролей клиента в реальном времени

**Тип.** Новая функция.  
**Категория службы.** Самостоятельный сброс пароля.  
**Возможности продукта.** Аутентификация пользователей.

Теперь вы можете просмотреть состояние обратной записи паролей клиента в локальной среде. Этот параметр доступен в разделе **Интеграция с локальной средой** на странице [Сброс пароля](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset). 

Если возникли проблемы с подключением к клиенту обратной записи в локальной среде, появится сообщение об ошибке, содержащее следующую информацию:

- сведения о том, почему не удается подключиться к клиенту обратной записи в локальной среде;
- ссылка на документацию, которая помогает устранить проблему. 

Дополнительные сведения см. в разделе [Интеграция с локальной средой](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Условный доступ на основе приложений Azure AD 
 
**Тип.** Новая функция.  
**Категория службы.** Azure AD.  
**Возможности продукта.** Безопасность и защита удостоверений.

Теперь вы можете ограничить доступ к Office 365 и другим облачным приложениям, подключенным к Azure AD, с [утвержденными клиентскими приложениями](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement) , поддерживающими политики защиты приложений Intune, с помощью [условного доступа на основе приложений Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Политики защиты приложений Intune используются для настройки и защиты данных компании в этих клиентских приложениях.

Объединив [приложения на основе приложений](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) с политиками условного доступа [на основе устройств](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) , вы получаете гибкие возможности для защиты данных персональных и корпоративных устройств.

Следующие условия и элементы управления теперь доступны для использования с условным доступом на основе приложений.

**Условие поддерживаемой платформы**

- iOS
- Android

**Условие клиентских приложений**

- Мобильные приложения и настольные клиенты

**Контроль доступа**

- Требование утвержденного клиентского приложения

Дополнительные сведения см. в статье [Условный доступ на основе приложений Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access).
 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Управление устройствами Azure AD на портале Azure

**Тип.** Новая функция.  
**Категория службы.** Регистрация устройств и управление ими.  
**Возможности продукта.** Безопасность и защита удостоверений.

Теперь все устройства, подключенные к Azure AD, и действия, связанные с устройствами, размещены в одном месте. На портале Azure доступен новый интерфейс администрирования для управления удостоверениями и параметрами устройств. В этом выпуске вы можете:

- Просмотрите все устройства, доступные для условного доступа в Azure AD.
- просматривать свойства, включая гибридные устройства, присоединенные к Azure AD;
- находить ключи BitLocker для устройств, присоединенных к Azure AD, чтобы управлять устройствами с помощью Intune и т. д.;
- управлять параметрами, связанными с устройствами Azure AD.

Дополнительные сведения см. в статье [Управление устройствами с помощью портала Azure](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Поддержка macOS в качестве платформы устройств для условного доступа Azure AD 

**Тип.** Новая функция.    
**Категория службы.** Условный доступ.  
**Возможности продукта.** Безопасность и защита удостоверений. 

Теперь вы можете включить (или исключить) macOS в качестве условия платформы устройства в политике условного доступа Azure AD. Добавив macOS в качестве поддерживаемой платформы устройства, вы можете:

- **Регистрировать устройства macOS и управлять ими в Intune.** Подобно другим платформам, таким как iOS и Android, приложение корпоративного портала доступно для единой регистрации устройств macOS. Вы можете использовать новое приложение корпоративного портала для macOS, чтобы зарегистрировать устройство в Intune и Azure AD.
- **Гарантировать соблюдение устройствами macOS политик соответствия вашей организации, определенных в Intune.** В Intune на портале Azure вы можете настроить соответствующие политики для устройств macOS. 
- **Предоставлять доступ к приложениям Azure AD только совместимым устройствам macOS.** Создание политики условного доступа имеет macOS как отдельный параметр платформы устройства. Теперь вы можете создавать политики условного доступа, относящиеся к macOS, для набора целевых приложений в Azure.

Дополнительные сведения см. здесь:

- [Создание политики соответствия устройств для устройств macOS (предварительная версия) при помощи Intune](https://aka.ms/macoscompliancepolicy)
- [Условный доступ в Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Расширение NPS для Многофакторной идентификации Azure 

**Тип.** Новая функция.    
**Категория службы.** Многофакторная идентификация.  
**Возможности продукта.** Аутентификация пользователей.

Расширение сервера политики сети для Многофакторной идентификации Azure добавляет в инфраструктуру проверки подлинности возможности Многофакторной идентификации на основе облака с использованием имеющихся серверов. Расширение NPS позволяет добавить телефонный звонок, текстовое сообщение или запрос на подтверждение в мобильном приложении в существующий процесс аутентификации. Можно обойтись без установки, настройки и поддержания новых серверов. 

Это расширение было создано для организаций, которым необходимо защитить VPN-подключения, не развертывая сервер Многофакторной идентификации Azure. Расширение NPS выполняет функции адаптера между RADIUS и облачной службой Azure MFA, предоставляя второй фактор проверки подлинности для федеративных или синхронизированных пользователей.

Дополнительные сведения см. в статье [Интеграция существующей инфраструктуры NPS с Многофакторной идентификацией Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).
 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Восстановление или окончательное удаление удаленных пользователей

**Тип.** Новая функция.    
**Категория службы.** Управление пользователями.  
**Возможности продукта.** Каталог. 

Теперь в центре администрирования Azure AD вы можете:

- Восстановить удаленного пользователя. 
- Окончательно удалить пользователя.

**Для этого:**

1. В центре администрирования Azure AD в разделе [Управление](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) выберите **Все пользователи**. 

2. В списке **Показать** выберите **Недавно удаленные пользователи**. 

3. Выберите одного или нескольких недавно удаленных пользователей, а затем восстановите их или удалите без возможности восстановления.
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Новые утвержденные клиентские приложения для условного доступа на основе приложений Azure AD
 
**Тип.** Измененная функция.  
**Категория службы.** Условный доступ.  
**Возможности продукта.** Безопасность и защита удостоверений.

Следующие приложения добавлены в список [утвержденных клиентских приложений](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- Планировщик (Майкрософт);
- Azure Information Protection 

Дополнительные сведения см. здесь:

- [Требование утвержденного клиентского приложения](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Условный доступ на основе приложений Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Использование "или" между элементами управления в политике условного доступа 

**Тип.** Измененная функция.    
**Категория службы.** Условный доступ.  
**Возможности продукта.** Безопасность и защита удостоверений.
 
Теперь можно использовать "или" (требуется один из выбранных элементов управления) для элементов управления условного доступа. Эта функция позволяет создавать политики со значением "или" между элементами управления доступом. Например, эта функция позволяет создать политику, требующую от пользователя входа в систему с помощью Многофакторной идентификации или использования соответствующего устройства.

Дополнительные сведения см. [в разделе элементы управления условным доступом Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).
 
---

### <a name="aggregation-of-real-time-risk-detections"></a>Агрегирование обнаруженных рисков в реальном времени

**Тип.** Измененная функция.    
**Категория службы.** Защита идентификации.  
**Возможности продукта.** Безопасность и защита удостоверений.

В защита идентификации Azure AD все обнаружения рисков в режиме реального времени, полученные с того же IP-адреса в заданный день, теперь объединяются для каждого типа обнаружения риска. Это изменение ограничивает объем обнаруженных рисков без каких бы то ни было изменений в системе безопасности пользователей.

Функция базового обнаружения в реальном времени работает каждый раз, когда пользователь выполняет вход. Если вы настроили для политики безопасности входа Многофакторную идентификацию или блокирование доступа, она все равно будет срабатывать при каждом подозрительном входе.
 
---
 
## <a name="october-2017"></a>Октябрь 2017 г.

### <a name="deprecate-azure-ad-reports"></a>Неподдерживаемые отчеты Azure AD

**Тип.** Планирование изменений.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Управление жизненным циклом удостоверений.  

Портал Azure предлагает:

- новую консоль администрирования Azure AD;
- новые программные интерфейсы (API) для отчетов по действиям и безопасности.
 
Благодаря этим новым возможностям API отчетов на конечной точке /reports были выведены из эксплуатации 10 декабря 2017 г. 

---

### <a name="automatic-sign-in-field-detection"></a>Автоматическое определение полей входа в систему

**Тип.** Исправление.   
**Категория службы.** "Мои приложения".  
**Возможности продукта.** Единый вход.  

Azure AD поддерживает автоматическое определение поля входа для приложений, преобразовывающих для просмотра поле имени пользователя и пароля HTML. Эти шаги описаны в разделе [Определение полей входа в систему для приложения вручную](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications-problems#manually-capture-sign-in-fields-for-an-app). Эту возможность можно найти путем добавления приложения *не из коллекции* на страницу **корпоративных приложений** на [портале Azure](https://aad.portal.azure.com). Кроме того, можно настроить режим **единого входа** в это новое приложение с помощью **единого входа по паролю** путем введения URL-адреса и сохранения страницы.
 
Из-за сбоя в службе эта функциональность была временно отключена. Сейчас эта проблема устранена и автоматическое обнаружение поля входа снова доступно.

---

### <a name="new-multi-factor-authentication-features"></a>Новые возможности Многофакторной идентификации

**Тип.** Новая функция.  
**Категория службы.** Многофакторная проверка подлинности.  
**Возможности продукта.** Безопасность и защита удостоверений.  

Многофакторная проверка подлинности (MFA) является важной частью процесса защиты организации. Чтобы сделать учетные данные более адаптивными, а работу — простой, были добавлены следующие возможности: 

- Влияние интеграции многофакторной аутентификации на отчет о входе в Azure AD, включая программный доступ к результатам многофакторной аутентификации.
- Более тесная интеграция конфигурации многофакторной проверки подлинности с конфигурацией Azure AD на портале Azure.

В общедоступной предварительной версии управление и составление отчетов многофакторной проверки подлинности являются неотъемлемой частью настройки основных компонентов Azure AD. Теперь вы можете управлять функциональными возможностями портала управления многофакторной проверки подлинности в Azure AD.

Дополнительные сведения см. в статье [Справочник по созданию отчетов многофакторной проверки подлинности на портале Azure](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 

---

### <a name="terms-of-use"></a>Условия использования

**Тип.** Новая функция.  
**Категория службы.** Условия использования.  
**Возможности продукта.** Соответствие требованиям.  

Вы можете использовать условия использования Azure AD для предоставления пользователям такой информации, как соответствующие заявления об отказе для юридических требований и требования к соответствию.

Условия использования Azure AD можно применять в следующих сценариях:

- общие условия использования для всех пользователей в вашей организации;
- определенные условия использования на основе атрибутов пользователя (например, для медсестер и врачей или местных и международных сотрудников, что выполняется с помощью динамических групп);
- определенные условия использования для доступа к важным для бизнеса приложениям, например Salesforce.

Дополнительные сведения см. в статье [Функция "Условия использования Azure Active Directory" (предварительная версия)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="enhancements-to-privileged-identity-management"></a>Улучшение управления привилегированными пользователями

**Тип.** Новая функция.  
**Категория службы.** Управление привилегированными пользователями.  
**Возможности продукта.** Управление привилегированными пользователями.  

С помощью службы Azure AD Privileged Identity Management (PIM) можно администрировать, контролировать и отслеживать доступ к ресурсам Azure (предварительная версия) в пределах организации:

- Подписки
- Группы ресурсов
- Виртуальные машины 

Все ресурсы на портале Azure, который использует функции Azure RBAC, могут воспользоваться всеми преимуществами управления безопасностью и жизненным циклом, которые может предложить Azure AD Privileged Identity Management.

Дополнительные сведения см. в статье [PIM для ресурсов Azure (предварительная версия)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

### <a name="access-reviews"></a>Проверки доступа

**Тип.** Новая функция.  
**Категория службы.** Проверки доступа.  
**Возможности продукта.** Соответствие требованиям.  

Проверки доступа (предварительная версия) позволяют организациям эффективно управлять членством в группе и доступом к корпоративным приложениям. 

- Проверки доступа позволяют повторно оценивать, действительно ли гостевым пользователям нужен доступ к приложениям и членство в группах. Сведения, предоставляемые проверками доступа, позволяют рецензентам решать, нужен ли гостевым пользователям постоянный доступ.
- С их помощью можно повторно подтвердить доступ сотрудников к приложениям и их членство в группах.

Можно собирать элементы управления проверки доступа в программы, соответствующие вашей организации, чтобы отслеживать доступ для приложений, чувствительных к риску или нарушениям соответствия.

Дополнительные сведения см. в статье [Проверки доступа Azure AD (предварительная версия)](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Скрытие сторонних приложений в разделе "Мои приложения" и от средства запуска приложения Office 365

**Тип.** Новая функция.  
**Категория службы.** "Мои приложения".  
**Возможности продукта.** Единый вход.  

Теперь можно эффективнее управлять приложениями, которые отображаются на пользовательском портале, с помощью нового свойства **Скрыть приложение**. Это помогает в случаях, когда плитки приложений отображаются для серверных служб или плиток-дубликатов и перегружают средства запуска пользовательских приложений. Переключатель находится в разделе **Свойства** в стороннем приложении. Он называется **Visible to user?** (Видимый для пользователя?). Скрыть приложение можно также программным способом с помощью PowerShell. 

Дополнительные сведения см. в статье [Скрытие приложения в пользовательском интерфейсе Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**Доступные возможности**

 В рамках перехода на новую консоль администратора доступны два новых программных интерфейса для извлечения доступных журналов действий Azure AD. В новом наборе программных интерфейсов расширены функции фильтрации, сортировки, аудита и действий входа. Доступ к данным, ранее доступным через отчеты о безопасности, теперь можно получить с помощью API обнаружения рисков для защиты идентификации в Microsoft Graph.


## <a name="september-2017"></a>Сентябрь 2017 г.

### <a name="hotfix-for-identity-manager"></a>Исправления для Identity Manager

**Тип.** Измененная функция.  
**Категория службы.** Identity Manager.  
**Возможности продукта.** Управление жизненным циклом удостоверений.  

Накопительный пакет исправлений (сборка 4.4.1642.0) для Identity Manager 2016 с пакетом обновления 1 стал доступен с 25 сентября 2017 года. Этот накопительный пакет исправлений:

- устраняет проблемы и добавляет улучшения;
- является накопительным обновлением, которое заменяет все обновления сборки Identity Manager 2016 с пакетом обновления 1 от Identity Manager 2016 версии 4.4.1459.0; 
- требует наличия сборки Identity Manager 2016 версии 4.4.1302.0. 

Дополнительные сведения см. в статье [Доступен накопительный пакет исправлений (сборка 4.4.1642.0) для пакета обновления 1 для Microsoft Identity Manager 2016](https://support.microsoft.com/help/4021562). 

---
