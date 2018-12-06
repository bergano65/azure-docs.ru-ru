---
title: Новые возможности Заметки о выпуске для Azure Active Directory | Документация Майкрософт
description: Узнайте о новых возможностях Azure Active Directory (Azure AD), включая последние заметки о выпуске, известные проблемы, исправления ошибок, нерекомендуемые функции и предстоящие изменения.
services: active-directory
author: eross-msft
manager: mtillman
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: lizross
ms.reviewer: dhanyahk
ms.openlocfilehash: dc4e421808ab0f79070224edea4b75f527affaf0
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52426243"
---
# <a name="whats-new-in-azure-active-directory"></a>Новые возможности Azure Active Directory

>Получайте уведомления о том, когда следует повторно посетить эту страницу для обновлений, скопировав и вставив URL-адрес `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` в средство чтения RSS-канала ![значок RSS](./media/whats-new/feed-icon-16x16.png).

Azure AD усовершенствуется на постоянной основе. Чтобы вы оставались в курсе последних разработок, в этой статье предоставлены такие сведения:

- Последние выпуски.
- Известные проблемы
- Исправления ошибок
- Нерекомендуемые функции.
- Планы по изменениям.

Эта страница обновляется ежемесячно, поэтому регулярно пересматривайте ее. Элементы, созданные больше 6 месяцев назад, можно найти в [архиве для новых возможностей в Azure Active Directory](whats-new-archive.md).

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

- Глобальный администратор или редактор компании

- Администратор службы Intune

- Администратор учетных записей

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

Новая вкладка **Устранение неполадок и поддержка** на странице **Входы в систему** портала Azure поможет администраторам и инженерам службы поддержки устранять неполадки, связанные с входами в систему AAD. Это новая вкладка содержит код ошибки, сообщение об ошибке и рекомендации по исправлению (при наличии), которые помогут устранить проблему. Если с проблемой не удастся справиться, здесь же можно создать запрос в службу поддержки с помощью новой функции **копирования в буфер обмена**, с помощью которой для файла журнала заполняются поля **Идентификатор запроса** и **Дата (UTC)** в запросе в службу поддержки.  

