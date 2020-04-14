---
title: Архив для Что нового в Active Directory Azure? | Документы Майкрософт
description: Заметки о выпуске "Новые возможности" в разделе "Обзор" для конкретного набора содержимого отображаются на протяжении шести месяцев. Через шесть месяцев элементы удаляются из основной статьи и помещаются в статью архива.
services: active-directory
author: msmimart
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25191951472e30492606ad97c440a13359c8ef24
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253175"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Архив для Что нового в Active Directory Azure?

Основное что [нового в публикации Примечаний к выпуску Azure Active Directory?](whats-new.md) содержит обновления за последние шесть месяцев, в то время как эта статья содержит всю старую информацию.

Что нового в active-каталоге Azure? примечания к выпуску предоставляют информацию о:

- Последние выпуски.
- Известные проблемы
- Исправления ошибок
- Нерекомендуемые функции.
- Планы по изменениям.

---

## <a name="september-2019"></a>Сентябрь 2019 г.

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>План изменений: Уничтожение пакетов контента Power BI

**Тип.** Планирование изменений.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.

С 1 октября 2019 года Power BI начнет обесценивать все пакеты контента, включая пакет контента Azure AD Power BI. В качестве альтернативы этому пакету содержимого можно использовать рабочие книги Azure AD, чтобы получить представление о службах, связанных с Azure AD. Приближаются дополнительные трудовые книжки, включая трудовые книжки о политиках условного доступа в режиме только отчета, идеи, основанные на согласии приложений, и многое другое.

Для получения дополнительной информации [How to use Azure Monitor workbooks for Azure Active Directory reports](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)о трудовых книжках см. Для получения дополнительной информации об амортизацию пакетов содержимого, [см.](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/)

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>Мой профиль переименовывается и интегрируется со страницей учетной записи Microsoft Office

**Тип.** Планирование изменений.  
**Категория обслуживания:** Мой профиль/аккаунт  
**Возможности продукта.** Совместная работа.

Начиная с октября, мой профиль опыт станет мой счет. В рамках этого изменения, везде, что в настоящее время говорит, **мой профиль** будет меняться на **мой счет**. Помимо изменения именования и некоторых улучшений дизайна, обновленный опыт будет предлагать дополнительную интеграцию со страницей учетной записи Microsoft Office. В частности, вы сможете получить доступ к установкам office и подпискам со страницы **учетной записи Обзор,** а также к контактным предпочтениям Office со страницы **Конфиденциальность.**

Для получения дополнительной информации о моем профиле (предварительный просмотр) опыт, см [Мой профиль (предварительный просмотр) обзор портала](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview).

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Массовое управление группами и участниками с помощью файлов CSV на портале Azure AD (публичный предварительный просмотр)

**Тип.** Новая функция.  
**Категория службы.** Управление группами.  
**Возможности продукта.** Совместная работа.

Мы рады сообщить о наличии публичного предварительного просмотра опыта управления большим объемом групп на портале Azure AD. Теперь для управления группами и списками участников можно использовать файл CSV и портал Azure AD, в том числе:

- Добавление или удаление членов из группы.

- Загрузка списка групп из каталога.

- Загрузка списка членов группы для определенной группы.

Для получения дополнительной информации, [см. Массовые добавить членов](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members), [Массовые удалить членов](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members), [Массовый список членов загрузки](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members), и [Массовые группы загрузки списка](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>Динамическое согласие теперь поддерживается через новую конечную точку согласия админа

**Тип.** Новая функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Проверка подлинности пользователей

Мы создали новую конечную точку согласия админа для поддержки динамического согласия, что полезно для приложений, которые хотят использовать динамическую модель согласия на платформе Microsoft Identity.

Для получения дополнительной информации о том, как использовать эту новую конечную точку, [см.](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Новые федеративные приложения доступны в галерее Azure AD App - сентябрь 2019

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.

В сентябре 2019 года мы добавили эти 29 новых приложений с поддержкой Федерации в галерею приложений:

[ScheduleLook](https://schedulelook.bbsonlineservices.net/), [MS Azure SSO Access&trade; for Ethidex Compliance Office - Одиночный вход,](https://docs.microsoft.com/azure/active-directory/saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial) [портал iServer](https://docs.microsoft.com/azure/active-directory/saas-apps/iserver-portal-tutorial), [SKYSITE](https://docs.microsoft.com/azure/active-directory/saas-apps/skysite-tutorial), [Concur Travel and Expense](https://docs.microsoft.com/azure/active-directory/saas-apps/concur-travel-and-expense-tutorial), [WorkBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/workboard-tutorial), `https://apps.yeeflow.com/`, ARC [Услуги](https://docs.microsoft.com/azure/active-directory/saas-apps/arc-facilities-tutorial), [Luware Stratus Team](https://stratus.emea.luware.cloud/login), Wide [Ideas](https://wideideas.online/wideideas/), [Prisma Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial), [JDLT Клиент концентратор](https://clients.jdlt.co.uk/login), [РЕНРАК U](https://docs.microsoft.com/azure/active-directory/saas-apps/renraku-tutorial), [SealPath Безопасный браузер](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive), [Prisma Облако](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial), `https://app.penneo.com/`, `https://app.testhtm.com/settings/email-integration`, [Cintoo Cloud](https://aec.cintoo.com/login), [Whitesource](https://docs.microsoft.com/azure/active-directory/saas-apps/whitesource-tutorial), [Хостинг Наследия Интернет SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-heritage-online-sso-tutorial), [IDC](https://docs.microsoft.com/azure/active-directory/saas-apps/idc-tutorial), [CakeHR](https://docs.microsoft.com/azure/active-directory/saas-apps/cakehr-tutorial), [BIS](https://docs.microsoft.com/azure/active-directory/saas-apps/bis-tutorial), Ку [Кай сборки команды](https://ms-contacts.coo-kai.jp/), [Sonarqube](https://docs.microsoft.com/azure/active-directory/saas-apps/sonarqube-tutorial), [Adobe Identity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/adobe-identity-management-tutorial), Открытие Преимущества [SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/discovery-benefits-sso-tutorial), [Амелио](https://app.amelio.co/),`https://itask.yipinapp.com/`

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-global-reader-role"></a>Новая роль глобального читателя Azure AD

**Тип.** Новая функция.  
**Категория обслуживания:** Rbac  
**Возможность продукта.** Контроль доступа.

Начиная с 24 сентября 2019 года, мы собираемся начать развертывание новой роли Active Directory (AD) Azure Под названием Global Reader. Это развертывание начнется с производства и глобальных облачных клиентов (GCC), заканчивая по всему миру в октябре.

Роль Глобального читателя является аналогом только для администратора, который читает сяпоните. Пользователи в этой роли могут читать настройки и административную информацию в службах Microsoft 365, но не могут принимать меры управления. Мы создали роль Глобального читателя, чтобы помочь уменьшить число глобальных администраторов в вашей организации. Поскольку учетные записи Global Administrator являются мощными и уязвимыми для атак, мы рекомендуем, чтобы у вас было менее пяти глобальных администраторов. Мы рекомендуем использовать роль Global Reader для планирования, аудита или расследований. Мы также рекомендуем использовать роль Global Reader в сочетании с другими ограниченными функциями администратора, такими как Exchange Administrator, чтобы помочь выполнить работу, не требуя роли Глобального Администратора.

Роль Глобального читателя работает с новым админ-центром Microsoft 365, Центром управления обменом, Центром менеджеров по админированности, Центром безопасности, Центром комплаенса, Админ-центром Azure AD и Центром управления устройствами.

>[!NOTE]
> В начале публичного предварительного просмотра роль Global Reader не будет работать с: SharePoint, Привилегированный контроль доступа, Клиентская коробка, этикетки чувствительности, жизненный цикл команд, Отчетность команд & Call Analytics, Команды IP Phone Device Management и Каталог приложений команд. 

Для получения дополнительной информации смотрите [разрешения на роль администратора в Active Directory Azure.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Доступ к серверу отчетов из мобильного приложения Power BI Mobile с помощью прокси-приложения Azure Active Directory

**Тип.** Новая функция.  
**Категория службы.** Прокси-сервер приложения.  
**Возможность продукта.** Контроль доступа.

Новая интеграция между мобильным приложением Power BI и прокси-сервером Azure AD Позволяет безопасно войти в мобильное приложение Power BI и просмотреть любой из отчетов организации, размещенных на сервере внутреннего отчета Power BI Report Server.

Для получения информации о Power BI Мобильное приложение, в том числе где скачать приложение, [см.](https://powerbi.microsoft.com/mobile/) Для получения дополнительной информации о том, как настроить мобильное приложение Power BI с помощью приложения Azure AD Proxy, [см.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi)

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>Доступна новая версия модуля AzureADPreview PowerShell

**Тип.** Измененная функция.  
**Категория службы.** Другая.  
**Возможности продукта:** Каталог

Новые cmdlets были добавлены в модуль AzureADPreview, чтобы помочь определить и назначить пользовательские роли в Azure AD, в том числе:

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
**Возможности продукта:** Каталог

Мы выпустили обновленную версию Azure AD Connect для клиентов с автоматическим обновлением. Эта новая версия включает в себя несколько новых функций, улучшений и исправлений ошибок. Для получения дополнительной информации об этой новой версии, см [Azure AD Connect: История релиза версии](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250).

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Azure Multi-фактор аутентификации (MFA) Сервер, версия 8.0.2 теперь доступна

**Тип.** Исправление.  
**Категория обслуживания:** Мид  
**Возможности продукта.** Безопасность и защита удостоверений.

Если вы являетесь существующим клиентом, который активировал MFA Server до 1 июля 2019 года, теперь вы можете скачать последнюю версию MFA Server (версия 8.0.2). В этой новой версии мы:

- Исправлена проблема, когда синхронизация Azure AD изменяет пользователя с отключенного на Enabled, пользователю отправляется электронная почта.

- Исправлена проблема, чтобы клиенты могли успешно обновиться, продолжая при этом использовать функциональность Тегов.

- Добавлен код страны Косово (No383).

- Добавлен одноразовый обход аудита регистрации в MultiFactorAuthSvc.log.

- Улучшенная производительность для Веб-службы SDK.

- Исправлены другие незначительные ошибки.

С 1 июля 2019 года microsoft перестала предлагать MFA Server для новых развертываний. Новые клиенты, которым требуется многофакторная аутентификация, должны использовать многофакторную аутентификацию Azure. Для получения дополнительной информации см. [Планирование облачного развертывания Azure Multi-Factor Authentication.](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

---

## <a name="august-2019"></a>Август 2019 г.

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>Улучшенный поиск, фильтрация и сортировка групп доступны на портале Azure AD (публичный предварительный просмотр)

**Тип.** Новая функция.  
**Категория службы.** Управление группами.  
**Возможности продукта.** Совместная работа.

Мы рады сообщить о доступности расширенного доступа к расширенным группам на портале Azure AD. Эти усовершенствования помогут вам лучше управлять группами и списками участников, предоставляя:

- Расширенные возможности поиска, такие как поиск подстроки в списках групп.
- Расширенные варианты фильтрации и сортировки в списках членов и владельцев.
- Новые возможности поиска для списков членов и владельцев.
- Более точные групповые подсчеты для больших групп.

Для получения дополнительной информации на портале «Управление» можно на [портале Azure.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Новые пользовательские роли доступны для управления регистрацией приложений (Public Preview)

**Тип.** Новая функция.  
**Категория обслуживания:** Rbac  
**Возможность продукта.** Контроль доступа.

Пользовательские роли (доступные с подпиской Azure AD P1 или P2) теперь могут помочь обеспечить вам мелкозернистого доступа, позволяя создавать определения ролей с конкретными разрешениями, а затем присваивать эти роли определенным ресурсам. В настоящее время вы создаете пользовательские роли, используя разрешения для управления регистрацией приложений, а затем присваивая роль конкретному приложению. Для получения дополнительной информации о пользовательских ролях смотрите [роли пользовательского администратора в active Directory (предварительный просмотр) Azure Active .](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview)

Если вам нужны дополнительные разрешения или поддержку ресурсов, которые вы в настоящее время не видите, вы можете отправить обратную связь на наш [сайт обратной связи Azure,](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) и мы добавим ваш запрос в нашу дорожную карту обновления.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Новые журналы подготовки помогут вам контролировать и устранить устранение развертывания приложения (Public Preview)

**Тип.** Новая функция.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта:** Управление жизненным циклом идентичности

Новые журналы подготовки доступны, чтобы помочь вам контролировать и устранить устранение проблем пользователя и группы подготовки развертывания. Эти новые файлы журнала содержат информацию о:

- Какие группы были успешно созданы в [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)
- Какие роли были импортированы из [Amazon Web Services (AWS)](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws)
- Какие сотрудники не были импортированы из [рабочего дня](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)

Для получения дополнительной информации смотрите [отчеты о представлении о представлении на портале Active Directory Azure (предварительный просмотр)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs).

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Новые отчеты безопасности для всех администраторов Azure AD (общая доступность)

**Тип.** Новая функция.  
**Категория обслуживания:** Защита личных данных  
**Возможности продукта.** Безопасность и защита удостоверений.

По умолчанию все администраторы Azure AD скоро смогут получить доступ к современным отчетам безопасности в Azure AD. До конца сентября вы сможете использовать баннер в верхней части современных отчетов о безопасности, чтобы вернуться к старым отчетам.

Современные отчеты о безопасности обеспечат дополнительные возможности из старых версий, в том числе:

- Расширенная фильтрация и сортировка
- Массовые действия, такие как увольнение пользователей риска
- Подтверждение скомпрометированных или безопасных объектов
- Состояние риска, покрытие: В опасности, Уволенный, Исправленный и Подтвержденный скомпрометирован
- Новые обнаружения рисков (доступны для подписчиков Azure AD Premium)

Для получения дополнительной информации [см. Риски пользователей](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users), [Рискованные вхыски](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins), и [обнаружения рисков](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections).

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>Управляемая идентификаторы, назначенные пользователем, доступна для виртуальных машин и наборов виртуальной шкалы машин (общая доступность)

**Тип.** Новая функция.  
**Категория обслуживания:** Управляемые идентификаторы для ресурсов Azure  
**Возможности продукта.** Возможности для разработчиков

Назначенные пользователем управляемые идентификаторы теперь, как правило, доступны для виртуальных машин и виртуальных наборов шкалы машин. В рамках этого Azure может создать идентификатор в клиенте Azure AD, которому доверяет используемая подписка, и может быть приписан к одному или несколько экземплярам службы Azure. Для получения дополнительной информации о управляемых идентификаторах, назначенных пользователем, [см. Что такое управляемые идентификаторы для ресурсов Azure?.](https://aka.ms/azuremanagedidentity)

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Пользователи могут сбросить свои пароли с помощью мобильного приложения или аппаратного токена (Общая доступность)

**Тип.** Измененная функция.  
**Категория службы.** Самостоятельный сброс пароля.  
**Возможности продукта:** Проверка подлинности пользователей

Пользователи, зарегистрировавшие мобильное приложение с вашей организацией, теперь могут сбросить свой пароль, одобрив уведомление от приложения Microsoft Authenticator или введя код из своего мобильного приложения или аппаратного токена.

Для получения дополнительной информации см. [Как это работает: сбросить пароля самообслуживания Azure AD.](https://aka.ms/authappsspr) Для получения дополнительной информации об опыте работы с пользователем [см.](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview)

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET игнорирует MSAL.NET общий кэш для сценариев

**Тип.** Исправление.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Проверка подлинности пользователей

Начиная с библиотеки аутентификации Azure AD (ADAL.NET) версии 5.0.0-preview, разработчики приложений должны [сериализировать один кэш на учетную запись для веб-приложений и веб-AI.](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api) В противном случае, некоторые сценарии, использующие поток от `UserAssertion` [имени,](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow)наряду с некоторыми конкретными случаями использования, могут привести к повышению привилегий. Чтобы избежать этой уязвимости, ADAL.NET теперь игнорирует библиотеку проверки подлинности Майкрософт для общего кэша dotnet (MSAL.NET) для сценариев.

Для получения дополнительной информации [Azure Active Directory Authentication Library Elevation of Privilege Vulnerability](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258)об этой проблеме см.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Новые федеративные приложения доступны в галерее Azure AD App - август 2019

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.

В августе 2019 года мы добавили эти 26 новых приложений с поддержкой Федерации в галерею приложений:

[Гражданская платформа](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial), [Amazon Бизнес](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial), [ProNovos Ops Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial), [Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial), [Inativ портал Viareport (Европа)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Робин](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial), [Академия Посещаемость](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial), [Приоритет Ная милис](https://sync.appfluence.com/pmwebng/), [Кусто MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [Карбонит Эндпойнт Резервное копирование](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial), [CP'Sync от Cincom](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial), [Chargebee](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial), [&trade; deliver.media портал](https://portal.deliver.media), Frontline [образование](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat AD подключиться](https://www.stashcat.com), [Blink](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial), [Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial), [ProNovos Analytics](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial), [Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial), [Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial), [Смотреть по цветам](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial), [Harness](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial), [EAB Навигация Стратегическое обслуживание](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>Доступны новые версии модулей AzureAD PowerShell и AzureADPreview PowerShell

**Тип.** Измененная функция.  
**Категория службы.** Другая.  
**Возможности продукта:** Каталог

Доступны новые обновления модулей AzureAD и AzureAD Preview PowerShell:

- Новый `-Filter` параметр был `Get-AzureADDirectoryRole` добавлен к параметру в модуле AzureAD. Этот параметр помогает фильтровать роли каталога, возвращенные cmdlet.
- Новые cmdlets были добавлены в модуль AzureADPreview, чтобы помочь определить и назначить пользовательские роли в Azure AD, в том числе:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Улучшение uI динамического правила группы на портале Azure

**Тип.** Измененная функция.  
**Категория службы.** Управление группами.  
**Возможности продукта.** Совместная работа.

Мы внесли некоторые улучшения в создание динамического правила группы, доступного на портале Azure, чтобы облегчить настройку нового правила или изменить существующие правила. Это улучшение конструкции позволяет создавать правила с до пяти выражений, а не только один. Мы также обновили список свойств устройства, чтобы удалить универшенные свойства устройства.

Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Новое разрешение приложения Microsoft Graph, доступное для использования с отзывами о доступе

**Тип.** Измененная функция.  
**Категория обслуживания:** Обзоры доступа  
**Возможности продукта:** Управление идентификацией

Мы представили новое разрешение приложения Microsoft `AccessReview.ReadWrite.Membership`Graph, которое позволяет приложениям автоматически создавать и получать отзывы о доступе для групповых принадлежностей и назначений приложений. Это разрешение может быть использовано запланированными заданиями или как часть автоматизации, не требуя входа в пользовательский контекст.

Для получения дополнительной информации смотрите пример ы [создания обзоров доступа Azure AD с помощью разрешений приложений Microsoft Graph с помощью блога PowerShell.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241)

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>В журналах активности Azure AD теперь доступны для экземпляров облаков правительства в Azure Monitor

**Тип.** Измененная функция.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.

Мы рады сообщить, что журналы активности Azure AD теперь доступны для экземпляров облаков правительственных систем в Azure Monitor. Теперь вы можете отправлять журналы Azure AD в свой аккаунт хранения или в концентратор событий для интеграции с инструментами SIEM, такими как [Sumologic,](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic) [Splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)и [ArcSight.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight) 

Для получения дополнительной информации о настройке Azure Monitor см. [журналы активности Azure AD в Azure Monitor.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations)

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Обновление пользователей до нового, расширенного опыта информации о безопасности

**Тип.** Измененная функция.  
**Категория обслуживания:**  Аутентификация (Логины)   
**Возможности продукта:** Проверка подлинности пользователей

25 сентября 2019 года мы отключим старый, не усиленный опыт информации о безопасности для регистрации и управления информацией о безопасности пользователей и только включим новую [улучшенную версию.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271) Это означает, что ваши пользователи больше не смогут использовать старый опыт.

Для получения дополнительной информации о расширенном опыте получения информации о безопасности посетите нашу [документацию для админ-адиктора](https://aka.ms/securityinfodocs) и нашу [пользовательскую документацию.](https://aka.ms/securityinfoguide)

#### <a name="to-turn-on-this-new-experience-you-must"></a>Чтобы включить этот новый опыт, вы должны:

1. Вопиюсь на портал Azure в качестве глобального администратора или администратора пользователей.

2. Перейдите в **Active Directory Azure > настройки пользователя > настройки управления функциями предварительного просмотра панели доступа.**

3. В **пользователи могут использовать функции предварительного просмотра для регистрации и управления информацией о безопасности - расширенная** область, выберите **Выбранный,** а затем либо выбрать группу пользователей или выбрать **Все,** чтобы включить эту функцию для всех пользователей в арендатора.

4. В «Пользователи могут использовать функции предварительного просмотра для регистрации и управления безопасностью (info) области, выберите **Нет**.

5. Сохраните параметры.

    После сохранения настроек у вас больше не будет доступа к старому интерфейсу информации о безопасности.

>[!Important]
>Если вы не выполните эти действия до 25 сентября 2019 года, ваш арендатор Azure Active Directory будет автоматически включен для расширенного просмотра. Если у вас есть вопросы, пожалуйста, свяжитесь с нами по адресу registrationpreview@microsoft.com.

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Запросы аутентификации с использованием логинов POST будут более строго проверены

**Тип.** Измененная функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Стандарты

Начиная со 2 сентября 2019 года, запросы на аутентификацию с использованием метода POST будут более строго проверены в отношении стандартов HTTP. В частности, пробелы и двойные котировки () больше не будут удалены из значений формы запроса. Эти изменения не будут нарушать число существующих клиентов и помогут убедиться, что запросы, отправленные в Azure AD, будут надежно обрабатываться каждый раз.

Для получения дополнительной [Azure AD breaking changes notices](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored)информации см.

---

## <a name="july-2019"></a>Июль 2019 г.

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>План изменения: Обновление службы прокси-приложений для поддержки только TLS 1.2

**Тип.** Планирование изменений.  
**Категория службы.** Прокси-сервер приложения.  
**Возможность продукта.** Контроль доступа.

Чтобы обеспечить вам наше самое сильное шифрование, мы собираемся начать ограничивать доступ службы Application Proxy только к протоколам TLS 1.2. Это ограничение будет первоначально развернуто для клиентов, которые уже используют протоколы TLS 1.2, так что вы не увидите влияние. Полное амортизацию протоколов TLS 1.0 и TLS 1.1 завершится 31 августа 2019 года. Клиенты, все еще использующие TLS 1.0 и TLS 1.1, получат расширенное уведомление для подготовки к этому изменению.

Для поддержания подключения к службе Application Proxy на протяжении всего этого изменения, мы рекомендуем вам убедиться, что ваши комбинации клиент-сервер и браузер-сервер обновляются для использования TLS 1.2. Мы также рекомендуем вам включить любые клиентские системы, используемые вашими сотрудниками для доступа к приложениям, опубликованным через службу Application Proxy.

Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>План изменений: Обновления дизайна приходят для галереи приложений

**Тип.** Планирование изменений.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Единый вход.

Новые изменения пользовательского интерфейса приходят к дизайну **Добавить из галереи** области добавить лезвие **приложения.** Эти изменения помогут вам легче найти приложения, которые поддерживают автоматическое обеспечение, OpenID Connect, язык цензуры безопасности (SAML) и пароль одиночный знак (SSO).

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>План изменений: Удаление IP-адреса сервера МИД с IP-адреса Office 365

**Тип.** Планирование изменений.  
**Категория обслуживания:** Мид  
**Возможности продукта.** Безопасность и защита удостоверений.

Мы удаляем IP-адрес сервера МИД из [IP-адреса Office 365 и веб-сервиса URL.](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service) Если в настоящее время вы полагаетесь на эти страницы для обновления настроек брандмауэра, необходимо убедиться, что вы также включаете список IP-адресов, задокументированных в разделе **требований к требованиям брандмауэра Azure Multi-Factor Authentication Server,** который начинается со статьей [Azure Multi-Factor Authentication Server.](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements)

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Токены только для приложений теперь требуют, чтобы клиентское приложение существовало в ресурсе-арендаторе

**Тип.** Исправление.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Проверка подлинности пользователей

26 июля 2019 года мы изменили способ предоставления токенов только приложений через [грант на учетные данные клиентов.](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) Ранее приложения могли получать токены для вызова других приложений, независимо от того, было ли клиентское приложение в клиенте. Мы обновили это поведение, поэтому ресурсы с одним арендатором, иногда называемые Web-aIs, могут вызываться только клиентскими приложениями, которые существуют в ресурсе-арендаторе.

Если ваше приложение не находится в ресурсе, вы получите сообщение `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` об ошибке, в котором говорится, что для устранения этой проблемы необходимо создать в клиентском приложении принцип службы клиента, используя либо [конечную точку согласия админа,](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) либо [через PowerShell,](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell)которая гарантирует, что ваш арендатор дал разрешение на работу в рамках арендатора.

Для получения дополнительной информации, см [Что нового для проверки подлинности?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Существующее согласие между клиентом и API по-прежнему не требуется. Приложения должны по-прежнему проводить свои собственные проверки авторизации.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Новый без пароль ввеза в Azure AD с помощью ключей безопасности FIDO2

**Тип.** Новая функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Проверка подлинности пользователей

Клиенты Azure AD теперь могут устанавливать политики для управления ключами безопасности FIDO2 для пользователей и групп своих организаций. Конечные пользователи также могут самостоятельно регистрировать свои ключи безопасности, использовать ключи для вхлых в свои учетные записи Майкрософт на веб-сайтах, находясь на устройствах, способных FIDO, а также войти в свои устройства с Windows 10, присоединенные к AD, примкнув к AD.

Для получения дополнительной информации можно получить информацию о том, что без паролей в [ещих аудиенции (предварительный просмотр)](/azure/active-directory/authentication/concept-authentication-passwordless) для информации, связанной с администратором, и [настроить информацию о безопасности, чтобы использовать ключ безопасности (Preview)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) для информации, связанной с конечными пользователями.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Новые федеративные приложения доступны в галерее Azure AD App - июль 2019

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.

В июле 2019 года мы добавили эти 18 новых приложений с поддержкой Федерации в галерею приложений:

[Ungerboeck программного обеспечения](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [Яркий шаблон Omnichannel Контакт-центр](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), [Умный Нелли](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [Looop](https://www.looop.co/schedule-a-demo/), [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [MS Azure SSO Доступ для Ethidex Соответствие Office&trade;](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [Hype](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [Абстрактный](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [Ascentis](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [HyperAnna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [PharmID WasteWitness](https://pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Автоматизировать подготовку учетной записи пользователя для этих недавно поддерживаемых приложений SaaS

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Мониторинг и отчетность.

Теперь можно автоматизировать создание, обновление и удаляние учетных записей пользователей для этих новых интегрированных приложений:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Федеративный каталог](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Для получения дополнительной информации о том, как лучше обезопасить свою организацию с помощью автоматизированного обеспечения функциональности учетной записи пользователя, [см.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Новый тег служб домена Azure AD для группы сетевой безопасности

**Тип.** Новая функция.  
**Категория службы.** Доменные службы Azure AD.  
**Возможности продукта.** Доменные службы Azure AD.

Если вы устали управлять длинными списками IP-адресов и диапазонов, вы можете использовать новый сетевой сервисный тег **AzureActiveDirectoryDomainServices** в группе сетевой безопасности Azure, чтобы обеспечить входящему трафик в виртуальную сетевую подсеть Azure AD Domain Services.

Для получения дополнительной информации об этом новом теге службы см. [Группы сетевой безопасности для служб доменов Azure AD.](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports)

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Новые аудиты безопасности для служб доменов Azure AD (публичный предварительный просмотр)

**Тип.** Новая функция.  
**Категория службы.** Доменные службы Azure AD.  
**Возможности продукта.** Доменные службы Azure AD.

Мы рады сообщить о выпуске Azure AD Domain Service Security Auditing для публичного предварительного просмотра. Аудит безопасности помогает получить важное представление об службах аутентификации, перелистывая события аудита безопасности на целевые ресурсы, включая рабочие пространства Azure Log Analytics и Azure Event Hub, используя портал Azure AD Domain Service.

Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events)

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Использование новых методов аутентификации & информации (Публичный предварительный просмотр)

**Тип.** Новая функция.  
**Категория службы.** Самостоятельный сброс пароля.  
**Возможности продукта.** Мониторинг и отчетность.

Новые методы проверки & отчеты по анализу информации могут помочь вам понять, как в организации регистрируются и используются такие функции, как Azure Multi-Factor Authentication и сбросить пароли самообслуживания, включая количество зарегистрированных пользователей для каждой функции, как часто используется сбросить пароли самообслуживания для сбросить пароли и каким способом происходит сбросить пароль.

Для получения дополнительной информаци [&и](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights)см.

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Новые отчеты о безопасности доступны для всех администраторов Azure AD (публичный предварительный просмотр)

**Тип.** Новая функция.  
**Категория обслуживания:** Защита личных данных  
**Возможности продукта.** Безопасность и защита удостоверений.

Теперь все администраторы Azure AD могут выбрать баннер в верхней части существующих отчетов безопасности, таких как **пользователи, помеченные для** отчета о рисках, чтобы начать использовать новый опыт безопасности, как показано в **пользователях Рискованных** и **отчетов о сопряжении рисков.** Со временем все отчеты о безопасности будут переходить от старых версий к новым, а новые отчеты предоставят вам следующие дополнительные возможности:

- Расширенная фильтрация и сортировка

- Массовые действия, такие как увольнение пользователей риска

- Подтверждение скомпрометированных или безопасных объектов

- Состояние риска, покрытие: В опасности, Уволенный, Исправленный и Подтвержденный скомпрометирован

Для получения дополнительной информации [см. Отчет о рискованных пользователях](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users) и [отчет о рискованных всхыдюймах.](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins)

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Новые аудиты безопасности для служб доменов Azure AD (публичный предварительный просмотр)

**Тип.** Новая функция.  
**Категория службы.** Доменные службы Azure AD.  
**Возможности продукта.** Доменные службы Azure AD.

Мы рады сообщить о выпуске Azure AD Domain Service Security Auditing для публичного предварительного просмотра. Аудит безопасности помогает получить важное представление об службах аутентификации, перелистывая события аудита безопасности на целевые ресурсы, включая рабочие пространства Azure Log Analytics и Azure Event Hub, используя портал Azure AD Domain Service.

Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events)

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Новая прямая федерация B2B с использованием SAML/WS-Fed (публичный предварительный просмотр)

**Тип.** Новая функция.  
**Категория службы.** B2B.  
**Возможности продукта.** B2B и B2C.

Прямая федерация помогает облегчить вам работу с партнерами, чье иТ-управляемое решение для идентификации не является Azure AD, работая с системами идентификации, поддерживающими стандарты SAML или WS-Fed. После налаживание прямых отношений с партнером, любой новый приглашенный пользователь, которого вы приглашаете из этого домена, может сотрудничать с вами, используя существующую организационную учетную запись, что делает пользовательский опыт ваших гостей более бесшовным.

Для получения дополнительной информации [см. Прямая федерация с AD FS и сторонними поставщиками для гостевых пользователей (предварительный просмотр)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Автоматизировать подготовку учетной записи пользователя для этих недавно поддерживаемых приложений SaaS

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Мониторинг и отчетность.

Теперь можно автоматизировать создание, обновление и удаляние учетных записей пользователей для этих новых интегрированных приложений:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Федеративный каталог](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Для получения дополнительной информации о том, как лучше обезопасить свою организацию с помощью автоматизированной подготовки учетной записи пользователя, [см.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Новая проверка дубликатов имен групп на портале Azure AD

**Тип.** Новая функция.  
**Категория службы.** Управление группами.  
**Возможности продукта.** Совместная работа.

Теперь, когда вы создаете или обновляете название группы с портала Azure AD, мы проведем проверку, чтобы увидеть, дублируете ли вы существующее имя группы в вашем ресурсе. Если мы определим, что имя уже используется другой группой, вам будет предложено изменить ваше имя.

Для получения дополнительной информации на [портале Azure AD можно ознакомиться на портале «Управление группами».](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD теперь поддерживает параметры статического запроса в ответах (перенаправить) URIs

**Тип.** Новая функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Проверка подлинности пользователей

Приложения Azure AD теперь могут регистрировать и использовать URL-изыумации `https://contoso.com/oauth2?idp=microsoft`ответов (перенаправление) с параметрами статических запросов (например, для запросов OAuth 2.0. Параметр статического запроса подлежит сопоставлению строк для URI ответа, как и любая другая часть ответа URI. Если нет зарегистрированной строки, которая соответствует перекодированному URL-ури, запрос отклоняется. При обнаружении ответа URI используется вся строка для перенаправления пользователя, включая параметр статического запроса.

Динамические УРИ ответа по-прежнему запрещены, поскольку они представляют угрозу безопасности и не могут быть использованы для сохранения государственной информации в запросе на аутентификацию. Для этого используйте `state` параметр.

В настоящее время экраны регистрации приложений портала Azure по-прежнему блокируют параметры запроса. Тем не менее, вы можете вручную отсеить манифест приложения для добавления и тестирования параметров запроса в приложении. Для получения дополнительной информации, см [Что нового для проверки подлинности?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Логи активности (MS Graph AIS) для Azure AD теперь доступны через PowerShell Cmdlets

**Тип.** Новая функция.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.

Мы рады сообщить, что журналы активности Azure AD (отчеты об аудите и входах) теперь доступны через модуль Azure AD PowerShell. Ранее можно было создавать собственные скрипты с помощью конечных точек MS GraphPi, а теперь мы расширили эту возможность до cmdlets PowerShell.

Для получения дополнительной информации о том, [Azure AD PowerShell cmdlets for reporting](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting)как использовать эти cmdlets, см.

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Обновлено управление фильтром для журналов аудита и входа в систему Azure AD

**Тип.** Измененная функция.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.

Мы обновили отчеты о журналах аудита и входа, чтобы теперь можно применять различные фильтры без необходимости добавлять их в качестве столбцов на экранах отчетов. Кроме того, теперь вы можете решить, сколько фильтров вы хотите показать на экране. Все эти обновления работают вместе, чтобы сделать ваши отчеты более простыми для чтения и более масштабированными для ваших потребностей.

Для получения дополнительной информации об этих [Filter sign-in activities](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities) [обновлениях](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) см.

---

## <a name="june-2019"></a>Июнь 2019 г.

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Новый API обнаружения рисков для Microsoft Graph (публичный предварительный просмотр)

**Тип.** Новая функция.  
**Категория обслуживания:** Защита личных данных  
**Возможности продукта.** Безопасность и защита удостоверений.

Мы рады сообщить о том, что новый API riskDetections для Microsoft Graph теперь находится в открытом доступе. Этот новый API можно использовать для просмотра списка пользователей, связанных с защитой идентификации вашей организации, и обнаружения рисков, связанных с регистрацией. Вы также можете использовать этот API для более эффективного запроса обнаружения рисков, включая сведения о типе обнаружения, статусе, уровне и многом другом.

Для получения дополнительной информации ознакомьтесь с [справочной документацией API обнаружения рисков.](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Новые федеративные приложения доступны в галерее приложений Azure AD - июнь 2019

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.

В июне 2019 года мы добавили эти 22 новых приложения с поддержкой Федерации в галерею приложений:

[Azure AD SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai ,](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial) [АНАЗУА](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [Azure VPN Клиент](https://portal.azure.com/), [ExpenseIn](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [Помощник Помощник](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial), [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz In-Car Office](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), [Oracle Cloud Infrastructure Консоль](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk SAML Аутентификация](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [Scrible Edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/software/), [Vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager для Oracle Retail Merchandising, Oracle Access Manager для Oracle E-Business Suite, Oracle IDCS для E-Business Suite, Oracle IDCS для PeopleSoft, Oracle IDCS для JD Edwards

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Автоматизировать подготовку учетной записи пользователя для этих недавно поддерживаемых приложений SaaS

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Мониторинг и отчетность.

Теперь можно автоматизировать создание, обновление и удаляние учетных записей пользователей для этих новых интегрированных приложений:

- [Zoom](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Envoy](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Для получения дополнительной информации о том, как лучше обезопасить свою организацию с помощью автоматизированного обеспечения функциональности учетной записи пользователя, [см.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Просмотр работы службы подготовки Azure AD в режиме реального времени

**Тип.** Измененная функция.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта:** Управление жизненным циклом идентичности

Мы обновили интерфейс подготовки Azure AD, включив в него новую панель прогресса, которая показывает, как далеко вы находитесь в процессе подготовки пользователей. Этот обновленный опыт также предоставляет информацию о количестве пользователей, подготовленных в течение текущего цикла, а также о том, сколько пользователей было подготовлено к настоящему времени.

Для получения дополнительной [информации, см.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Брендинг компании теперь появляется на вывески и экранах ошибок

**Тип.** Измененная функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Проверка подлинности пользователей

Мы обновили Azure AD, чтобы брендинг вашей компании теперь отосвазался на экранах вывески и ошибок, а также на странице входа. Вам не нужно ничего делать, чтобы включить эту функцию, Azure AD просто использует ресурсы, которые вы уже создали в области **брендинга компании** портала Azure.

Для получения дополнительной информации о настройке брендинга компании можно [добавить брендинг на страницы Active Directory вашей организации.](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding)

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Сервер Azure Multi-Factor Authentication (MFA) больше не доступен для новых развертываний

**Тип.** Прекращение поддержки.  
**Категория обслуживания:** Мид  
**Возможности продукта.** Безопасность и защита удостоверений.

С 1 июля 2019 года Microsoft больше не будет предлагать MFA Server для новых развертываний. Новые клиенты, которым требуется многофакторная аутентификация в своей организации, теперь должны использовать многофакторную аутентификацию Azure на основе облака. Клиенты, активирующие MFA Server до 1 июля, не увидят изменений. Вы все еще сможете загрузить последнюю версию, получить будущие обновления и создать учетные данные активации.

Для получения дополнительной информации см. [Начало работы с azure Multi-Factor Authentication Server](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy). Для получения дополнительной информации об облачной azure Multi-Factor Authentication см. [Планирование многофакторной аутентификации Azure.](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

---

## <a name="may-2019"></a>Май 2019 г.

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Изменение службы: Будущая поддержка только протоколов TLS 1.2 на службе Application Proxy

**Тип.** Планирование изменений.  
**Категория службы.** Прокси-сервер приложения.  
**Возможность продукта.** Контроль доступа.

Чтобы обеспечить лучшее в своем классе шифрование для наших клиентов, мы ограничиваем доступ только к протоколам TLS 1.2 в службе Application Proxy. Это изменение постепенно развертывается для клиентов, которые уже используют только протоколы TLS 1.2, так что вы не должны видеть никаких изменений.

Износ TLS 1.0 и TLS 1.1 происходит 31 августа 2019 года, но мы предоставим дополнительное предварительное уведомление, так что у вас будет время, чтобы подготовиться к этому изменению. Чтобы подготовиться к этому изменению, убедитесь, что комбинации клиент-сервер и браузер-сервер, включая любых клиентов, используемых пользователями для доступа к приложениям, опубликованным через Application Proxy, обновляются для использования протокола TLS 1.2 для поддержания подключения к службе App Proxy. Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin)

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Используйте отчет об использовании и сведениях для просмотра данных о входе в приложение

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Мониторинг и отчетность.

Теперь вы можете использовать отчет об использовании и сведениях, расположенный в области **корпоративных приложений** портала Azure, чтобы получить ориентированное на приложение представление данных входа, включая информацию о:

- Топ использованных приложений для вашей организации

- Приложения с самыми неудачными ввозами

- Лучшие ошибки в вхаски для каждого приложения

Более подробную информацию об этой функции можно получить [на портале Active Directory Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Автоматизация подготовки пользователей к облачным приложениям с помощью Azure AD

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Мониторинг и отчетность.

Следуйте этим новым учебникам, чтобы использовать службу предоставления AD Azure для автоматизации создания, удаления и обновления учетных записей пользователей для следующих облачных приложений:

- [Комит](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [Динамическийсигнал](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

Вы также можете следить за этим новым [учебником Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), который предоставляет информацию о том, как обеспечить групповые объекты.

Для получения дополнительной информации о том, как лучше [Automate user provisioning to SaaS applications with Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)обезопасить свою организацию с помощью автоматизированного обеспечения функциональности учетной записи пользователя, см.

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Безопасный балл идентификации теперь доступен в Azure AD (общая доступность)

**Тип.** Новая функция.  
**Категория службы:** недоступно  
**Возможности продукта.** Безопасность и защита удостоверений.

Теперь вы можете отслеживать и улучшать свою позицию безопасности идентификации, используя функцию идентификационных данных в Azure AD. Функция обеспечения идентификационных данных использует одну панель мониторинга, чтобы помочь вам:

- Объективно измерьте вашу позу безопасности личности, на основе оценки между 1 и 223.

- Планирование улучшений безопасности идентификационных данных

- Просмотрите успешность улучшений безопасности

Для получения дополнительной информации о [What is the identity secure score in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score)функции оценки безопасности идентификации см.

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Новый опыт регистрации приложений теперь доступен (общая доступность)

**Тип.** Новая функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Возможности для разработчиков

Новый опыт [регистрации приложений](https://aka.ms/appregistrations) теперь в общей доступности. Этот новый опыт включает в себя все ключевые функции, с которые вы знакомы с портала Azure и портала регистрации приложений, и улучшает их с помощью:

- **Улучшение управления приложениями.** Вместо того, чтобы видеть приложения на разных порталах, теперь вы можете видеть все приложения в одном месте.

- **Упрощенная регистрация приложений.** От улучшенного опыта навигации до обновленного опыта выбора разрешений — теперь проще регистрировать и управлять приложениями.

- **Более подробная информация.** Вы можете найти более подробную информацию о вашем приложении, в том числе руководства по быстрому запуску и многое другое.

Для получения дополнительной информации, см [Microsoft идентификационной платформы](https://docs.microsoft.com/azure/active-directory/develop/) и [App регистрации опыт теперь, как правило, доступны!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) блог объявление.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Новые возможности, доступные в API рисковых пользователей для защиты личных данных

**Тип.** Новая функция.  
**Категория обслуживания:** Защита личных данных  
**Возможности продукта.** Безопасность и защита удостоверений.

Мы рады сообщить, что теперь вы можете использовать API рискованных пользователей для получения истории риска пользователей, увольнения рискованных пользователей и подтверждения того, что пользователи были скомпрометированы. Это изменение поможет вам более эффективно обновлять статус риска пользователей и понимать их историю риска.

Для получения дополнительной [Risky Users API reference documentation](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)информации см.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Новые федеративные приложения доступны в галерее приложений Azure AD - май 2019

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.

В мае 2019 года мы добавили в галерею приложений эти 21 новое приложение с поддержкой Федерации:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [Real Links](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Sign](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [Displayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo Sales Engage](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [OutSystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [Meta4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [Quantum Workplace](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [Cobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [webMethods API Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Улучшенный опыт создания и управления группами на портале Azure AD

**Тип.** Новая функция.  
**Категория службы.** Управление группами.  
**Возможности продукта.** Совместная работа.

Мы внесли улучшения в опыт групп, связанных с Azure AD. Эти улучшения позволяют администраторам лучше управлять списками групп, списками членов и предоставлять дополнительные возможности создания.

Добавлены некоторые улучшения, среди которых:

- Базовая фильтрация по типу членства и типу группы.

- Добавление новых столбцов, таких как адрес источника и адреса электронной почты.

- Возможность многоразовых групп, членов и списков владельцев для легкого удаления.

- Возможность выбора адреса электронной почты и добавления владельцев во время создания группы.

Дополнительные сведения см. в разделе [Создание базовой группы и добавление членов с помощью Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Налаживание политики именования для групп Office 365 на портале Azure AD (общая доступность)

**Тип.** Измененная функция.  
**Категория службы.** Управление группами.  
**Возможности продукта.** Совместная работа.

Администраторы теперь могут настроить политику именования для групп Office 365 с помощью портала Azure AD. Это изменение помогает обеспечить соблюдение согласованных конвенций именования для групп Office 365, созданных или отредактированных пользователями в вашей организации.

Вы можете настроить политику именования для групп Office 365 двумя различными способами:

- Определите префиксы или суффиксы, которые автоматически добавляются к названию группы.

- Загрузите индивидуальный набор заблокированных слов для организации, которые не допускаются в названиях групп (например, "CEO, Payroll, HR").

Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy)

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Конечные точки Microsoft GraphPI теперь доступны для журналов деятельности Azure AD (общая доступность)

**Тип.** Измененная функция.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.

Мы рады сообщить об общей доступности поддержки конечных точек Microsoft GraphPI для журналов активности Azure AD. С помощью этого выпуска теперь можно использовать версию 1.0 журналов аудита Azure AD, а также AIS журналов входа.

Для получения дополнительной информации смотрите [обзор API-журнала API аудита Azure AD.](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0)

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Администраторы теперь могут использовать Условный доступ для комбинированного процесса регистрации (публичный предварительный просмотр)

**Тип.** Новая функция.  
**Категория обслуживания:** Условный доступ  
**Возможности продукта.** Безопасность и защита удостоверений.  

Администраторы теперь могут создавать политики условного доступа для использования на объединенной странице регистрации. Это включает в себя применение политик, чтобы разрешить регистрацию, если:

- Пользователи находятся в доверенной сети.

- Пользователи подвержены низкому риску входиний.

- Пользователи находятся на управляемом устройстве.

- Пользователи соглашаются с условиями использования организации (TOU).

Для получения дополнительной информации об условном доступе и сбое паролей можно ознакомиться с публикацией в [блоге «Условный доступ к AD» с комбинированным МИД и сбросить пароль.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348) Для получения дополнительной информации о политиках условного доступа для комбинированного процесса [регистрации см.](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration) Для получения дополнительной информации о функциях использования [Azure Active Directory terms of use feature](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)Azure AD см.

---

## <a name="april-2019"></a>Апрель 2019 г.

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Новое обнаружение угроз Azure AD теперь доступно в рамках раздела Защиты идентификационных данных Azure AD

**Тип.** Новая функция.  
**Категория обслуживания:** Защита идентификационных данных AD Azure  
**Возможности продукта.** Безопасность и защита удостоверений.

Обнаружение угроз Azure AD теперь доступно в рамках обновленной функции защиты идентификационных данных Azure AD. Эта новая функциональность помогает указать необычную активность пользователя для конкретного пользователя или действия, которая согласуется с известными шаблонами атак на основе внутренних и внешних источников разведки угроз корпорации Майкрософт.

Для получения дополнительной информации об обновленной версии Azure AD Identity Protection см. [Четыре основных усовершенствования защиты идентификационных данных Azure AD теперь в общедоступном](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) блоге предварительного просмотра и в блоге [«Что такое активная защита идентификации каталогов Azure» (обновлено)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) . Для получения дополнительной информации об обнаружении интеллектуальных угроз Azure AD смотрите статью обнаружения рисков обнаружения [рисков Azure Active Directory Protection.](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks)

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Управление правами Azure AD теперь доступно (публичный предварительный просмотр)

**Тип.** Новая функция.  
**Категория обслуживания:** Управление идентификацией  
**Возможности продукта:** Управление идентификацией

Управление правами Azure AD, которое теперь доступно для предварительного просмотра, помогает клиентам делегировать управление пакетами доступа, что определяет, как сотрудники и деловые партнеры могут запрашивать доступ, кто должен одобрить и как долго они имеют доступ. Пакеты доступа могут управлять членством в группах Azure AD и Office 365, ролевые назначения в корпоративных приложениях и ролевые назначения для сайтов SharePoint Online. Узнайте больше об [управлении правами в обзоре управления правами Azure AD.](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) Чтобы узнать больше о широте функций управления идентификацией Azure AD, включая [What is Azure AD Identity Governance?](../governance/identity-governance-overview.md)привилегированное управление идентификацией, обзоры доступа и условия использования, см.

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Налаживание политики именования для групп Office 365 на портале Azure AD (публичный предварительный просмотр)

**Тип.** Новая функция.  
**Категория службы.** Управление группами.  
**Возможности продукта.** Совместная работа.

Администраторы теперь могут настроить политику именования для групп Office 365 с помощью портала Azure AD. Это изменение помогает обеспечить соблюдение согласованных конвенций именования для групп Office 365, созданных или отредактированных пользователями в вашей организации.

Вы можете настроить политику именования для групп Office 365 двумя различными способами:

- Определите префиксы или суффиксы, которые автоматически добавляются к названию группы.

- Загрузите индивидуальный набор заблокированных слов для организации, которые не допускаются в названиях групп (например, "CEO, Payroll, HR").

Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy)

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>В Azure Monitor (общая доступность) теперь доступны журналы активности Azure AD

**Тип.** Новая функция.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.

Чтобы помочь устранить отзывы о визуализации с помощью журналов деятельности Azure AD, мы представляем новую функцию Insights в журнале Analytics. Эта функция поможет вам получить представление о ресурсах Azure AD с помощью наших интерактивных шаблонов под названием Workbooks. Эти предварительно построенные рабочие книги могут предоставить подробную информацию для приложений или пользователей, и включают в себя:

- **Вопинье.** Предоставляет подробную информацию для приложений и пользователей, включая местоположение ввода, операционную систему и браузерный клиент и версию, а также количество успешных или неудачных ввода.

- **Устаревшая аутентификация и условный доступ.** Предоставляет подробную информацию для приложений и пользователей, использующих устаревшую аутентификацию, включая использование многофакторной аутентификации, вызванное политиками условного доступа, приложения, использующие политики условного доступа, и так далее.

- **Анализ сбоя.** Помогает определить, происходят ли ошибки в ввне в ввоза из-за действий пользователя, проблем с политикой или инфраструктуры.

- **Пользовательские отчеты.** Можно создавать новые или отстранять существующие трудовые книжки, чтобы настроить функцию Insights для организации.

Для получения дополнительной информации см. [Как использовать рабочие книги Azure Monitor для отчетов Active Directory Azure.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Новые федеративные приложения доступны в галерее приложений Azure AD - апрель 2019

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.

В апреле 2019 года мы добавили в галерею приложений эти 21 новое приложение с поддержкой Федерации:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [Indiggo](https://indiggolead.com/) [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial) [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial) [Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [Benchling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileI ,](https://mileiq.onelink.me/991934284/7e980085) [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial) [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [Mitel Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), Alibaba [Cloud (Role-based SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent Equity](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial) [monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial) [Management](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial),

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Новый вариант проверки частоты доступа и выбор нескольких ролей

**Тип.** Новая функция.  
**Категория обслуживания:** Обзоры доступа  
**Возможности продукта:** Управление идентификацией

Новые обновления в обзорах доступа Azure AD позволяют:

- Измените частоту ваших обзоров доступа на **полугодовой,** в дополнение к ранее существующим опциям еженедельно, ежемесячно, ежеквартально и ежегодно.

- При создании единого обзора доступа выберите несколько ролей ресурсов Azure AD и Azure. В этой ситуации все роли настроены с одинаковыми настройками, и все рецензенты уведомляются одновременно.

Для получения дополнительной информации о том, как создать обзор доступа, [см.](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Система оповещения Azure AD Connect (ы) переходит, отправляя новую информацию об отправителе электронной почты некоторым клиентам

**Тип.** Измененная функция.  
**Категория службы.** AD Sync.  
**Возможности продукта.** Платформа.

Azure AD Connect находится в процессе перехода нашей системы оповещения электронной почты (ы), потенциально показывая некоторым клиентам нового отправителя электронной почты. Чтобы решить эту проблему, необходимо добавить `azure-noreply@microsoft.com` в список разрешений организации, иначе вы не сможете продолжать получать важные оповещения от служб Office 365, Azure или синхронизации.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Изменения суффикса UPN теперь успешны между федеративными доменами в Azure AD Connect

**Тип.** Исправление.  
**Категория службы.** AD Sync.  
**Возможности продукта.** Платформа.

Теперь вы можете успешно изменить суффикс UPN пользователя с одного федеративного домена на другой федеративный домен в Azure AD Connect. Это исправление означает, что вы больше не должны испытывать сообщение об ошибке FederatedDomainChangeError во время цикла синхронизации или получать уведомление электронной почты с указанием: "Не удается обновить этот объект в Azure Active Directory, потому что атрибут "FederatedUser.UserPrincipalName" не действителен. Обновление значения в местных службах каталога".

Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror)

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Повышенная безопасность с помощью политики условного доступа на основе приложения в Azure AD (публичный предварительный просмотр)

**Тип.** Новая функция.  
**Категория обслуживания:** Условный доступ  
**Возможности продукта.** Безопасность и защита удостоверений.

Условный доступ на основе защиты приложений теперь доступен с помощью политики **защиты приложений Require.** Эта новая политика помогает повысить безопасность вашей организации, помогая предотвратить:

- Пользователи получают доступ к приложениям без лицензии Microsoft Intune.

- Пользователи не могут получить политику защиты приложений Microsoft Intune.

- Пользователи получают доступ к приложениям без настроенной политики защиты приложений Microsoft Intune.

Для получения дополнительной информации [см. Как требовать политики защиты приложений для доступа к облачным приложениям с помощью условия условного доступа.](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access)

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Новая поддержка единого всхотивки Azure AD и условного доступа в Microsoft Edge (публичный предварительный просмотр)

**Тип.** Новая функция.  
**Категория обслуживания:** Условный доступ  
**Возможности продукта.** Безопасность и защита удостоверений.

Мы расширили нашу поддержку Azure AD для Microsoft Edge, в том числе предоставив новую поддержку одиночному всхотвому и условному доступу Azure AD. Если ранее вы использовали управляемый браузер Microsoft Intune, теперь вы можете использовать Microsoft Edge.

Для получения дополнительной информации о настройке и управлении устройствами и приложениями с [Require approved client apps for cloud app access with Conditional Access](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)использованием условия "Условный доступ" [см.](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) Для получения дополнительной информации о том, как управлять доступом с помощью Microsoft Edge с помощью политик Microsoft Intune, смотрите [Управление доступом в Интернет с помощью защищенного от политики microsoft Intune браузера.](https://docs.microsoft.com/intune/app-configuration-managed-browser)

---

## <a name="march-2019"></a>Март 2019 г.

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Рамки опыта идентификации и поддержка пользовательской политики в Azure Active Directory B2C теперь доступны (GA)

**Тип.** Новая функция.  
**Категория службы.** B2C — управление удостоверениями клиентов.  
**Возможности продукта.** B2B и B2C.

Теперь можно создавать пользовательские политики в Azure AD B2C, включая следующие задачи, которые поддерживаются в масштабе и в рамках нашей Azure SLA:

- Создавайте и загружайте пользовательские поездки пользователей по проверке подлинности с помощью пользовательских политик.

- Поэтапно описывать пути взаимодействия пользователя для обмена данными между поставщиками удостоверений.

- Определять условные ветвления путей взаимодействия пользователей.

- Преобразование и карта претензий для использования в принятии решений и коммуникаций в режиме реального времени.

- Используйте службы с поддержкой REST API в пользовательских поездках пользователя по проверке подлинности. Например, с поставщиками электронной почты, CRM и несвободными системами авторизации.

- Федеративная связь с поставщиками идентификационных данных, которые соответствуют протоколу OpenIDConnect. Например, с мультитенантными Azure AD, поставщиками социальных счетов или двухфакторными поставщиками проверки.

Для получения дополнительной информации о создании пользовательских политик см. [Заметки разработчиков для пользовательских политик в Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) и прочитайте [сообщение блога Алекса Саймона, включая тематические исследования.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Новые федеративные приложения доступны в галерее приложений Azure AD - март 2019

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.

В марте 2019 года мы добавили эти 14 новых приложений с поддержкой Федерации в галерею приложений:

[Делегат ISEC7 Mobile Exchange](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [система аудита на основе объяснений](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [экономичный](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [Powerschool Performance Matters](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [интрасети Iris](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit Horizons](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [TAS](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Новые разъемы для подготовки новых разъемов «Скразлер» и «Атлас» в галерее Azure AD – март 2019 года

**Тип.** Новая функция.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.

Автоматизация создания, обновления и удаливания учетных записей пользователей для следующих приложений:

[Эскалер](https://aka.ms/ZscalerProvisioning), [Sscaler бета](https://aka.ms/ZscalerBetaProvisioning)-, [Эскаллер Один](https://aka.ms/ZscalerOneProvisioning), [Эскаллер Два](https://aka.ms/ZscalerTwoProvisioning), [Sscaler Три](https://aka.ms/ZscalerThreeProvisioning), [Sscaler SCloud](https://aka.ms/ZscalerZSCloudProvisioning), [Атласский облако](https://aka.ms/atlassianCloudProvisioning)

Для получения дополнительной информации о том, как лучше [Automate user provisioning to SaaS applications with Azure AD](https://aka.ms/ProvisioningDocumentation)обезопасить свою организацию с помощью автоматизированного обеспечения функциональности учетной записи пользователя, см.

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Восстановление и управление удаленными группами Office 365 на портале Azure AD

**Тип.** Новая функция.  
**Категория службы.** Управление группами.  
**Возможности продукта.** Совместная работа.

Теперь вы можете просматривать и управлять удаленными группами Office 365 с портала Azure AD. Это изменение поможет вам увидеть, какие группы доступны для восстановления, а также позволяет удаленно удалить любые группы, которые не нужны вашей организации.

Для получения дополнительной информации смотрите [Restore expired или удаленные группы](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Единый входе теперь доступен для защищенных Azure AD SAML приложений с помощью приложения Proxy (публичный предварительный просмотр)

**Тип.** Новая функция.  
**Категория службы.** Прокси-сервер приложения.  
**Возможность продукта.** Контроль доступа.

Теперь вы можете предоставить один опыт регистрации (SSO) для местных, SAML-аутентифицированных приложений, а также удаленный доступ к этим приложениям через Application Proxy. Для получения дополнительной информации о том, как настроить SAML [SAML single sign-on for on-premises applications with Application Proxy (Preview)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps)SSO с вашими приложениями, см.

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Клиентские приложения в циклах запросов будут прерваны для повышения надежности и пользовательского опыта

**Тип.** Новая функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Проверка подлинности пользователей

Клиентские приложения могут неправильно выдавать сотни одинаковых запросов входа в течение короткого периода времени. Эти запросы, независимо от того, являются ли они успешными или нет, все это способствует плохой пользовательский опыт и повышенная рабочая нагрузка для ВПЛ, увеличение задержки для всех пользователей и снижение доступности ВПЛ.

Это обновление отправляет `invalid_grant` ошибку: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` для клиентских приложений, которые выдают дубликаты запросов несколько раз в течение короткого периода времени, за рамки обычной работы. Клиентские приложения, столкнувшиеся с этой проблемой, должны отображать интерактивный запрос, требующий, чтобы пользователь снова вписывались. Для получения дополнительной информации об этом изменении и о том, как исправить приложение, если оно сталкивается с этой ошибкой, [см.](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted)

---

### <a name="new-audit-logs-user-experience-now-available"></a>Новый пользовательский интерфейс журналов аудита теперь доступен

**Тип.** Измененная функция.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.

Мы создали новую страницу **журналов** Azure AD Audit, чтобы улучшить как читаемость, так и способ поиска информации. Чтобы увидеть новую страницу **журналов аудита,** выберите **журналы аудита** в разделе **Активность** Azure AD.

![Новая страница журналов аудита, с информацией о образцах](media/whats-new/audit-logs-page.png)

Более подробную информацию о новой странице **журналов аудита** можно узнать [на портале Active Directory Azure.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs)

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Новые предупреждения и рекомендации, помогающие предотвратить случайную блокировку администратора от неправильно настроенных политик условного доступа

**Тип.** Измененная функция.  
**Категория обслуживания:** Условный доступ  
**Возможности продукта.** Безопасность и защита удостоверений.

Чтобы предотвратить случайные блокировки администраторов из собственных клиентов с помощью неправильно настроенных политик условного доступа, мы создали новые предупреждения и обновленные рекомендации на портале Azure. Для получения дополнительной информации [What are service dependencies in Azure Active Directory Conditional Access](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies)о новом руководстве см.

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Улучшенные условия использования конечными пользователями на мобильных устройствах

**Тип.** Измененная функция.  
**Категория обслуживания:** Условия использования  
**Возможности продукта.** Система управления.

Мы обновили существующие условия использования, чтобы улучшить то, как вы просматриваете и соглашаетесь с условиями использования на мобильном устройстве. Теперь вы можете увеличить и увеличить, вернуться назад, скачать информацию, и выбрать гиперссылки. Для получения дополнительной информации об [Azure Active Directory terms of use feature](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users)обновленных условиях использования см.

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Доступ к новому опыту загрузки журналов Azure AD Activity

**Тип.** Измененная функция.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.

Теперь вы можете загрузить большое количество журналов активности непосредственно с портала Azure. Это обновление позволяет:

- Скачать до 250 000 строк.

- Получайте уведомления после завершения загрузки.

- Настройте имя файла.

- Определите формат вывода, jSON или CSV.

Для получения более подробной [Quickstart: Download an audit report using the Azure portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report) информации об этой функции см.

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Нарушение изменений: Обновления для оценки состояния Exchange ActiveSync (EAS)

**Тип.** Планирование изменений.  
**Категория обслуживания:** Условный доступ  
**Возможность продукта.** Контроль доступа.

Мы находимся в процессе обновления, как Exchange ActiveSync (EAS) оценивает следующие условия:

- Местоположение пользователя, основанное на стране, регионе или IP-адресе

- Риск при входе

- Платформа устройства

Если вы ранее использовали эти условия в политиках условного доступа, имейте в виду, что поведение состояния может измениться. Например, если ранее в политике использовалось условие местоположения пользователя, можно обнаружить, что политика теперь пропущена в зависимости от местоположения пользователя.

---

## <a name="february-2019"></a>Февраль 2019 г.

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Настраиваемое шифрование токенов Azure AD SAML (публичный предварительный просмотр) 

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Единый вход.

Теперь вы можете настроить любое поддерживаемое приложение SAML для получения зашифрованных токенов SAML. При настройке и использовании с приложением Azure AD шифрует испускаемые утверждения SAML с помощью общедоступного ключа, полученного из сертификата, хранящегося в Azure AD.

Для получения дополнительной информации о настройке шифрования токенов SAML [см.](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Создание обзора доступа для групп или приложений с помощью обзоров доступа КОН AD Azure

**Тип.** Новая функция.  
**Категория обслуживания:** Обзоры доступа  
**Возможности продукта.** Система управления.

Теперь вы можете включить несколько групп или приложений в единый обзор доступа Azure AD для членства в группе или назначения приложения. Отзывы о доступе с несколькими группами или приложениями настраиваются с использованием одних и тех же настроек, и все включенные рецензенты уведомляются одновременно.

Для получения дополнительной информации о том, как создать обзор доступа с помощью Обзоров доступа Azure AD, [см.](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Новые федеративные приложения доступны в галерее приложений Azure AD - февраль 2019

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
В феврале 2019 года мы добавили эти 27 новых приложений с поддержкой Федерации в галерею приложений:

[Euromonitor Паспорт](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [FAT FINGER](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Skyward змлатив](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Разрешение Нажмите, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [Сейсмическая](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [Доля Мечта](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [webMethods Интеграция Облако](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), Знание Везде [LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [OU Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [Periscope данных](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [Netop Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud по Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp Платформа производительности](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Расширенная комбинированная регистрация МИД/SSPR

**Тип.** Измененная функция.  
**Категория службы.** Самостоятельный сброс пароля.  
**Возможности продукта:** Проверка подлинности пользователей

В ответ на отзывы клиентов мы улучшили комбинированный опыт предварительного просмотра регистрации MFA/SSPR, помогая пользователям быстрее регистрировать информацию о безопасности как для МИД, так и для SSPR. 

**Чтобы включить расширенный опыт для ваших пользователей сегодня, выполните следующие действия:**

1. Как глобальный администратор или администратор пользователя, войдите на портал Azure и перейдите на **настройки Azure Active Directory > настройки пользователя > управлять настройками для функций предварительного просмотра панели доступа.** 

2. В **пользователях, которые могут использовать функции предварительного просмотра для регистрации и управления информацией о безопасности - опция обновления,** выбрать включить функции для **выбранной группы пользователей** или для **всех пользователей.**

В течение следующих нескольких недель, мы будем удалять возможность включить старый комбинированный МИД / SSPR регистрации предварительного просмотра опыт для арендаторов, которые еще не включен.

**Чтобы узнать, будет ли элемент управления удален для вашего арендатора, выполните следующие действия:**

1. Как глобальный администратор или администратор пользователя, войдите на портал Azure и перейдите на **настройки Azure Active Directory > настройки пользователя > управлять настройками для функций предварительного просмотра панели доступа.**  

2. Если **пользователи, которые могут использовать функции предварительного просмотра для регистрации и управления опцией информации о безопасности,** настроены на **none,** параметр будет удален из вашего арендатора.

Независимо от того, обращались ли вы ранее на старый комбинированный опыт предварительного просмотра регистрации MFA/SSPR для пользователей или нет, старый опыт будет выключен в будущем. Поэтому мы настоятельно рекомендуем вам как можно скорее перейти к новому, расширенному опыту.

Для получения дополнительной информации о [Cool enhancements to the Azure AD combined MFA and password reset registration experience](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)расширенном опыте регистрации см.

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Обновленный опыт управления политиками для потоков пользователей

**Тип.** Измененная функция.  
**Категория службы.** B2C — управление удостоверениями клиентов.  
**Возможности продукта.** B2B и B2C.

Мы обновили процесс создания и управления политиками для потоков пользователей (ранее известных как встроенные политики) проще. Этот новый опыт теперь является по умолчанию для всех ваших клиентов Azure AD.

Вы можете предоставить дополнительную обратную связь и предложения, используя улыбку или хмуриться значки в **области обратной связи Отправить нам** в верхней части экрана портала.

Для получения дополнительной информации о новом [Azure AD B2C now has JavaScript customization and many more new features](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) опыте управления политикой см.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Выберите определенные версии элементов страницы, предоставленные Azure AD B2C

**Тип.** Новая функция.  
**Категория службы.** B2C — управление удостоверениями клиентов.  
**Возможности продукта.** B2B и B2C.

Теперь можно выбрать конкретную версию элементов страницы, предоставленную Azure AD B2C. Выбрав конкретную версию, вы можете протестировать обновления до того, как они появятся на странице, и вы сможете получить предсказуемое поведение. Кроме того, теперь можно выбрать для обеспечения определенных версий страниц, чтобы разрешить настройки JavaScript. Чтобы включить эту функцию, перейдите на страницу **Свойств** в потоках пользователей.

Для получения дополнительной информации о выборе конкретных версий элементов страницы, [см.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Настраиваемые требования к паролям для конечных пользователей для B2C (GA)

**Тип.** Новая функция.  
**Категория службы.** B2C — управление удостоверениями клиентов.  
**Возможности продукта.** B2B и B2C.

Теперь можно настроить сложность паролей организации для конечных пользователей вместо того, чтобы использовать наемоную политику паролей Azure AD. Из лезвия **свойств** пользователей (ранее известного как встроенные политики) можно выбрать сложность паролей **Simple** или **Strong**или создать **пользовательский** набор требований.

Для получения дополнительной информации о конфигурации требований [сложности](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity)паролей см.

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Новые шаблоны по умолчанию для пользовательских фирменных аутентификации

**Тип.** Новая функция.  
**Категория службы.** B2C — управление удостоверениями клиентов.  
**Возможности продукта.** B2B и B2C.

Вы можете использовать наши новые шаблоны по умолчанию, расположенные на **лезвии компонов страниц** ы и потоков пользователей (ранее известных как встроенные политики), для создания пользовательского фирменного опыта проверки подлинности для пользователей.

Для получения дополнительной информации об использовании шаблонов см. [Azure AD B2C теперь имеет настройку JavaScript и многие другие новые функции.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)

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

- **Используйте файлы cookie HTTP-Only.** Устанавливает флаг **HTTPOnly** для файлов cookie доступа и сеанса на Application Proxy. Применение этого параметра обеспечивает дополнительные преимущества безопасности, например, помогает предотвратить копирование или изменение файлов cookie с помощью написания скриптов на стороне клиента. Рекомендуется включить этот флаг (выберите **Да**) для дополнительных преимуществ.

- **Используйте безопасное печенье.** Устанавливает флаг **Безопасный** для файлов cookie доступа и сеанса в прокси приложении. Применение этого параметра обеспечивает дополнительные преимущества безопасности, обеспечивая передачу файлов cookie только по безопасным каналам TLS, например HTTPS. Рекомендуется включить этот флаг (выберите **Да**) для дополнительных преимуществ.

- **Используйте стойкие файлы cookie.** Предотвращает файлы cookie доступа от истечения срока действия, когда веб-браузер закрыт. Эти файлы cookie действуют в течение времени существования маркера доступа. Тем не менее файлы cookie сбрасываются при достижении времени истечения срока действия, или если пользователь вручную удаляет файл cookie. Мы рекомендуем оставить по умолчанию параметр **Нет** и применять его только для более старых приложений, которые не отправляют файлы cookie между процессами.

Дополнительные сведения о новых файлах cookie см. в разделе [Параметры файлов cookie для доступа к локальным приложениям в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Новые федеративные приложения доступные в коллекции приложений Azure AD — январь 2019

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
В январе 2019 года мы добавили 35 новых приложений с поддержкой федерации в коллекцию приложений:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [Talent Palette](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco Umbrella](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Internet Access Administrator](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [Expiration Reminder](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR Viewer](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [Verb](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital Close](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [набор средств GoodPractice](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [облачная служба PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [Workable](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [GTNexus SSO System](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [ARES for Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 for Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [Visitly](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Korn Ferry ALP](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Новые улучшения защиты идентификации Azure AD (общедоступная предварительная версия)

**Тип.** Измененная функция.  
**Категория обслуживания:** Защита личных данных  
**Возможности продукта.** Безопасность и защита удостоверений.

Рады сообщить, что мы добавили следующие усовершенствования для общедоступной предварительной версии Защиты идентификации Azure AD, включая:

- обновленный и более интегрированный пользовательский интерфейс;

- Дополнительные API

- улучшенная оценка риска с помощью машинного обучения;

- выравнивание продукта для пользователей, представляющих риск и рискованные входы в систему.

Дополнительные сведения об усовершенствованиях см. статью [Что такое Защита идентификации Azure Active Directory (обновленная)?](https://aka.ms/IdentityProtectionDocs), чтобы узнать больше и поделиться своими мыслями с помощью запросов внутри продукта.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Новая функция блокировки приложения для приложения Microsoft Authenticator на устройствах iOS и Android

**Тип.** Новая функция.  
**Категория обслуживания:** Приложение Microsoft Authenticator  
**Возможности продукта.** Безопасность и защита удостоверений.

Чтобы обеспечить большую безопасность ваших одноразовых секретных кодов, информации о приложении и параметров приложения, вы можете включить функцию "Блокировка приложения" в приложении Microsoft Authenticator. Включение App Lock означает, что вам будет предложено проверить подлинность с помощью PIN-кода или биометрических при каждом открытии приложения Microsoft Authenticator.

Дополнительные сведения можно найти в статье [Часто задаваемые вопросы о приложении Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Улучшенные возможности экспорта Azure AD Privileged Identity Management

**Тип.** Новая функция.  
**Категория службы.** Управление привилегированными пользователями.  
**Возможности продукта.** Управление привилегированными пользователями.

Администраторы Azure AD Privileged Identity Management теперь могут экспортировать все активные и допустимые назначения ролей для определенного ресурса, включая назначения ролей для всех дочерних ресурсов. Ранее у администраторов возникали трудности при получении полного списка назначений ролей для подписки, и им нужно было экспортировать назначения ролей для каждого конкретного ресурса.

Дополнительные сведения см. в статье [Просмотр пользователей с ролями службы Azure в PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="novemberdecember-2018"></a>Ноябрь и декабрь 2018 г.

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Пользователи, удаленные из области синхронизации, больше не преобразуются в облачные учетные записи

**Тип.** Исправление.  
**Категория обслуживания:** Управление пользователями  
**Возможности продукта:** Каталог

>[!Important]
>Мы знаем и понимаем, как вы беспокоитесь на счет этого исправления. Поэтому мы откатили это изменение, пока не сможем упростить реализацию исправления в вашей организации.

Мы исправили ошибку, при которой флаг пользователя DirSyncEnabled ошибочно переключился бы на **False,** когда объект Active Directory Domain Services (AD DS) был исключен из сферы синхронизации, а затем перемещен в бин Recycle в Azure AD на следующем цикле синхронизации. В результате этого исправления, если пользователь исключается из области синхронизации, а затем восстанавливается из корзины Azure AD, учетная запись пользователя остается синхронизированной из локальной службы AD (как и ожидается) и ею нельзя управлять в облаке, так как ее главным источником (SoA) по-прежнему остается локальная служба AD.

До этого исправления возникала проблема, когда флаг DirSyncEnabled переключался в значение False. Из-за этого создавалось ошибочное впечатление, что такие учетные записи были преобразованы в облачные объекты, которыми можно управлять в облаке. При этом главным источником учетных записей по-прежнему оставалась локальная среда и все синхронизированные свойства (теневые атрибуты) поступали из локальной службы AD. Это условие вызывало несколько проблем в Azure AD и других облачных рабочих нагрузках (таких как Exchange Online), которые должны были обрабатывать эти учетные записи как синхронизированные из AD, хотя их поведение соответствовало облачным учетным записям.

В настоящее время единственным способом по-настоящему преобразовать учетную запись, синхронизированную из AD, в облачную учетную запись является отключение DirSync на уровне клиента, что запускает внутреннюю операцию по переносу главного источника (SoA). Такое изменение SoA требует (в том числе) очистки всех атрибутов, связанных с локальной средой (таких как LastDirSyncTime и теневые атрибуты), и отправки в другие облачные рабочие нагрузки сигнала о преобразовании соответствующего объекта в облачную учетную запись.

Соответственно, это исправление предотвращает прямое изменение атрибута ImmutableID пользователя, синхронизированного из AD, что требовалось в некоторых сценариях в прошлом. По сути атрибут ImmutableID объекта в Azure AD, как и предполагает название, должен быть неизменяемым. Для разрешения таких ситуаций доступны новые функции, реализованные в Azure AD Connect Health и клиенте службы синхронизации Azure AD Connect.

- **Поэтапное крупномасштабное изменение ImmutableID для нескольких пользователей**
  
  Например, вам необходимо выполнить длительную миграцию между лесами AD DS. Решение: Используйте Azure AD Connect для **настройки исходного якоря** и, по мере миграции пользователя, скопируйте существующие значения ImmutableID из Azure AD в атрибут нового леса пользователя AD DS ms-DS-Consistency-Guid. Дополнительные сведения см. в статье, посвященной [использованию ms-DS-ConsistencyGuid в качестве sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Крупномасштабное изменение ImmutableID для нескольких пользователей за один раз**

  Например, при реализации Azure AD Connect вы сделали ошибку и теперь необходимо изменить атрибут SourceAnchor. Решение: отключить DirSync на уровне арендатора и очистить все недействительные значения ImmutableID. Дополнительные сведения см. в статье, посвященной [отключению синхронизации каталогов для Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Повторное сопоставление локального пользователя с существующим пользователем в Azure AD**. Например, повторное создание пользователя AD DS приводит к появлению дубликата учетной записи Azure AD вместо его повторного сопоставления с существующей учетной записью Azure AD (потерянный объект). Решение: Используйте Azure AD Connect Health на портале Azure для перекарты исходного якоря/неизменяемого идола. Дополнительные сведения см. в статье [Сценарий с потерянным объектом](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Нарушение изменений: Обновления схемы аудита и входа в систему через Azure Monitor

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
|loggedByService|Указывает имя службы, создавшей журнала аудита.|NULL|<ul><li>"Account Provisioning" (Подготовка учетных записей).</li><li>"Core Directory" (Основной каталог);</li><li>Самостоятельный сброс пароля</li></ul>|
|Результат|Содержит результат журналов аудита. Ранее это значение перечислялось, но теперь отображается фактическое значение.|<ul><li>0</li><li>1</li></ul>|<ul><li>Успешно</li><li>Failure</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Измененные поля в схеме входа в систему
Следующие поля в схеме входа в систему будут изменены.

|Имя поля|Изменения|Прежние значения|Новые значения|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Ранее это поле называлось **conditionalaccessPolicies**. Теперь оно называется **conditionalaccessPolicies**.|Без изменения.|Без изменения.|
|conditionalAccessStatus|Содержит результат состояния политики условного доступа при входе в систему. Ранее это значение перечислялось, но теперь отображается фактическое значение.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Успешно</li><li>Failure</li><li>Неприменимо</li><li>Выключено</li></ul>|
|appliedConditionalAccessPolicies: result|Содержит результат состояния отдельной политики условного доступа при входе в систему. Ранее это значение перечислялось, но теперь отображается фактическое значение.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Успешно</li><li>Failure</li><li>Неприменимо</li><li>Выключено</li></ul>|

Дополнительные сведения о схеме см. в разделе [Интерпретация схемы журналов аудита Azure AD в Azure Monitor (предварительная версия)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema).

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Улучшения защиты идентификации для контролируемой модели машинного обучения и подсистемы оценки риска

**Тип.** Измененная функция.  
**Категория обслуживания:** Защита личных данных  
**Возможности продукта:** Оценки риска

Улучшения подсистемы оценки риска пользователя и риска при входе, связанные с защитой идентификации, могут повысить точность и охват при оценке риска пользователя. Администраторы могут заметить, что уровень риска пользователя больше не связан напрямую с уровнем риска определенных случаев обнаружения, а число и уровень рискованных событий входа в систему возросли.

Обнаружение рисков теперь оценивается контролируемой моделью машинного обучения, которая вычисляет риск пользователя с помощью дополнительных функций вводов пользователя и шаблона обнаружения. На основании этой модели администратор может найти пользователей с высоким уровнем риска, даже если уровень риска для случаев обнаружения, связанных с этими пользователями, не превышает низкий или средний уровень. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Администраторы могут сбрасывать свой пароль с помощью приложения Microsoft Authenticator (предварительная версия)

**Тип.** Измененная функция.  
**Категория службы.** Самостоятельный сброс пароля.  
**Возможности продукта:** Проверка подлинности пользователей

Администраторы Azure AD теперь могут сбросить свой пароль с помощью уведомлений приложения Microsoft Authenticator, кода из любого мобильного приложения Authenticator или аппаратного токена. Чтобы сбросить свой пароль, администраторы теперь могут использовать два из следующих методов:

- уведомление приложения Microsoft Authenticator;

- другое мобильное приложение Authenticator или код аппаратного токена;

- Email

- Телефонный звонок

- Текстовое сообщение

Дополнительные сведения об использовании приложения Microsoft Authenticator для сброса паролей см. в разделе [Мобильное приложение и SSPR (предварительная версия)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr).

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Новая роль администратора облачных устройств в Azure AD (предварительная версия)

**Тип.** Новая функция.  
**Категория обслуживания:** Регистрация и управление устройствами  
**Возможности продукта:** Контроль доступа

Администраторы могут назначать пользователям новую роль "Администратор облачных устройств" для выполнения операций администрирования облачных устройств. Пользователи с этой ролью могут включать, отключать и удалять устройства в Azure AD, а также считывать ключи BitLocker в Windows 10 (при наличии) на портале Azure.

Дополнительные сведения о ролях и разрешениях см. в статье [Назначение ролей администратора в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Управление устройствами с помощью новой метки времени действия в Azure AD (предварительная версия)

**Тип.** Новая функция.  
**Категория обслуживания:** Регистрация и управление устройствами  
**Возможности продукта:** Управление жизненным циклом устройства

Мы понимаем, что со временем необходимо обновить и удалить устройства своих организаций в Azure AD, чтобы избежать устаревших устройств в вашей среде. Чтобы упростить этот процесс, теперь служба Azure AD обновляет устройства с помощью новой метки времени действия, помогая управлять жизненным циклом устройств.

Для получения дополнительной информации о том, как получить и использовать эту метку времени, [см.](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Администраторы могут требовать от пользователей принимать условия использования на каждом устройстве

**Тип.** Новая функция.  
**Категория обслуживания:** Условия использования  
**Возможности продукта.** Система управления.
 
Администраторы теперь могут включить **требование к пользователям, чтобы дать согласие на каждый** вариант устройства, чтобы требовать от пользователей принять ваши условия использования на каждом устройстве, которое они используют на вашем арендаторе.

Для получения дополнительной [Per-device terms of use section of the Azure Active Directory terms of use feature](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use)информации см.

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Администраторы могут настроить сроки использования, срок действия которых истекает, на основе повторяющегося графика

**Тип.** Новая функция.  
**Категория обслуживания:** Условия использования  
**Возможности продукта.** Система управления.
 

Теперь администраторы могут включить опцию **согласия expire,** чтобы условия использования истекали для всех пользователей на основе указанного вами расписания. Расписание может повторяться один раз в год, два раза в год, ежеквартально или ежемесячно. После истечения срока действия условий использования пользователи должны повторно принять их.

Для получения дополнительной информации смотрите [раздел «Добавить термины использования» функции «Активный каталог» Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use)

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Администраторы могут настроить сроки использования, срок действия которых истекает, в зависимости от расписания каждого пользователя

**Тип.** Новая функция.  
**Категория обслуживания:** Условия использования  
**Возможности продукта.** Система управления.

Администраторы теперь могут указать продолжительность, что пользователь должен повторно принять условия использования. Например, администраторы могут указать, что пользователи должны повторно принимать условия использования каждые 90 дней.

Для получения дополнительной информации смотрите [раздел «Добавить термины использования» функции «Активный каталог» Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use)
 
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
**Возможности продукта:** Каталог

Период предоставления общедоступной предварительной версии группового лицензирования завершен, и теперь выпущена общедоступная версия этой функции. В рамках данного общедоступного выпуска мы сделали эту функцию более масштабируемой. Кроме того, мы добавили возможность повторной обработки назначений группового лицензирования для отдельного пользователя и возможность использовать групповое лицензирование с лицензиями E3 или A3 в Office 365.

Дополнительные сведения о групповом лицензировании см. в разделе [Что такое лицензии групп в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>В коллекции приложений Azure AD доступны новые федеративные приложения (ноябрь 2018 г.)

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
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
**Категория обслуживания:** Корпоративные приложения  
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
**Возможности продукта:** Каталог

Мы рады сообщить, что теперь с помощью API управления доменами ForceDelete можно удалить имя личного домена и асинхронно изменить такое имя (contoso.com) в ссылках на пользователей, группы и приложения на имя домена по умолчанию (contoso.onmicrosoft.com).

Такое изменение позволяет быстрее удалить имя личного домена, которое ваша организация больше не использует или намерена перенести в другую службу Azure AD.

Дополнительные сведения см. в статье [Удаление имени личного домена](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>Сентябрь 2018 г.
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Обновление разрешений роли администратора для динамических групп

**Тип.** Исправление.  
**Категория службы.** Управление группами.  
**Возможности продукта.** Совместная работа.

Мы исправили проблему, которая не позволяла некоторым ролям администратора создавать и обновлять правила динамического членства, не являясь владельцем группы.

Вот эти роли:

- Глобальный администратор.

- Администратор Intune

- Администратор пользователей

Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Упрощенные параметры конфигурации единого входа (SSO) для некоторых сторонних приложений

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Единый вход.

Мы понимаем, что настройка единого входа (SSO) для приложений SaaS (программное обеспечение как услуга) может создавать трудности из-за уникальных настроек для каждого приложения. Мы создали упрощенную процедуру настройки, при которой автоматически указываются параметры единого входа для следующих приложений SaaS сторонних разработчиков:

- Zendesk

- ArcGis Online;

- Jamf Pro.

Чтобы начать использовать этот опыт одним щелчком мыши, перейдите на страницу > **конфигурации SSO** **портала Azure**для приложения. Дополнительные сведения есть в статье [Интеграция приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list).

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

Новая вкладка **«Устранение и поддержка проблем»** на странице **ввоза** в систему Azure предназначена для помощи админомам и инженерам поддержки в устранении неполадок, связанных со встыками ВС AD Azure. Эта новая вкладка предоставляет код ошибки, сообщение об ошибке и рекомендации по исправлению положения (если таковые имеется), чтобы помочь решить проблему. Если с проблемой не удастся справиться, здесь же можно создать запрос в службу поддержки с помощью новой функции **копирования в буфер обмена**, с помощью которой для файла журнала заполняются поля **Идентификатор запроса** и **Дата (UTC)** в запросе в службу поддержки.  

![Войти в журналы, показывающие новую вкладку](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Улучшенная поддержка пользовательских свойств расширения, позволяющих создать правила динамического членства

**Тип.** Измененная функция.  
**Категория службы.** Управление группами.  
**Возможности продукта.** Совместная работа.

После этого обновления вы сможете при создании динамического правила членства перейти по ссылке **Get custom extension properties** (Получить пользовательские свойства расширения) из конструктора динамических правил для пользовательских групп, ввести уникальный идентификатор приложения и получить полный список пользовательских свойств расширения. Этот список можно обновить, чтобы просмотреть новые пользовательские свойства расширения для выбранного приложения.

Дополнительные сведения об использовании пользовательских свойств расширения в правилах динамического членства см. в разделе [Свойства расширения и пользовательские свойства расширения](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Новые утвержденные клиентские приложения для приложения Azure AD на основе условного доступа

**Тип.** Планирование изменений.  
**Категория обслуживания:** Условный доступ  
**Возможности продукта.** Безопасность и защита удостоверений.

Следующие приложения добавлены в список [утвержденных клиентских приложений](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview):

- Microsoft To-Do

- Microsoft Stream;

Дополнительные сведения см. в разделе:

- [Условный доступ к приложению Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Добавлена поддержка самостоятельного сброса пароля с экрана блокировки Windows 7, 8 и 8.1

**Тип.** Новая функция.  
**Категория обслуживания:** SSPR  
**Возможности продукта:** Проверка подлинности пользователей

Когда вы настроите эту новую функцию, для пользователей отобразится ссылка для сброса пароля на экране **блокировки** любого устройства под управлением Windows 7, Windows 8 или Windows 8.1. Щелкнув эту ссылку, пользователь пройдет тот же процесс сброса пароля, что и в веб-браузере.

Дополнительные сведения см. в статье [How to: Enable password reset from Windows 7, 8, and 8.1](https://aka.ms/ssprforwindows78) (Практическое руководство. Включение функции сброса пароля из Windows 7, 8 и 8.1)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Уведомление об изменении: коды авторизации больше не будут доступны для повторного использования 

**Тип.** Планирование изменений.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Проверка подлинности пользователей

С 15 ноября 2018 г. в Azure AD прекращается поддержка использованных кодов аутентификации для приложений. Это изменение системы безопасности помогает Azure AD соответствовать спецификации OAuth и будет применяться для конечных точек версии 1 и 2.

Если ваше приложение повторно использует коды проверки подлинности для получения маркеров для нескольких ресурсов, мы рекомендуем использовать код для получения маркера обновления, а затем использовать этот маркер для получения дополнительных маркеров для других ресурсов. Коды проверки подлинности можно использовать только один раз, однако маркеры обновления можно использовать несколько раз для нескольких ресурсов. У приложения, которое пытается повторно использовать код аутентификации в потоке кода OAuth, возникнет ошибка invalid_grant.

Это и другие изменения в работе с протоколами описаны на странице [с полным списком новых возможностей для аутентификации](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>В коллекции приложений Azure AD доступны новые федеративные приложения (сентябрь 2018 г.)

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
В сентябре 2018 года мы добавили в коллекцию приложений следующие 16 новых приложений с поддержкой федерации:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [Comeet Recruiting Software](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [JDA Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), join.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass, [Rackspace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft SSO for Azure, SurveyMonkey, [Convene](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial).

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://aka.ms/azureadapprequest).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Поддержка дополнительных методов преобразования утверждений

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Единый вход.

Мы добавили новые методы преобразования утверждений — ToLower() и ToUpper(), — которые можно применять для SAML-маркеров на странице **настройки единого входа** на основе SAML.

Дополнительные сведения см. в статье [Настройка утверждений, выпущенных в токене SAML для корпоративных приложений в AAD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Обновленный пользовательский интерфейс для настройки приложений на основе SAML (предварительная версия)

**Тип.** Измененная функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Единый вход.

В обновленном пользовательском интерфейсе для настройки приложений на основе SAML вы получите следующие возможности:

- новый интерфейс пошаговой настройки приложений на основе SAML;

- больше информации о том, чего не хватает в конфигурации и что настроено неправильно;

- возможность добавлять несколько адресов электронной почты для уведомлений об истечении срока действия сертификата;

- новые методы ToLower() и ToUpper() и другие изменения в механизме преобразования утверждений;

- возможность передачи собственного сертификата для подписи маркера в корпоративных приложениях;

- возможность настроить формат NameID для приложений SAML и указать расширение каталога по идентификатору имени.

Чтобы включить обновленное представление, щелкните ссылку **Попробуйте новый интерфейс** в верхней части страницы **Единый вход**. Дополнительные сведения см. в [руководстве по настройке единого входа на основе SAML для приложения в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications).

---

## <a name="august-2018"></a>Август 2018 г.

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
**Возможности продукта:** Проверка подлинности пользователей

С 15 ноября 2018 г. в Azure AD прекращается поддержка использованных кодов аутентификации для приложений. Это изменение системы безопасности помогает Azure AD соответствовать спецификации OAuth и будет применяться для конечных точек версии 1 и 2.

Если ваше приложение повторно использует коды проверки подлинности для получения маркеров для нескольких ресурсов, мы рекомендуем использовать код для получения маркера обновления, а затем использовать этот маркер для получения дополнительных маркеров для других ресурсов. Коды проверки подлинности можно использовать только один раз, однако маркеры обновления можно использовать несколько раз для нескольких ресурсов. У приложения, которое пытается повторно использовать код аутентификации в потоке кода OAuth, возникнет ошибка invalid_grant.

Это и другие изменения в работе с протоколами описаны на странице [с полным списком новых возможностей для аутентификации](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Конвергентное управление сведениями для защиты для самостоятельного сброса пароля (SSPR) и Многофакторной идентификации (MFA)

**Тип.** Новая функция.  
**Категория обслуживания:** SSPR  
**Возможности продукта:** Проверка подлинности пользователей
 
Эта новая функция позволяет пользователям управлять своими сведениями для защиты (например, номером телефона, мобильным приложением и т. д.) для SSPR и MFA в одном расположении и интерфейсе (ранее все выполнялось в двух разных расположениях).

Этот конвергентный интерфейс доступен для пользователей, использующих либо SSPR, либо MFA. Кроме того, если организация не требует регистрацию MFA или SSPR в принудительном порядке, пользователи все равно могут зарегистрировать в сведениях для защиты разрешенные организацией методы MFA или SSPR через портал My Apps.

Это общедоступная предварительная версия. Администраторы могут включить новую функцию (при необходимости) для выбранной группы или всех пользователей в клиенте. Дополнительные сведения о конвергентном интерфейсе см. в [этой записи блога](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/).

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Новый параметр "HTTP-Only cookies" в приложениях Azure AD Application Proxy

**Тип.** Новая функция.  
**Категория службы.** Прокси-сервер приложения.  
**Возможность продукта.** Контроль доступа.

В приложениях Application Proxy доступен новый параметр под названием **HTTP-Only Cookies** (Только файлы cookie HTTP). Он позволяет обеспечить дополнительную безопасность, включив флаг HTTPOnly в заголовок ответа HTTP для файлов cookie сеанса и доступа к Application Proxy, запрещая доступ к файлу cookie с клиентского скрипта и предотвращая такие действия, как копирование или изменение файла cookie. Хотя этот флаг не использовался ранее, ваши файлы cookie всегда шифровались и передавались с помощью подключения TLS для защиты от неправильных изменений.

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
**Категория обслуживания:** Корпоративные приложения  
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
- Azure AD Connect Health 
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

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Информация об условном доступе, добавленная в отчет о входах Azure AD

**Тип.** Новая функция.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Безопасность и защита удостоверений.
 
Это обновление позволяет увидеть, оценка каких политик выполняется, когда пользователь входит в систему, а также результат политики. Кроме того, в отчет теперь входит тип клиентского приложения, используемого пользователем, поэтому вы можете идентифицировать трафик устаревшего протокола. В записях отчетов теперь также можно выполнить поиск по идентификатору корреляции, который можно найти в сообщении об ошибке пользователя и использовать для идентификации и устранения неполадок соответствующего запроса на вход.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Просмотр устаревших способов аутентификации через журналы действий входа

**Тип.** Новая функция.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.
 
С появлением поля **Клиентское приложение** в журналах действий входа клиенты теперь могут видеть, какие пользователи применяют устаревшие способы аутентификации. Клиенты смогут получить доступ к этой информации с помощью Входа в Microsoft Graph API или через журналы активности входа на портале Azure AD, где вы можете использовать управление **приложения клиента** для фильтрации устаревших аутентификаций. Дополнительные сведения см. в документации.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Новые федеративные приложения, доступные в коллекции приложений Azure AD (июль 2018 г.)

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
В июле 2018 года мы добавили 16 новых приложений с поддержкой федерации в коллекцию приложений:

[Innovation Hub](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [Certain Admin SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), PSUC Staging, [iPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [Screencast-O-Matic](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial), PowerSchool Unified Classroom, [Eli Onboarding](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [Bomgar Remote Support](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [Nimblex](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [Imagineer WebVision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial), [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [SecureW2 JoinNow Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](../saas-apps/kanbanize-tutorial.md), [SmartLPA](../saas-apps/smartlpa-tutorial.md), [Skills Base](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial).

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
**Категория обслуживания:** Azure AD  
**Возможности продукта:** Проверка подлинности пользователей

Мы обновили пользовательский интерфейс для входа в веб-службы Майкрософт, такие как Office 365 и Azure. Благодаря этому изменению экраны стали менее загроможденными и более понятными. Дополнительные сведения об этом изменении см. в записи блога, посвященной [предстоящим улучшениям интерфейса входа Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/).

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Новый выпуск Azure AD Connect — июль 2018 г.

**Тип.** Измененная функция.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта:** Управление жизненным циклом идентичности

Последний выпуск Azure AD Connect включает в себя: 

- исправления ошибок и обновления функций поддержки; 

- общедоступную версию интеграции между Ping и Federate;

- обновления для последней версии клиента SQL 2012. 

Дополнительные сведения об этом обновлении см. в статье [Azure AD Connect. История выпусков версий](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history).

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Обновления условий использования пользовательского интерфейса конечного пользователя

**Тип.** Измененная функция.  
**Категория обслуживания:** Условия использования  
**Возможности продукта.** Система управления.

В пользовательском интерфейсе с условиями использования мы обновляем строку принятия.

**Текущий текст.** "Чтобы получить доступ к ресурсам [имя_клиента], вы должны принять условия использования".<br>**Новый текст.** "Чтобы получить доступ к ресурсам [имя_клиента], вы должны прочитать условия использования".

**Текущий текст.** "Выбирая параметр "Принять", вы подтверждаете, что соглашаетесь со всеми вышеупомянутыми условиями использования".<br>**Новый текст.** "Нажмите кнопку "Принять", чтобы подтвердить, что вы прочитали и поняли условия использования".

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Сквозная проверка подлинности поддерживает устаревшие протоколы и приложения

**Тип.** Измененная функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Проверка подлинности пользователей
 
Сквозная проверка подлинности теперь поддерживает устаревшие протоколы и приложения. Теперь полностью поддерживаются следующие ограничения:

- Входы пользователей в устаревшие клиентские приложения Office (Office 2010 и Office 2013) без необходимости применения современной проверки подлинности.

- Общий доступ к календарю и получение сведений о доступности в гибридных средах Exchange поддерживается только для Office 2010.

- Входы пользователей в клиентские приложения Skype для бизнеса без необходимости применения современной проверки подлинности.

- Вход пользователей в PowerShell версии 1.0.

- Программа регистрации устройств Apple (Apple DEP) с помощью помощника по настройке iOS. 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Управление конвергированными сведениями для защиты для самостоятельного сброса пароля и Многофакторной идентификации

**Тип.** Новая функция.  
**Категория обслуживания:** SSPR  
**Возможности продукта:** Проверка подлинности пользователей

Эта новая функция позволяет пользователям управлять своими сведениями для защиты (например, номером телефона, адресом электронной почты, мобильным приложением и т. д.) для самостоятельного сброса пароля (SSPR) и Многофакторной идентификации (MFA) в одном интерфейсе. Пользователям больше не нужно регистрировать одни и те же сведения для защиты для SSPR и MFA в двух разных интерфейсах. Этот новый интерфейс также применяется к пользователям с SSPR или MFA.

Если организация не применяет регистрацию MFA или SSPR, пользователи могут зарегистрировать свои сведения для защиты через портал **My Apps**. Оттуда они могут зарегистрировать любые методы, разрешенные для MFA или SSPR. 

Это общедоступная предварительная версия. Администраторы могут включить новую функцию (при необходимости) для выбранной группы пользователей или всех пользователей в клиенте.

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Использование приложения Microsoft Authenticator для проверки идентификации при сбросе пароля

**Тип.** Измененная функция.  
**Категория обслуживания:** SSPR  
**Возможности продукта:** Проверка подлинности пользователей

Эта функция позволяет пользователям, не являющимся администраторами, подтвердить свою личность при сбросе пароля с помощью уведомления или кода от приложения Microsoft Authenticator (или любого другого приложения проверки подлинности). Когда администратор включит этот метод самостоятельного сброса пароля, пользователи, зарегистрировавшие мобильное приложение через aka.ms/mfasetup или aka.ms/setupsecurityinfo, смогут использовать свое мобильное приложение как метод проверки при сбросе пароля.

Уведомление от мобильного приложения можно включить только как часть политики, которая требует двух методов для сброса пароля.

---

## <a name="june-2018"></a>Июнь 2018 г.

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Уведомление об изменении: исправление безопасности для потока делегированной авторизации для приложений, использующих API журналов действий Azure AD

**Тип.** Планирование изменений.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.

Из-за усиления безопасности нам пришлось внести изменения в разрешения для приложений, которые используют делегированный поток авторизации для доступа к [AI-приложениям активности Azure AD.](https://aka.ms/aadreportsapi) Это изменение вступило в силу **26 июня 2018 г**.

Если какое-либо из ваших приложений использует AAP-журнал активности Azure AD, выполните следующие действия, чтобы убедиться, что приложение не сломается после изменения.

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

    1. Открытый Internet Explorer, выберите **Инструменты** > **Интернет Варианты** > **Расширенный**.
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
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
В июне 2018 года мы добавили эти 15 новых приложений с поддержкой федерации в коллекцию приложений:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), [Settling music](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), [бизнес-приложение с включенным токеном SAML 1.1](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [Supermood](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), [Autotask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Endpoint Backup](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Skyhigh Networks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho One](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [Локальная среда SharePoint](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [ForeSee CX Suite](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial). Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Защита паролей Azure AD доступна в общедоступной предварительной версии

**Тип.** Новая функция.  
**Категория обслуживания:** Защита личных данных  
**Возможности продукта:** Проверка подлинности пользователей

Защиту паролей Azure AD можно использовать, чтобы исключить легко угадываемые пароли из вашей среды. Исключение этих паролей помогает снизить риск компрометации в случае атаки с подбором пароля.

В частности защита паролей Azure AD помогает вам:

- защитить учетные записи вашей организации как в Azure AD, так и в Windows Server Active Directory (AD); 
- предотвратить использование пользователями паролей из списка 500 наиболее часто используемых паролей и более 1 миллиона вариантов замены этих паролей;
- управлять защитой паролей Azure AD из одного расположения на портале Azure AD для Azure AD и локального экземпляра Windows Server AD.

Дополнительные сведения о защите паролей Azure AD см. в статье [Исключение неправильных паролей в организации](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Новый шаблон политики условного доступа "все гости", созданный в периоды создания использования

**Тип.** Новая функция.  
**Категория обслуживания:** Условия использования  
**Возможности продукта.** Система управления.

При создании условий использования создается новый шаблон политики условного доступа для «всех гостей» и «всех приложений». Этот новый шаблон политики применяет только что созданные условия использования, упрощая создание и применение процесса для гостей.

Дополнительные сведения см. в статье [Функция "Условия использования Azure Active Directory" (предварительная версия)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Новый шаблон политики условного доступа, созданный в период создания терминов использования

**Тип.** Новая функция.  
**Категория обслуживания:** Условия использования  
**Возможности продукта.** Система управления.

При создании условий использования также создается новый шаблон политики условного доступа "обычный". Этот новый шаблон политики позволяет создавать ToU, а затем сразу же перейти к лезвию создания политики условного доступа, без необходимости вручную перемещаться по порталу.

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
**Категория обслуживания:** Корпоративные приложения  
**Возможность продукта.** Контроль доступа.

Теперь администратор может делегировать задачи управления приложением без присвоения роли глобального администратора. Новые роли и возможности:

- **Новые стандартные роли администратора Azure AD:**

    - **Администратор приложения.** Предоставляет возможность управлять всеми аспектами всех приложений, в том числе регистрацией, параметрами единого входа, назначением приложений и лицензированием, параметрами прокси-сервера приложений и согласием (за исключением ресурсов Azure AD).

    - **Администратор облачных приложений.** Предоставляет все возможности администратора приложений, за исключением управления прокси-сервером приложений, так как он не предоставляет локальный доступ.

    - **Разработчик приложений.** Предоставляет возможность создавать регистрации приложений, даже если отключен параметр, **позволяющий пользователям регистрировать приложения**.

- **Владение (настраивается для регистрации отдельного приложения и для приложения отдельного предприятия, аналогично процессу группового владения:**
 
    - **Владелец регистрации приложения.** Предоставляет возможность управлять всеми аспектами имеющейся регистрации приложения, включая манифест приложения и добавление дополнительных владельцев.

    - **Владелец корпоративного приложения.** Предоставляет возможность управлять многими аспектами собственных корпоративных приложений, в том числе параметрами единого входа, назначением приложений и согласием (за исключением ресурсов Azure AD).

Дополнительные сведения об общедоступной предварительной версии см. в записи блога [Hallelujah! Azure AD delegated application management roles are in public preview!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) (Делегированные роли управления приложением Azure AD в общедоступной предварительной версии) . Для получения дополнительной информации о ролях и разрешениях смотрите [назначение ролей администратора в Active Directory Azure.](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)

---

## <a name="may-2018"></a>Май 2018 г.

### <a name="expressroute-support-changes"></a>Изменения в поддержке ExpressRoute

**Тип.** Планирование изменений.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Платформа.  

Предложения модели "программное обеспечение как услуга", такие как Azure Active Directory (Azure AD), лучше всего работают напрямую через Интернет, не требуя ExpressRoute или любых других частных VPN-туннелей. По этой причине с **1 августа 2018** мы прекращаем поддержку ExpressRoute для служб Azure AD с помощью общедоступного пиринга Azure и сообществ Azure в пиринге корпорации Майкрософт. Во всех службах, на которые повлияет это изменение, трафик Azure AD будет постепенно переходить от ExpressRoute к Интернету.

Мы изменяем способ предоставления поддержки, но понимаем, что в некоторых ситуациях вам может понадобиться выделенный набор каналов для трафика проверки подлинности. Поэтому в Azure AD будут и дальше поддерживаться ограничения диапазона IP-адресов каждого клиента с использованием ExpressRoute и служб, в которых уже используется пиринг Майкрософт, в сообществе "Другие онлайн-службы Office 365" Если затронуты ваши службы, но вам требуется ExpressRoute, необходимо сделать следующее:

- **Если вы используете общедоступный пиринг Azure.** Необходимо перейти к пирингу Майкрософт и зарегистрироваться в сообществе **Другие онлайн-службы Office 365 (12076:5100)**. Дополнительные сведения см. в статье [Переход с общедоступного пиринга на пиринг Майкрософт](https://docs.microsoft.com/azure/expressroute/how-to-move-peering).

- **Если вы используете пиринг Майкрософт.** Зарегистрируйтесь в сообществе **Другие онлайн-службы Office 365 (12076:5100)**. Дополнительные сведения о требованиях к маршрутизации см. в разделе [Поддержка сообществ BGP](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) статьи о требованиях к маршрутизации ExpressRoute.

Если вам необходимо продолжить использование выделенных каналов, потребуется поговорить со службой технической поддержки учетных записей Майкрософт о том, как получить разрешение на использование сообщества**Другие онлайн-службы Office 365 (12076:5100)**. Контрольный совет MS Office проверит, нужны ли вам эти каналы, и убедится, что вы понимаете технические последствия их использования. Несанкционированные подписки, пытающиеся создать фильтры маршрутов для Office 365, получат сообщение об ошибке. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>API Microsoft Graph для администрирования условий использования

**Тип.** Новая функция.  
**Категория обслуживания:** Условия использования  
**Возможности продукта.** Возможности для разработчиков
 
Мы добавили AIS Microsoft Graph для администрирования терминов использования Azure AD. Вы можете создавать, обновлять, удалять условия объекта использования.

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

- [cornerstone ondemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Список всех приложений, поддерживающих подготовку пользователей в [https://aka.ms/appstutorial](https://aka.ms/appstutorial)галерее Azure AD, можно узнать:

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Функция проверки доступа Azure AD для групп и приложений теперь поддерживает повторяющиеся проверки

**Тип.** Новая функция.  
**Категория обслуживания:** Обзоры доступа  
**Возможности продукта.** Система управления.
 
Функция проверки доступа для групп и приложений стала общедоступной в Azure AD Premium P2.  Администраторы смогут настраивать автоматическое повторение операций проверки доступа для членств в группах и назначений приложений с регулярным интервалом, например ежемесячно или ежеквартально.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Журналы действий Azure AD (вход и аудит) теперь доступны через MS Graph

**Тип.** Новая функция.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.
 
В настоящее время журналы деятельности Azure AD, включающий журналы входа и аудита, теперь доступны через API Microsoft Graph. Мы обнажили две конечные точки через API Microsoft Graph для доступа к этим журналам. Ознакомьтесь с [документацией](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) по программному доступу к API отчетов Azure AD. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Улучшения процедуры активации B2B и возможности покинуть организацию

**Тип.** Новая функция.  
**Категория службы.** B2B.  
**Возможности продукта.** B2B и B2C.

**Как раз вовремя искупление:** После того, как вы поделитесь ресурсом с гостем, использующим B2B API , вам не нужно отправлять специальное письмо-приглашение. В большинстве случаев гостевой пользователь может обратиться к ресурсу, после чего выполняется оперативная процедура активации. Больше никаких недоразумений из-за пропущенных сообщений электронной почты. Нет больше спрашивать ваших гостевых пользователей "Вы нажали на эту ссылку выкупа система послала вас?". Это означает, что когда SPO использует диспетчер приглашений, облачные вложения могут иметь одинаковый канонический URL-адрес для всех пользователей — как внутренних, так и внешних — в любом состоянии активации.

**Современная процедура активации.** Больше никакой целевой страницы активации с разделением экрана. Пользователи увидят современный согласованный интерфейс с заявлением о конфиденциальности приглашающей организации, как это происходит для приложений сторонних разработчиков.

**Гость пользователи могут покинуть организацию:** После того, как отношения пользователя с оргойской организацией закончены, он может самостоятельно покинуть организацию. Нет больше вызова приглашения орг е-аммин "быть удалены", не более повышения поддержки билетов.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Новые федеративные приложения, доступные в коллекции приложений Azure AD — май 2018 г.

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
В мае 2018 года мы добавили 18 новых приложений с поддержкой федерации в нашу коллекцию приложений:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix Data3Sixty Govern, [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [Jamf Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [Envi MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe Captivate Prime](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [Montage Online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial), OpenReel, Arc Publishing - [SSO](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [Riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [Flock](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [Reviewsnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial) [まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial)

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial).

Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Новое пошаговое руководство по развертыванию для Azure Active Directory

**Тип.** Новая функция.  
**Категория службы.** Другая.  
**Возможности продукта:** Каталог
 
Новое, пошаговым руководством о том, как развернуть Активный каталог Azure (Azure AD), включая сбросить пароли самообслуживания (SSPR), единый вход (SSO), Условный доступ (CA), прокси-приложение, предоставление пользовательских запросов, службы федерации активного каталога (ADFS) для сквозной аутентификации (PTA) и ADFS для синхронизации хэша паролей (PHS).

Чтобы просмотреть руководство по развертыванию, перейдите к репозиторию [Identity Deployment Guides](https://aka.ms/DeploymentPlans) в GitHub. Чтобы оставить отзыв о руководстве по развертыванию, используйте [форму обратной связи плана развертывания](https://aka.ms/deploymentplanfeedback). Если у вас возникли вопросы о руководствах по развертыванию, свяжитесь с нами на сайте [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Поиск корпоративных приложений — загрузка дополнительных приложений

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Единый вход.
 
У вас возникают трудности с поиском приложений или субъектов-служб? Теперь вы можете загружать больше приложений в списке всех корпоративных приложений. По умолчанию отображается 20 приложений. Теперь вы можете нажать, **загрузить больше** для просмотра дополнительных приложений. 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>Майский выпуск AADConnect содержит общедоступную предварительную версию функции интеграции с PingFederate, важные обновления для системы безопасности, множество исправлений ошибок и новые средства для устранения неполадок. 

**Тип.** Измененная функция.  
**Категория службы.** AD Connect.  
**Возможности продукта:** Управление жизненным циклом идентичности
 
Майский выпуск AADConnect содержит общедоступную предварительную версию функции интеграции с PingFederate, важные обновления для системы безопасности, множество исправлений ошибок и новые средства для устранения неполадок. См. [заметки о выпуске](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Проверки доступа Azure AD: автоматическое применение

**Тип.** Измененная функция.  
**Категория обслуживания:** Обзоры доступа  
**Возможности продукта.** Система управления.

Функция проверки доступа для групп и приложений стала общедоступной в Azure AD Premium P2. Администратор может автоматически применить изменения рецензента к соответствующей группе или соответствующему приложению после завершения проверки доступа. Кроме того, он может указать, что происходит с доступом пользователя, если рецензенты не ответили, а именно: запрет доступа, сохранение доступа или следование рекомендациям системы. 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>Маркеры идентификации теперь невозможно возвращать с помощью режима ответа query для новых приложений. 

**Тип.** Измененная функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Проверка подлинности пользователей
 
Приложения, созданные 25 апреля 2018 года или позднее, больше не могут запрашивать значение для **id_token** с использованием режима ответа **query**.  Это обеспечивает соответствие Azure AD спецификациям OIDC и помогает снизить уязвимость ваших приложений перед атаками.  Приложения, созданные до 25 апреля 2018 года, могут использовать режим ответа **query** с типом запроса **id_token**.  Возвращается ошибка при запросе id_token от AAD, является **AADSTS70007: 'запрос' не является поддерживаемым значением "response_mode" при запросе маркера**.

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
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Единый вход.

При настройке приложений с функцией единого входа на основе SAML вы можете протестировать интеграцию на странице конфигурации. Если в процессе входа возникнет ошибка, ее можно указать в интерфейсе тестирования, и Azure AD предоставит вам пошаговые инструкции по решению конкретной проблемы.

Дополнительные сведения см. в разделе:

- [Настройка единого входа для приложений, которых нет в коллекции приложений Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Отладка единого входа на основе SAML в приложения в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Условия использования Azure AD теперь есть на отчетность пользователя

**Тип.** Новая функция.  
**Категория обслуживания:** Условия использования  
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
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Единый вход.

На странице приложений Enterprise администраторы могут отправить файл метаданных SAML для настройки входа на основе SAML для приложений из коллекции AAD и не входящих в нее.

Кроме того, URL-адрес метаданных федерации приложения Azure AD можно использовать для настройки единого входа в целевом приложении.

Дополнительные сведения см. в статье [Настройка единого входа для приложений, которых нет в коллекции приложений Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Выпущена общедоступная версия функции "Условия использования"

**Тип.** Новая функция.  
**Категория обслуживания:** Условия использования  
**Возможности продукта.** Соответствие требованиям.
 

Условия использования Azure AD перешли от общедоступного предварительного просмотра к общедоступному.

Дополнительные сведения см. в статье [Функция "Условия использования Azure Active Directory"](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Предоставление или отзыв приглашений пользователям B2B из отдельных организаций

**Тип.** Новая функция.  
**Категория службы.** B2B.  
**Возможности продукта.** B2B и B2C.
 

Теперь можно указать, с какими партнерскими организациями вы хотите совместно использовать данные и совместно работать в службе совместной работы Azure AD B2B. Для этого можно создать список определенных разрешенных или запрещенных доменов. При блокировке домена с помощью этих возможностей сотрудники больше не смогут отправлять приглашения пользователям из этого домена.

Это помогает контролировать доступ к ресурсам, обеспечивая при этом удобную работу для утвержденных пользователей.

Эта функция B2B Collaboration доступна для всех клиентов Azure Active Directory и может использоваться в сочетании с функциями Azure AD Premium, такими как Conditional Access и identity protection для более детального контроля того, когда и как внешние бизнес-пользователи входят в систему и получают доступ.

Дополнительные сведения см. в статье [Предоставление или отзыв приглашений пользователям B2B из отдельных организаций](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Новые федеративные приложения доступны в коллекции приложений Azure AD

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
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

Для получения дополнительной информации см. [пользователи Grant B2B в Azure AD-доступе к вашим корпоративным приложениям.](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises)

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

Дополнительные сведения см. в разделе [Что происходит при подготовке](/azure//active-directory/app-provisioning/how-provisioning-works).

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Самостоятельный сброс пароля с экрана блокировки Windows 10 для компьютеров, присоединенных к гибридной среде Azure AD

**Тип.** Измененная функция.  
**Категория службы.** Самостоятельный сброс пароля.  
**Возможности продукта:** Проверка подлинности пользователей
 
Мы обновили компонент Windows 10 SSPR, добавив поддержку компьютеров, присоединенных к гибридной среде Azure AD. Эта функция доступна в Windows 10 RS4 и дает пользователям возможность сбрасывать пароль с экрана блокировки на компьютере с Windows 10. Эту функцию могут применять пользователи, которые включены и зарегистрированы для самостоятельного сброса пароля.

Дополнительные сведения см. в статье [Сброс пароля Azure AD на экране входа](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows).

---

## <a name="march-2018"></a>Март 2018 г.
 
### <a name="certificate-expire-notification"></a>Уведомление об истечении срока действия сертификата

**Тип.** Исправление.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Единый вход.
 
Azure AD отправляет уведомление о сертификатах с истекающим сроком действия для приложений из коллекции или не из коллекции. 

Некоторые пользователи не получают уведомления о корпоративных приложениях, для которых настроен единый вход для на основе SAML. Эта проблема была устранена. Azure AD отправляет уведомление для сертификатов, срок действия которых истекает через 7, 30 и 60 дней. Это событие отображается в журналах аудита. 

Дополнительные сведения см. в разделе:

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

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Ограничьте доступ к браузеру с помощью управляемого браузера Intune с помощью приложения Azure AD на основе условного доступа для iOS и Android

**Тип.** Новая функция.  
**Категория обслуживания:** Условный доступ  
**Возможности продукта.** Безопасность и защита удостоверений.
 
**Сейчас на этапе общедоступной предварительной версии**

**Единый вход в Intune Managed Browser.** Ваши сотрудники могут использовать единый вход через собственные клиенты (например, Microsoft Outlook) и Intune Managed Browser для всех подключенных приложений Azure AD.

**Intune Управляемый браузер Условная поддержка доступа:** Теперь вы можете требовать от сотрудников использовать браузер Intune Managed с использованием политик условного доступа на основе приложений.

Дополнительные сведения об этом см. в нашем [блоге](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Дополнительные сведения см. в разделе:

- [Настройка приложения на основе условного доступа](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Управление доступом в Интернет с помощью политик Managed Browser в Microsoft Intune](https://aka.ms/managedbrowser)  

---
 
### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>App Прокси Cmdlets в PowerShell GA Модуль

**Тип.** Новая функция.  
**Категория службы.** Прокси-сервер приложения.  
**Возможность продукта.** Контроль доступа.
 
Поддержка приложения Прокси cmdlets теперь в PowerShell GA Модуль! Это требует, чтобы вы оставались в курсе модулей PowerShell - если вы стали более чем на год позади, некоторые cmdlets может перестать работать. 

Дополнительные сведения см. в разделе [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Поддержка собственных клиентов Office 365 службой простого единого входа с помощью неинтерактивного протокола

**Тип.** Новая функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Проверка подлинности пользователей
 
Пользователь, применяющий собственные клиенты Office 365 (версии 16.0.8730.xxxx и более поздней версии), может использовать автоматический вход с помощью простого единого входа. Поддержка этой возможности обеспечивается за счет добавления в Azure AD неинтерактивного протокола (WS-Trust).

Для получения дополнительной информации, [см. Как восподписаны на родной клиент с Бесшовные SSO работы?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)
 
---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Пользователи могут использовать автоматический вход с помощью простого единого входа, если приложение отправляет запросы на вход на клиентские конечные точки Azure AD

**Тип.** Новая функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Проверка подлинности пользователей
 
Пользователи могут использовать автоматический вход с помощью простого единого входа, если приложение (например, `https://contoso.sharepoint.com`) отправляет запросы на вход на клиентские конечные точки Azure AD, т. е. `https://login.microsoftonline.com/contoso.com/<..>` или `https://login.microsoftonline.com/<tenant_ID>/<..>`, вместо обычной конечной точки Azure AD (`https://login.microsoftonline.com/common/<...>`).

Для получения дополнительной [Azure Active Directory Seamless Single Sign-On](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)информации см. 

---
 
### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Необходимо добавить в параметры зоны интрасети пользователей только один URL-адрес Azure AD вместо двух (как было раньше) для развертывания простого единого входа

**Тип.** Новая функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Проверка подлинности пользователей
 
Чтобы развернуть простой единый вход для пользователей, необходимо добавить только один URL-адрес Azure AD в параметры зоны интрасети пользователей с помощью групповой политики Active Directory: `https://autologon.microsoftazuread-sso.com`. Ранее клиентам требовалось добавить два URL-адреса.

Для получения дополнительной [Azure Active Directory Seamless Single Sign-On](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)информации см. 
 
---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Новые федеративные приложения доступны в коллекции приложений Azure AD

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
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
**Возможности продукта:** Проверка подлинности пользователей
 
Теперь приложение Azure AD может запрашивать пользовательские или необязательные утверждения в токенах JWT или SAML.  Это утверждения о пользователе или клиенте, которые не включены по умолчанию в токен из-за ограничения размера или применимости.  В настоящий момент доступна общедоступная предварительная версия этой функции для приложений Azure AD в конечных точках версии 1.0 и версии 2.0.  Ознакомьтесь с соответствующей документацией, чтобы получить сведения о том, какие утверждения могут быть добавлены и как изменить манифест приложения, чтобы запросить их.  

Дополнительные сведения см. в статье [Optional claims in Azure AD (preview)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims) (Необязательные утверждения в Azure AD (предварительная версия)).
 
---
 
### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD поддерживает PKCE для более безопасных потоков OAuth

**Тип.** Новая функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Проверка подлинности пользователей
 
Документация по Azure AD была обновлена с учетом поддержки PKCE, что обеспечивает более безопасную связь во время потока предоставления кода авторизации OAuth 2.0.  S256 и code_challenge без шифрования поддерживаются в конечных точках версии 1.0 и версии 2.0. 

Для получения дополнительной информации смотрите [Запрос кода авторизации](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code). 
 
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

Для получения дополнительной информации [см. Динамические правила членства для групп в Active Directory Azure](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Улучшенный выход при простом едином входе

**Тип.** Измененная функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта:** Проверка подлинности пользователей
 
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
**Возможности продукта:** Каталог

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

В рамках обратной связи с клиентами, позволяющими ролям, не использующим функции для атакжем, иметь доступ к журналам активности Azure AD, мы позволили пользователям, вслекувшим роль «Читатель отчетов», получить доступ к входам и аудиту в рамках портала Azure, а также использовать API Microsoft Graph. 

Дополнительные сведения об использовании этих отчетов см. в статье [Отчеты Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Утверждение EmployeeID, доступное в виде атрибута пользователя и идентификатора пользователя

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Единый вход.
 
Утверждение **EmployeeID** можно настроить как идентификатор пользователя и атрибут пользователя для пользователей-участников и гостей B2B в приложении с входом на основе SAML из пользовательского интерфейса корпоративного приложения.

Дополнительные сведения см. в статье [Настройка утверждений, выпущенных в токене SAML для корпоративных приложений в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Упрощенное управление приложениями с использованием подстановочных знаков в Azure AD Application Proxy

**Тип.** Новая функция.  
**Категория службы.** Прокси-сервер приложения.  
**Возможности продукта:** Проверка подлинности пользователей
 
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
 
### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Приложения, поддерживающие политики защиты приложений Intune, добавленные для использования с помощью приложения Azure AD

**Тип.** Измененная функция.  
**Категория обслуживания:** Условный доступ  
**Возможности продукта.** Безопасность и защита удостоверений.

Мы добавили больше приложений, которые поддерживают условный доступ на основе приложений. Теперь вы можете получить доступ к Office 365 и другим облачным приложениям, подключенным к Azure AD, с помощью этих клиентских приложений.

К концу февраля будут добавлены следующие приложения:

- Microsoft Power BI

- Microsoft Launcher;

- Microsoft Invoicing.

Дополнительные сведения см. в разделе:

- [Требование утвержденного клиентского приложения](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Условный доступ к приложению Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Условия использования обновления для мобильного опыта 

**Тип.** Измененная функция.  
**Категория обслуживания:** Условия использования  
**Возможности продукта.** Соответствие требованиям.

Когда условия использования отображаются, теперь вы можете нажать **Имея проблемы просмотра? Нажмите здесь**. При выборе этой ссылки условия использования откроются, адаптировавшись под ваше устройство. Независимо от размера шрифта в документе или размера экрана устройства, документ можно увеличить, чтобы прочитать нужный фрагмент. 

---
 
## <a name="january-2018"></a>Январь 2018 г.
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Новые федеративные приложения доступны в коллекции приложений Azure AD 

**Тип.** Новая функция.  
**Категория обслуживания:** Корпоративные приложения  
**Возможности продукта.** Интеграция с решениями сторонних производителей.

В январе 2018 года в коллекцию приложений были добавлены следующие новые приложения с поддержкой федерации:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust Программное обеспечение для управления конфиденциальностью](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), «IriusRisk Федеративный каталог, и [Fidelity NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

Дополнительные сведения об этих приложениях см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial).

Дополнительные сведения о том, как добавить приложение в коллекцию приложений Azure AD см. в [этой статье](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="sign-in-with-additional-risk-detected"></a>Вход в систему с обнаруженным дополнительным риском

**Тип.** Новая функция.  
**Категория обслуживания:** Защита личных данных  
**Возможности продукта.** Безопасность и защита удостоверений.

Понимание обнаружения обнаруженного риска связано с подпиской Azure AD. Выпуск Azure AD Premium P2 позволяет получать самые подробные сведения обо всех основных обнаружениях.

В издании Azure AD Premium P1 обнаружения, не покрываемые вашей лицензией, отображаются как обнаружение риска, входе в систему с обнаруженным дополнительным риском.

Дополнительные сведения см. в статье об [обнаружении рисков Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 
---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Скрытие приложений Office 365 на панели доступа пользователя

**Тип.** Новая функция.  
**Категория службы.** "Мои приложения".  
**Возможности продукта.** Единый вход.

Теперь вы можете лучше управлять тем, как приложения Office 365 отображаются на панелях доступа пользователя, с помощью нового пользовательского параметра. Он позволяет уменьшить количество приложений, которые отображаются на панелях доступа пользователей, если вы предпочитаете, чтобы приложения Office отображались только на портале Office. Настройка расположена в **настройках пользователя** и помечена, **пользователи могут видеть приложения Office 365 только на портале Office 365.**

Дополнительные сведения см. в статье [Скрытие приложения в пользовательском интерфейсе Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 
### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Простой вход в приложения, поддерживающие единый вход с помощью пароля, непосредственно из URL-адреса приложения 

**Тип.** Новая функция.  
**Категория службы.** "Мои приложения".  
**Возможности продукта.** Единый вход.

Расширение браузера "Мои приложения" теперь доступно с помощью удобного инструмента, который дает возможность выполнять единый вход в "Мои приложения" через ярлык в вашем браузере. После установки появится значок в виде вафли в браузере, который предоставляет быстрый доступ к приложениям. Теперь пользователи могут воспользоваться следующими преимуществами:

- Возможность прямого входной регистрации в приложениях на основе SSO с страницы входной системы приложения
- Запускать любое приложение с помощью функции быстрого поиска.
- Использовать ярлыки для недавно использовавшихся приложений из расширения.
- Расширение доступно для браузеров Microsoft Edge, Chrome и Firefox.
 
Дополнительные сведения см. в разделе [Расширение защищенного входа на страницу "Мои приложения"](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Интерфейс администрирования Azure AD на классическом портале Azure больше не поддерживается

**Тип.** Прекращение поддержки.   
**Категория обслуживания:** Azure AD  
**Возможности продукта:** Каталог

Начиная с 8 января 2018 года интерфейс администрирования Azure AD на классическом портале Azure больше не поддерживается. Это произошло в связи с прекращением поддержки самого классического портала Azure. В будущем следует использовать [Центр администрирования Azure AD](https://aad.portal.azure.com) для задач администрирования Azure AD, выполняемых с помощью портала.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>Начиная с 8 января 2018 года прекращена поддержка веб-портала.

**Тип.** Прекращение поддержки.  
**Категория обслуживания:** Azure AD  
**Возможности продукта:** Каталог
 
Начиная с 8 января 2018 года прекращена поддержка веб-портала PhoneFactor. Этот портал использовался для администрирования сервера MFA, но эти функции были перенесены на портал Azure по адресу portal.azure.com. 

Чтобы перейти к настройке MFA, выберите **Azure Active Directory \> Сервер MFA**.
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Неподдерживаемые отчеты Azure AD

**Тип.** Прекращение поддержки.  
**Категория службы.** Отчеты.  
**Возможности продукта:** Управление жизненным циклом идентичности  


С выходом общедоступной версии новой консоли администрирования Azure Active Directory и новых API-интерфейсов, доступных для отчетов о действиях и безопасности, API-интерфейсы отчетов в конечной точке /reports были удалены 31 декабря 2017 года.

**Доступные возможности**

В рамках перехода на новую консоль администратора мы создали 2 новых программных интерфейса для извлечения журналов действий Azure AD. В новом наборе программных интерфейсов расширены функции фильтрации, сортировки, аудита и действий входа. Данные, ранее доступные через отчеты о безопасности, теперь могут быть доступны через API обнаружения рисков identity Protection в Microsoft Graph.

Дополнительные сведения см. в разделе:

- [Начало работы с API отчетов Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Начало работы с защитой идентификации Azure Active Directory и Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>Декабрь 2017 г.

### <a name="terms-of-use-in-the-access-panel"></a>Условия использования на панели доступа

**Тип.** Новая функция.  
**Категория обслуживания:** Условия использования  
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
**Категория обслуживания:** Azure AD  
**Возможности продукта:** Проверка подлинности пользователя
 
Пользовательские интерфейсы системы идентификации пользователей Azure AD и Майкрософт были перепроектированы таким образом, чтобы они имели единый внешний вид. Кроме того, страница входа в Azure AD сначала собирает имя пользователя, а затем учетные данные на втором экране.

Дополнительные сведения см. в записи блога [The new Azure AD Signin Experience is now in Public Preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/) (Новый интерфейс входа в Azure AD теперь находится в общедоступной предварительной версии).
 
---
 
### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Меньше запросов для входа. Новая функция "оставаться в системе" для входа в Azure AD

**Тип.** Новая функция.  
**Категория обслуживания:** Azure AD  
**Возможности продукта:** Проверка подлинности пользователя
 
Флажок **Оставаться в системе** на странице входа в Azure AD был заменен новым запросом, который появляется после прохождения проверки подлинности. 

Если на такой запрос ответить **Да**, служба предоставит постоянный токен обновления. То же самое происходило при установке флажка **Оставаться в системе** в старой версии. Для федеративных клиентов этот запрос отображается после выполнения проверки подлинности в службе федерации.

Дополнительные сведения см. в записи блога [Fewer login prompts: The new “Keep me signed in” experience for Azure AD is in preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/) (Меньшее число запросов пароля. Новая функция "Оставаться в системе" в Azure AD). 

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Добавление конфигурации для требования развертывания условий использования перед их принятием

**Тип.** Новая функция.  
**Категория обслуживания:** Условия использования  
**Возможности продукта.** Соответствие требованиям.
 
Функция для администраторов, позволяющая требовать развертывания условий использования перед их принятием пользователем.

**Включите** или **отключите** параметр "Требовать, чтобы пользователи развернули условия использования". Если выбран вариант **Вкл.**, чтобы принять соглашение, пользователям сначала нужно просмотреть условия.

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
**Возможности продукта:** Проверка подлинности пользователя

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
**Возможности продукта:** Служба управления доступом 

Использование службы контроля доступа Azure Active Directory (также называется просто службой контроля доступа) будет прекращено в конце 2018 г. Дополнительные сведения, включая подробное расписание и общее руководство по переходу, будут предоставлены в ближайшие недели. Вы можете оставить на этой странице комментарии с любыми вопросами, касающимися службы контроля доступа, и член нашей команды ответит вам.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Предоставление доступа через браузер к управляемому браузеру Intune 

**Тип.** Планирование изменений.  
**Категория обслуживания:** Условный доступ  
**Возможности продукта.** Безопасность и защита удостоверений.

Вы можете предоставлять доступ через браузер к Office 365 и другим облачным приложениям, подключенным к Azure AD, используя Intune Managed Browser в качестве утвержденного приложения. 

Теперь можно настроить следующее условие для условного доступа на основе приложений:

**Клиентские приложения:** Обозреватель

**Каков эффект изменения?**

В настоящее время при использовании этого условия доступ заблокирован. Если доступна предварительная версия, для всех операций доступа потребуется приложение управляемого браузера. 

Ищите сведения об этой возможности в предстоящих записях в блогах и заметках о выпуске. 

Для получения дополнительной информации смотрите [Условный доступ в Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Новые утвержденные клиентские приложения для приложения Azure AD на основе условного доступа

**Тип.** Планирование изменений.  
**Категория обслуживания:** Условный доступ  
**Возможности продукта.** Безопасность и защита удостоверений.

Следующие приложения добавлены в список [утвержденных клиентских приложений](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/);
- Microsoft StaffHub

Дополнительные сведения см. в разделе:

- [Требование утвержденного клиентского приложения](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Условный доступ к приложению Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Поддержка условий использования для нескольких языков

**Тип.** Новая функция.    
**Категория обслуживания:** Условия использования  
**Возможности продукта.** Соответствие требованиям.

Теперь администраторы могут создавать новые условия использования, содержащие несколько документов в формате PDF. Вы можете отметить эти документы соответствующими языками. Пользователям будут показаны PDF-файлы на том языке, который соответствует их предпочтениям. Если документа на их языке нет, отображается язык по умолчанию.

---
 
### <a name="real-time-password-writeback-client-status"></a>Состояние обратной записи паролей клиента в реальном времени

**Тип.** Новая функция.  
**Категория службы.** Самостоятельный сброс пароля.  
**Возможности продукта:** Проверка подлинности пользователя

Теперь вы можете просмотреть состояние обратной записи паролей клиента в локальной среде. Этот параметр доступен в разделе **Интеграция с локальной средой** на странице [Сброс пароля](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset). 

Если возникли проблемы с подключением к клиенту обратной записи в локальной среде, появится сообщение об ошибке, содержащее следующую информацию:

- сведения о том, почему не удается подключиться к клиенту обратной записи в локальной среде;
- ссылка на документацию, которая помогает устранить проблему. 

Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration)

---

### <a name="azure-ad-app-based-conditional-access"></a>Условный доступ к приложению Azure AD 
 
**Тип.** Новая функция.  
**Категория обслуживания:** Azure AD  
**Возможности продукта.** Безопасность и защита удостоверений.

Теперь можно ограничить доступ к Office 365 и другим облачным приложениям, подключенным к AD Azure AD, [утвержденным клиентским приложениям,](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview) поддерживающим политику защиты приложений Intune, используя [приложение Azure AD Conditional Access.](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) Политики защиты приложений Intune используются для настройки и защиты данных компании в этих клиентских приложениях.

[Объединив политики условного](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) доступа на основе [приложений](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) с устройствами, вы можете гибко защищать данные для личных и фирменных устройств.

Следующие условия и элементы управления теперь доступны для использования с помощью приложения на основе условного доступа:

**Условие поддерживаемой платформы**

- iOS
- Android

**Условие клиентских приложений**

- Мобильные приложения и настольные клиенты

**Управление доступом**

- Требование утвержденного клиентского приложения

Для получения дополнительной информации смотрите [приложение Azure AD на основе условного доступа.](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)
 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Управление устройствами Azure AD на портале Azure

**Тип.** Новая функция.  
**Категория обслуживания:** Регистрация и управление устройствами  
**Возможности продукта.** Безопасность и защита удостоверений.

Теперь все устройства, подключенные к Azure AD, и действия, связанные с устройствами, размещены в одном месте. На портале Azure доступен новый интерфейс администрирования для управления удостоверениями и параметрами устройств. В этом выпуске вы можете:

- Просмотр всех устройств, доступных для условного доступа в Azure AD.
- просматривать свойства, включая гибридные устройства, присоединенные к Azure AD;
- находить ключи BitLocker для устройств, присоединенных к Azure AD, чтобы управлять устройствами с помощью Intune и т. д.;
- управлять параметрами, связанными с устройствами Azure AD.

Дополнительные сведения см. в статье [Управление устройствами с помощью портала Azure](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Поддержка macOS как платформы устройства для условного доступа Azure AD 

**Тип.** Новая функция.    
**Категория обслуживания:** Условный доступ  
**Возможности продукта.** Безопасность и защита удостоверений. 

Теперь можно включить (или исключить) macOS в качестве условия платформы устройства в политику условного доступа Azure AD. Добавив macOS в качестве поддерживаемой платформы устройства, вы можете:

- **Регистрировать устройства macOS и управлять ими в Intune.** Подобно другим платформам, таким как iOS и Android, приложение корпоративного портала доступно для единой регистрации устройств macOS. Вы можете использовать новое приложение корпоративного портала для macOS, чтобы зарегистрировать устройство в Intune и Azure AD.
- **Гарантировать соблюдение устройствами macOS политик соответствия вашей организации, определенных в Intune.** В Intune на портале Azure вы можете настроить соответствующие политики для устройств macOS. 
- **Предоставлять доступ к приложениям Azure AD только совместимым устройствам macOS.** В качестве отдельного варианта платформы устройства имеется macOS. Теперь можно авторить политики условного доступа macOS для целевого приложения, установленного в Azure.

Дополнительные сведения см. в разделе:

- [Создание политики соответствия устройств для устройств macOS (предварительная версия) при помощи Intune](https://aka.ms/macoscompliancepolicy)
- [Условный доступ в Azure aD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Расширение NPS для Многофакторной идентификации Azure 

**Тип.** Новая функция.    
**Категория службы.** Многофакторная идентификация.  
**Возможности продукта:** Проверка подлинности пользователя

Расширение сервера политики сети для Многофакторной идентификации Azure добавляет в инфраструктуру проверки подлинности возможности Многофакторной идентификации на основе облака с использованием имеющихся серверов. Расширение NPS позволяет добавить телефонный звонок, текстовое сообщение или запрос на подтверждение в мобильном приложении в существующий процесс аутентификации. Можно обойтись без установки, настройки и поддержания новых серверов. 

Это расширение было создано для организаций, которым необходимо защитить VPN-подключения, не развертывая сервер Многофакторной идентификации Azure. Расширение NPS выполняет функции адаптера между RADIUS и облачной службой Azure MFA, предоставляя второй фактор проверки подлинности для федеративных или синхронизированных пользователей.

Дополнительные сведения см. в статье [Интеграция существующей инфраструктуры NPS с Многофакторной идентификацией Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).
 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Восстановление или окончательное удаление удаленных пользователей

**Тип.** Новая функция.    
**Категория обслуживания:** Управление пользователями  
**Возможности продукта:** Каталог 

Теперь в центре администрирования Azure AD вы можете:

- Восстановить удаленного пользователя. 
- Окончательно удалить пользователя.

**Для этого:**

1. В центре администрирования Azure AD в разделе **Управление** выберите [Все пользователи](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All). 

2. В списке **Показать** выберите **Недавно удаленные пользователи**. 

3. Выберите одного или нескольких недавно удаленных пользователей, а затем восстановите их или удалите без возможности восстановления.
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Новые утвержденные клиентские приложения для приложения Azure AD на основе условного доступа
 
**Тип.** Измененная функция.  
**Категория обслуживания:** Условный доступ  
**Возможности продукта.** Безопасность и защита удостоверений.

Следующие приложения добавлены в список [утвержденных клиентских приложений](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview):

- Планировщик (Майкрософт);
- Azure Information Protection 

Дополнительные сведения см. в разделе:

- [Требование утвержденного клиентского приложения](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Условный доступ к приложению Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Используйте "OR" между элементами управления в политике условного доступа 

**Тип.** Измененная функция.    
**Категория обслуживания:** Условный доступ  
**Возможности продукта.** Безопасность и защита удостоверений.
 
Теперь можно использовать "OR" (требуется один из выбранных элементов управления) для элементов управления условным доступом. Эта функция позволяет создавать политики со значением "или" между элементами управления доступом. Например, эта функция позволяет создать политику, требующую от пользователя входа в систему с помощью Многофакторной идентификации или использования соответствующего устройства.

Для получения дополнительной информации смотрите [элементы управления в Azure AD Условный доступ](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).
 
---

### <a name="aggregation-of-real-time-risk-detections"></a>Агрегирование обнаружений рисков в реальном времени

**Тип.** Измененная функция.    
**Категория обслуживания:** Защита личных данных  
**Возможности продукта.** Безопасность и защита удостоверений.

В Azure AD Identity Protection все обнаружения рисков в реальном времени, возникшые с одного и того же IP-адреса в данный день, теперь агрегируются для каждого типа обнаружения риска. Это изменение ограничивает объем обнаружений рисков, отображаемых без каких-либо изменений в безопасности пользователя.

Функция базового обнаружения в реальном времени работает каждый раз, когда пользователь выполняет вход. Если вы настроили для политики безопасности входа Многофакторную идентификацию или блокирование доступа, она все равно будет срабатывать при каждом подозрительном входе.
 
---
 
## <a name="october-2017"></a>Октябрь 2017 г.

### <a name="deprecate-azure-ad-reports"></a>Неподдерживаемые отчеты Azure AD

**Тип.** Планирование изменений.  
**Категория службы.** Отчеты.  
**Возможности продукта:** Управление жизненным циклом идентичности  

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
**Категория обслуживания:** Многофакторная аутентификация  
**Возможности продукта.** Безопасность и защита удостоверений.  

Многофакторная проверка подлинности (MFA) является важной частью процесса защиты организации. Чтобы сделать учетные данные более адаптивными, а работу — простой, были добавлены следующие возможности: 

- Влияние интеграции многофакторной аутентификации на отчет о входе в Azure AD, включая программный доступ к результатам многофакторной аутентификации.
- Более тесная интеграция конфигурации многофакторной проверки подлинности с конфигурацией Azure AD на портале Azure.

В общедоступной предварительной версии управление и составление отчетов многофакторной проверки подлинности являются неотъемлемой частью настройки основных компонентов Azure AD. Теперь вы можете управлять функциональными возможностями портала управления многофакторной проверки подлинности в Azure AD.

Дополнительные сведения см. в статье [Справочник по созданию отчетов многофакторной проверки подлинности на портале Azure](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 

---

### <a name="terms-of-use"></a>Условия использования

**Тип.** Новая функция.  
**Категория обслуживания:** Условия использования  
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

 В рамках перехода на новую консоль администратора доступны два новых программных интерфейса для извлечения доступных журналов действий Azure AD. В новом наборе программных интерфейсов расширены функции фильтрации, сортировки, аудита и действий входа. Данные, ранее доступные через отчеты о безопасности, теперь могут быть доступны через API обнаружения рисков защиты идентификационных данных в Microsoft Graph.


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