![Журналы входа в систему на новой вкладке](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Улучшенная поддержка пользовательских свойств расширения, позволяющих создать правила динамического членства

**Тип.** Измененная функция.  
**Категория службы.** Управление группами.  
**Возможности продукта.** Совместная работа.

После этого обновления вы сможете при создании динамического правила членства перейти по ссылке **Get custom extension properties** (Получить пользовательские свойства расширения) из конструктора динамических правил для пользовательских групп, ввести уникальный идентификатор приложения и получить полный список пользовательских свойств расширения. Этот список можно обновить, чтобы просмотреть новые пользовательские свойства расширения для выбранного приложения.

Дополнительные сведения об использовании пользовательских свойств расширения в правилах динамического членства см. в разделе [Свойства расширения и пользовательские свойства расширения](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Новые утвержденные клиентские приложения для условного доступа на основе приложения Azure AD

**Тип.** Планирование изменений.  
**Категория службы.** Условный доступ.  
**Возможности продукта.** Безопасность и защита удостоверений.

Следующие приложения добавлены в список [утвержденных клиентских приложений](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference#approved-client-app-requirement):

- Microsoft To-Do

- Microsoft Stream;

Дополнительные сведения можно найти в разделе 

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
- защиту идентификации Azure AD 
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

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Сведения об условном доступе, добавленные в отчет о входе в Azure AD

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

- [Cisco Spark](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-spark-provisioning-tutorial)

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

### <a name="updates-to-the-terms-of-use-tou-end-user-ui"></a>Обновления пользовательского интерфейса с условиями использования (ToU)

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

## <a name="june-2018"></a>Июнь 2018 г.

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Уведомление об изменении: исправление безопасности делегированной авторизации для приложений, использующих API для журналов действий Azure AD

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

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-tou-creation"></a>Шаблон политики условного доступа "все гости", созданный при формировании условий использования

**Тип.** Новая функция.  
**Категория службы.** Условия использования.  
**Возможности продукта.** Система управления.

Во время формирования условий использования также создается шаблон политики условного доступа для всех гостей и всех приложений. Этот новый шаблон политики применяет только что созданные условия использования, упрощая создание и применение процесса для гостей.

Дополнительные сведения см. в статье [Функция "Условия использования Azure Active Directory" (предварительная версия)](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-tou-creation"></a>Пользовательский шаблон политики условного доступа, созданный при формировании условий использования

**Тип.** Новая функция.  
**Категория службы.** Условия использования.  
**Возможности продукта.** Система управления.

Во время формирования условий использования также создается пользовательский шаблон политики условного доступа. Этот новый шаблон политики позволяет создать условия использования и немедленно перейти к колонке создания политики условного доступа без необходимости вручную переходить по порталу.

Дополнительные сведения см. в статье [Функция "Условия использования Azure Active Directory" (предварительная версия)](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

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

- **Если вы используете общедоступный пиринг Azure.** Необходимо перейти к пирингу Майкрософт и зарегистрироваться в сообществе **Другие онлайн-службы Office 365 (12076:5100)**. Дополнительные сведения см. в статье [Переход с общедоступного пиринга на пиринг Майкрософт](https://docs.microsoft.com/azure/expressroute/how-to-move-peering).

- **Если вы используете пиринг Майкрософт.** Зарегистрируйтесь в сообществе **Другие онлайн-службы Office 365 (12076:5100)**. Дополнительные сведения о требованиях к маршрутизации см. в разделе [Поддержка сообществ BGP](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) статьи о требованиях к маршрутизации ExpressRoute.

Если вам необходимо продолжить использование выделенных каналов, потребуется поговорить со службой технической поддержки учетных записей Майкрософт о том, как получить разрешение на использование сообщества**Другие онлайн-службы Office 365 (12076:5100)**. Контрольный совет MS Office проверит, нужны ли вам эти каналы, и убедится, что вы понимаете технические последствия их использования. Несанкционированные подписки, пытающиеся создать фильтры маршрутов для Office 365, получат сообщение об ошибке. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>API Microsoft Graph для администрирования условий использования

**Тип.** Новая функция.  
**Категория службы.** Условия использования.  
**Возможности продукта.** Возможности для разработчиков
 
Мы добавили API Microsoft Graph для администрирования условий использования Azure AD. Вы можете создавать, изменять и удалять объект условий использования.

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
 
Новое пошаговое руководство по развертыванию Azure Active Directory, которое включает такие темы, как самостоятельный сброс пароля (SSPR), единый вход (SSO), условный доступ, прокси-сервер приложения, подготовка пользователей, службы федерации Active Directory для сквозной проверки подлинности и ADFS для синхронизации хэша паролей.

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

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-responsemode-for-new-apps"></a>Маркеры идентификации теперь невозможно возвращать с помощью режима ответа query для новых приложений. 

**Тип.** Измененная функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Проверка подлинности пользователей.
 
Приложения, созданные 25 апреля 2018 года или позднее, больше не могут запрашивать значение для **id_token** с использованием режима ответа **query**.  Это обеспечивает соответствие Azure AD спецификациям OIDC и помогает снизить уязвимость ваших приложений перед атаками.  Приложения, созданные до 25 апреля 2018 года, могут использовать режим ответа **query** с типом запроса **id_token**.  При запросе значения id_token из AAD возвращается ошибка **AADSTS70007: query не является поддерживаемым значением для режима response_mode при запросе токена**.

Значения **fragment** и **form_post** для режимов ответа по-прежнему доступны при создании новых объектов приложения (например, для использования прокси приложения). Вам следует использовать одно из этих значений response_mode, прежде чем создавать приложения.  

---