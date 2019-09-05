---
title: Справочные материалы по действиям аудита в Azure Active Directory | Документация Майкрософт
description: Общие сведения о действиях аудита, которые могут быть зарегистрированы в журналах аудита в Azure Active Directory.
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/24/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: aaa60a7737b7781a21e23516d139332f10bdf448
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305998"
---
# <a name="azure-ad-audit-activity-reference"></a>Справочные материалы по действиям аудита в Azure Active Directory

Функция отчетов в Azure Active Directory (Azure AD) позволяет получать всю необходимую информацию о работе среды.

Архитектура создания отчетов в Azure AD состоит из следующих компонентов.

- **Отчеты о действиях** 
    - [Входы](concept-sign-ins.md) — информация об использовании управляемых приложений и входах пользователей.
    - [Журналы аудита](concept-audit-logs.md) — возможность отслеживания с помощью журналов всех изменений, внесенных при использовании разных функций в Azure AD. 
    
- **Отчеты системы безопасности** 
    - [Входы, представляющие риск](concept-risky-sign-ins.md). Вход, представляющий риск, означает, что в систему пытался войти пользователь, который не является законным владельцем учетной записи. 
    - [Пользователи, находящиеся в группе риска](concept-user-at-risk.md). Такая пометка означает, что конфиденциальность учетной записи пользователя, возможно, нарушена. 

В этой статье перечислены действия аудита, которые могут записываться в журналах аудита.

## <a name="access-reviews"></a>Проверки доступа

|Категория аудита|Действия|
|---|---|
|Проверки доступа|Окончание проверки доступа|
|Проверки доступа|Добавление утверждающего в утверждение запроса|
|Проверки доступа|Добавление утверждающего в проверку доступа|
|Проверки доступа|Применить проверку доступа|
|Проверки доступа|Создать проверку доступа|
|Проверки доступа|Создание программы|
|Проверки доступа|Создание утверждения запроса|
|Проверки доступа|Удаление проверки доступа|
|Проверки доступа|Удаление программы|
|Проверки доступа|Привязка элемента управления|
|Проверки доступа|Присоединение проверки доступа Azure AD|
|Проверки доступа|Удаление утверждающего из проверки доступа|
|Проверки доступа|Запрос остановки проверки|
|Проверки доступа|Запросить результат применения проверки|
|Проверки доступа|Проверка членства в ролях RBAC|
|Проверки доступа|Проверка назначения приложений|
|Проверки доступа|Проверка членства в группах|
|Проверки доступа|Проверка запроса на утверждение запроса|
|Проверки доступа|Отмена привязки элемента управления|
|Проверки доступа|Обновление проверки доступа|
|Проверки доступа|Обновление состояния подключения проверок доступа Azure Active Directory|
|Проверки доступа|Изменение параметров для уведомлений по почте при проверке доступа|
|Проверки доступа|Изменить число повторений проверок доступа|
|Проверки доступа|Изменить длительность повторений проверок доступа в днях|
|Проверки доступа|Изменить тип окончания повторений проверок доступа|
|Проверки доступа|Изменить тип повторений проверок доступа|
|Проверки доступа|Изменить параметры для напоминаний при проверке доступа|
|Проверки доступа|Обновление программы|
|Проверки доступа|Изменение утверждения запроса|
|Проверки доступа|Пользователь отключен|

## <a name="account-provisioning"></a>"Подготовка учетных записей";

|Категория аудита|Действия|
|---|---|
|Управление приложениями|Retrieve V2 application permissions (Получение разрешений для приложения версии 2)|
|Управление приложениями|Retrieve V2 application service principals in the current tenant (Получение субъектов-служб для приложения версии 2 в текущем клиенте)|
|Управление приложениями|Update V1 application (Обновление приложения версии 1)|
|Управление приложениями|Update V1 application (Обновление приложения версии 2)|
|Управление приложениями|Update V2 application permission grant (Обновление разрешения, предоставленного приложению версии 2)|
|Управление приложениями|Add Oauth2Permissiongrant (Добавление записи Oauth2Permissiongrant)|
|Управление приложениями|Add app role assignment to service principal (Добавление назначения роли приложения для субъекта-службы)|

## <a name="application-proxy"></a>Прокси приложения

|Категория аудита|Действия|
|---|---|
|Управление приложениями|Добавить приложение|
|Управление приложениями|Add owner to application (Добавление владельца приложения)|
|Управление приложениями|Add owner to service principal (Добавление владельца субъекта-службы)|
|Управление приложениями|Add policy to service principal (Добавление политики субъекта-службы)|
|Управление каталогом|Добавление субъекта-службы|
|Управление каталогом|Добавление учетных данных субъекта-службы|
|Управление каталогом|Consent to application (Разрешение на приложение)|
|Управление каталогом|Удаление приложения|
|Управление каталогом|Hard Delete application (Необратимое удаление приложения)|
|Управление каталогом|Remove Oauth2Permissiongrant (Удаление записи Oauth2Permissiongrant)|
|Управление каталогом|Remove app role assignment from service principal (Удаление назначения роли приложения из субъекта-службы)|
|Управление каталогом|Remove owner from application (Удаление владельца приложения)|
|Resource|Remove owner from service principal (Удаление владельца субъекта-службы)|
|Resource|Remove policy from service principal (Удаление политики субъекта-службы)|
|Resource|Удаление субъекта-службы|


## <a name="automated-password-rollover"></a>Automated password rollover (Автоматическая смена пароля);

|Категория аудита|Действия|
|---|---|
|Управление приложениями|Удаление учетных данных субъекта-службы|


## <a name="b2c"></a>B2C

|Категория аудита|Действия|
|---|---|
|Управление приложениями|Restore application (Восстановление приложения)|
|Управление приложениями|Revoke consent (Отмена согласия)|
|Управление приложениями|Обновление приложения|
|Управление приложениями|Обновление внешних секретов.|
|Управление приложениями|Update service principal (Обновление субъекта-службы)|
|Управление приложениями|Issue an access token to the application (Предоставление маркера доступа для приложения)|
|Управление приложениями|Issue an authorization code to the application (Предоставление кода авторизации для приложения)|
|Управление приложениями|Issue an access token to the application (Предоставление id_token для приложения)|
|Управление приложениями|Validate local account credentials (Проверка учетных данных локальной учетной записи)|
|Управление приложениями|Validate user authentication (Проверка подлинности для пользователя)|
|Управление приложениями|Add V2 application permissions (Добавление разрешений для приложения версии 2)|
|Управление приложениями|Add a key based on ASCII secret to a CPIM key container (Добавление ключа на основе секрета ASCII в контейнер ключей CPIM)|
|Управление приложениями|Add a key to a CPIM key container (Добавление ключа в контейнер ключей CPIM)|
|Управление приложениями|AdminPolicyDatas-SetResources|
|Управление приложениями|AdminUserJourneys-GetResources|
|Управление приложениями|AdminUserJourneys-RemoveResources|
|Проверка подлинности|AdminUserJourneys-SetResources|
|Проверка подлинности|Create IdentityProvider (Создание записи IdentityProvider)|
|Проверка подлинности|Create V1 application (Создание приложения версии 1)|
|Проверка подлинности|Create V2 application (Создание приложения версии 2)|
|Проверка подлинности|Create a custom domains in the tenant (Создание личных доменов в клиенте)|
|Authorization|Create a new AdminUserJourney (Создание записи AdminUserJourney)|
|Authorization|Create localized resource json (Создание JSON-файла локализованных ресурсов)|
|Authorization|Create new Custom IDP (Создание настраиваемого поставщика удостоверений)|
|Authorization|Create new IDP (Создание поставщика удостоверений)|
|Authorization|Create or update a B2C directory resource (Создание или обновление ресурса каталога B2C)|
|Authorization|Создать политику|
|Authorization|Create trustFramework policy (Создание политики trustFramework)|
|Authorization|Create trustFramework policy with configurable prefix (Создание политики trustFramework с настраиваемым префиксом)|
|Authorization|Create user attribute (Создание пользовательского атрибута)|
|Authorization|CreateTrustFrameworkPolicy|
|Authorization|Creates or Update an new AdminUserJourney (Создание или обновление записи AdminUserJourney)|
|Authorization|Delete IDP (Удаление поставщика удостоверений)|
|Authorization|Delete IdentityProvider (Удаление записи IdentityProvider)|
|Authorization|Delete V1 application (Удаление приложения версии 1)|
|Authorization|Delete V2 application (Удаление приложения версии 2)|
|Authorization|Delete V2 application permission grant (Удаление разрешений, предоставленных приложению версии 2)|
|Authorization|Delete a B2C directory resource (Удаление ресурса каталога B2C)|
|Authorization|Delete a CPIM key container (Добавление контейнера ключей CPIM)|
|Authorization|Delete trustFramework policy (Удаление политики trustFramework)|
|Authorization|Delete user attribute (Удаление пользовательского атрибута)|
|Authorization|Enable B2C feature (Включение компонента B2C)|
|Authorization|Get B2C directory resources in a subscription (Получение данных ресурсов каталога B2C в подписке)|
|Authorization|Get Custom IDP (Получение данных настраиваемого поставщика удостоверений)|
|Authorization|Get IDP (Получение данных поставщика удостоверений)|
|Authorization|Get V1 and V2 applications (Получение данных приложений 1 и 2)|
|Authorization|Get V1 application (Получение данных приложения версии 1)|
|Authorization|Get V1 applications (Получение данных приложений версии 1)|
|Authorization|Get V2 application (Получение данных приложения версии 2)|
|Authorization|Get V2 applications (Получение данных приложений версии 2)|
|Authorization|Получение ресурса каталога B2C|
|Authorization|Get a list of custom domains in the tenant (Получение списка личных доменов в клиенте)|
|Authorization|Get a user journey (Получение пути взаимодействия пользователя)|
|Authorization|Get allowed application claims for user journey (Получение допустимых утверждений приложения для пути взаимодействия пользователя)|
|Authorization|Get allowed self-asserted claims for user journey (Получение допустимых самоподтвержденных утверждений для пути взаимодействия пользователя)|
|Authorization|Get allowed self-asserted claims of policy (Получение допустимых самоподтвержденных утверждений политики)|
|Authorization|Get available output claims list (Получение списка доступных исходящих утверждений)|
|Authorization|Get content definitions for user journey (Получение определений содержимого для пути взаимодействия пользователя)|
|Authorization|Get idps for a specific admin flow (Получение данных поставщиков удостоверений для определенной последовательности операций администрирования)|
|Authorization|Get key container active key metadata in JWK (Получение метаданных в формате JWK для активных ключей в контейнере ключей)|
|Authorization|Get list of all admin flows (Получение списка всех последовательностей операций администрирования)|
|Authorization|Get list of tags for all admin flows for all users (Получение списка тегов всех последовательностей операций администрирования для всех пользователей)|
|Authorization|Get list of tenants for a user (Получение списка клиентов для пользователя)|
|Authorization|Get local accounts' self-asserted claims (Получение самоподтвержденных утверждений для локальных учетных записей)|
|Authorization|Get localized resource json (Получение JSON-файла локализованных ресурсов)|
|Authorization|Get operations of Microsoft.AzureActiveDirectory resource provider (Получение списка операций поставщика ресурсов Microsoft.AzureActiveDirectory)|
|Authorization|Get policies (Получение политик)|
|Authorization|Get policy (Получение политики)|
|Authorization|Get resource properties of a tenant (Получение свойств ресурсов клиента)|
|Authorization|Get supported IDP list (Получение списка поддерживаемых поставщиков удостоверений)|
|Authorization|Get supported IDP list of the user journey (Получение списка поддерживаемых поставщиков удостоверений для пути взаимодействия пользователя)|
|Authorization|Get tenant Info (Получение сведений о клиенте)|
|Authorization|Get tenant allowed features (Получение допустимых функций клиента)|
|Authorization|Get tenant defined Custom IDP list (Получение списка настраиваемых поставщиков удостоверений, определенных для клиента)|
|Authorization|Get tenant defined IDP list (Получение списка поставщиков удостоверений, определенных для клиента)|
|Authorization|Get tenant defined local IDP list (Получение списка локальных поставщиков удостоверений, определенных для клиента)|
|Authorization|Get tenant details for a user for resource creation (Получение сведений о клиенте для пользователя для создания ресурса)|
|Authorization|Get tenant list (Получение списка клиентов)|
|Authorization|Get tenantDomains (Получение доменов клиента)|
|Authorization|Get the default supported culture for CPIM (Получение сведений о поддерживаемых по умолчанию языке и региональных параметрах для CPIM)|
|Authorization|Get the details of an admin flow (Получение сведений о последовательности операций администрирования)|
|Authorization|Get the list of UserJourneys for this tenant (Получение списка путей взаимодействия пользователя для этого клиента)|
|Authorization|Get the set of available supported cultures for CPIM (Получение набора доступных поддерживаемых языков и региональных параметров для CPIM)|
|Authorization|Get trustFramework policy (Получение политики trustFramework)|
|Authorization|Get trustFramework policy as xml (Получение политики trustFramework в формате XML)|
|Authorization|Get user attribute (Получение пользовательского атрибута)|
|Authorization|Get user attributes (Получение пользовательских атрибутов)|
|Authorization|Get user journey list (Получение списка путей взаимодействия пользователя)|
|Authorization|GetIEFPolicies|
|Authorization|GetIdentityProviders|
|Authorization|GetTrustFrameworkPolicy|
|Authorization|Gets a CPIM key container in jwk format (Получение данных контейнера ключей CPIM в формате JWK)|
|Authorization|Gets list of key containers in the tenant (Получение списка контейнеров ключей в клиенте)|
|Authorization|Gets the type of tenant (Получение сведений о типе клиента)|
|Authorization|MigrateTenantMetadata|
|Authorization|Patch IdentityProvider (Исправление для поставщика удостоверений)|
|Authorization|PutTrustFrameworkPolicy|
|Authorization|PutTrustFrameworkpolicy|
|Authorization|Remove a user journey (Удаление пути взаимодействия пользователя)|
|Authorization|Restore a CPIM key container backup (Восстановление контейнера ключей CPIM из резервной копии)|
|Authorization|Retrieve V2 application permissions (Получение разрешений для приложения версии 2)|
|Authorization|Retrieve V2 application service principals in the current tenant (Получение субъектов-служб для приложения версии 2 в текущем клиенте)|
|Authorization|Update Custom IDP (Изменение настраиваемого поставщика удостоверений)|
|Authorization|Update IDP (Изменение поставщика удостоверений)|
|Authorization|Изменение локального поставщика удостоверений|
|Authorization|Update V1 application (Обновление приложения версии 1)|
|Authorization|Update V1 application (Обновление приложения версии 2)|
|Authorization|Update V2 application permission grant (Обновление разрешения, предоставленного приложению версии 2)|
|Authorization|Обновление политики|
|Authorization|Update user attribute (Обновление пользовательского атрибута)|
|Authorization|Upload a CPIM encrypted key (Отправка зашифрованного ключа CPIM)|
|Authorization|Авторизация пользователя: API отключен для набора функций клиента|
|Authorization|Авторизация пользователя: пользователю предоставлен доступ как администратору клиента|
|Authorization|Авторизация пользователя: пользователю предоставлены права доступа как прошедшему проверку подлинности|
|Authorization|Verify if B2C feature is enabled (Проверка того, включен ли компонент B2C)|
|Authorization|Проверка того, включен ли компонент|
|Authorization|Создание программы|
|Authorization|Удаление программы|
|Authorization|Привязка элемента управления|
|Authorization|Присоединение проверки доступа Azure AD|
|Authorization|Отмена привязки элемента управления|
|Authorization|Обновление программы|
|Authorization|Disable Desktop Sso (Отключение единого входа в классическом приложении)|
|Authorization|Disable Desktop Sso for a specific domain (Отключение единого входа в классическом приложении для определенного домена)|
|Authorization|Отключить прокси приложения|
|Authorization|Disable passthrough authentication (Отключение сквозной проверки подлинности)|
|Authorization|Enable Desktop Sso (Включение единого входа в классическом приложении)|
|Управление каталогом|Enable Desktop Sso for a specific domain (Включение единого входа в классическом приложении для определенного домена)|
|Управление каталогом|Enable application proxy (Включение прокси приложения)|
|Управление каталогом|Enable passthrough authentication (Включение сквозной проверки подлинности)|
|Управление каталогом|Create a custom domains in the tenant (Создание личных доменов в клиенте)|
|Управление каталогом|Enable B2C feature (Включение компонента B2C)|
|Управление каталогом|Get a list of custom domains in the tenant (Получение списка личных доменов в клиенте)|
|Управление каталогом|Get resource properties of a tenant (Получение свойств ресурсов клиента)|
|Управление каталогом|Get tenant Info (Получение сведений о клиенте)|
|Управление каталогом|Get tenant allowed features (Получение допустимых функций клиента)|
|Управление каталогом|Get tenantDomains (Получение доменов клиента)|
|Ключ|Gets the type of tenant (Получение сведений о типе клиента)|
|Ключ|Verify if B2C feature is enabled (Проверка того, включен ли компонент B2C)|
|Ключ|Проверка того, включен ли компонент|
|Ключ|Добавление партнера для компании|
|Ключ|Add unverified domain (Добавление непроверенного домена)|
|Ключ|Add verified domain (Добавление проверенного домена)|
|Ключ|Create company (Создание организации)|
|Ключ|Создание параметров организации.|
|Ключ|Удаление параметров организации.|
|Ключ|Demote partner (Изменение типа партнера)|
|Ключ|Directory deleted (Каталог удален)|
|Другой|Directory deleted permanently (Каталог удален без возможности восстановления)|
|Другой|Directory scheduled for deletion (Запланировано удаление этого каталога)|
|Resource|Promote company to partner (Продвижение компании до партнера)|
|Resource|Purge rights management properties (Очистка свойств управления правами)|
|Resource|Remove partner from company (Удаление партнера из компании)|
|Resource|Remove unverified domain (Удаление непроверенного домена)|
|Resource|Remove verified domain (Удаление проверенного домена)|
|Resource|Установка информации о компании|
|Resource|Set DirSync feature (Установка компонента Dirsync)|
|Resource|Set Dirsyncenabled flag (Установка флага Dirsyncenabled)|
|Resource|Set Partnership (Настройка партнерства)|
|Resource|Set accidental deletion threshold (Указание порога случайного удаления)|
|Resource|Set company allowed data location (Указание расположения данных организации)|
|Resource|Set company multinational feature enabled (Включение функции многонациональности для организации)|
|Resource|Set directory feature on tenant (Установка поддержки каталога в клиенте)|
|Resource|Установка проверки подлинности домена|
|Resource|Установка параметров федерации для домена|
|Resource|Set password policy (Настройка политики паролей)|
|Resource|Set rights management properties (Установка свойств управления правами)|
|Resource|Update company (Обновление организации)|
|Resource|Update company settings (Обновление параметров организации)|
|Resource|Обновление домена|
|Resource|Проверка домена|
|Resource|Проверка домена с помощью электронной почты|
|Resource|Выполняется подключение|
|Resource|Изменение параметров оповещений|
|Resource|Update weekly digest settings (Обновление параметров еженедельного дайджеста)|
|Resource|Disable password writeback for directory (Отключение обратной записи паролей для каталога)|
|Resource|Enable password writeback for directory (Включение обратной записи паролей для каталога)|
|Resource|Add app role assignment to group (Добавление назначения роли приложения для группы)|
|Resource|Добавить группу|
|Resource|Добавление участника в группу|
|Resource|Add owner to group (Добавление владельца группы)|
|Resource|Create group settings (Создание параметров группы)|
|Resource|Удаление группы|
|Resource|Delete group settings (Удаление параметров группы)|
|Resource|Finish applying group based license to users (Прекращение назначения для пользователей лицензии, назначенной группе)|
|Resource|Hard Delete group (Необратимое удаление группы)|
|Resource|Remove app role assignment from group (Удаление назначения роли приложения из группы)|
|Resource|Удаление участника из группы|
|Resource|Remove owner from group (Удаление владельца группы)|
|Resource|Restore Group (Восстановление группы)|
|Resource|Set group license (Настройка лицензии для группы)|
|Resource|Задание пользователя для управления группой.|
|Resource|Start applying group based license to users (Назначение пользователям лицензии, назначенной группе)|
|Resource|Trigger group license recalculation (Активация пересчета лицензий групп)|
|Resource|Обновление группы|
|Resource|Update group settings (Обновление параметров группы)|
|Resource|Добавить участника|
|Resource|Создать группу|
|Resource|Удалить группу|
|Resource|Удалить участника|
|Resource|Обновление группы|
|Resource|Approve a pending request to join a group (Подтверждение ожидающего запроса на присоединение к группе)|
|Resource|Cancel a pending request to join a group (Отмена ожидающего запроса на присоединение к группе)|
|Resource|Create lifecycle management policy (Создание политики управления жизненным циклом)|
|Resource|Delete a pending request to join a group (Удаление ожидающего запроса на присоединение к группе)|
|Resource|Reject a pending request to join a group (Отклонение ожидающего запроса на присоединение к группе)|
|Resource|Продлить действие группы|
|Resource|Request to join a group (Запрос на присоединение к группе)|
|Resource|Set dynamic group properties (Указание свойств динамической группы)|
|Resource|Update lifecycle management policy (Обновление политики управления жизненным циклом)|
|Resource|Add a key based on ASCII secret to a CPIM key container (Добавление ключа на основе секрета ASCII в контейнер ключей CPIM)|
|Resource|Add a key to a CPIM key container (Добавление ключа в контейнер ключей CPIM)|
|Resource|Delete a CPIM key container (Добавление контейнера ключей CPIM)|
|Resource|Delete key container (Удаление контейнера ключей)|
|Resource|Get key container active key metadata in JWK (Получение метаданных в формате JWK для активных ключей в контейнере ключей)|
|Resource|Get key container metadata (Получение метаданных контейнера ключей)|
|Resource|Gets a CPIM key container in jwk format (Получение данных контейнера ключей CPIM в формате JWK)|
|Resource|Gets list of key containers in the tenant (Получение списка контейнеров ключей в клиенте)|
|Resource|Restore a CPIM key container backup (Восстановление контейнера ключей CPIM из резервной копии)|
|Resource|Save key container (Сохранение контейнера ключей)|
|Resource|Upload a CPIM encrypted key (Отправка зашифрованного ключа CPIM)|
|Resource|Issue an authorization code to the application (Предоставление кода авторизации для приложения)|
|Resource|Issue an access token to the application (Предоставление id_token для приложения)|


## <a name="core-directory"></a>Core directory (Основной каталог);

|Категория аудита|Действия|
|---|---|
|Administrative Unit Management (Управление административной единицей)|Скачивание одного типа обнаружения рисков|
|Administrative Unit Management (Управление административной единицей)|Download admins and status of weekly digest opt-in (Скачивание сведений о состоянии подписки на еженедельный дайджест и подписавшихся на него администраторах)|
|Administrative Unit Management (Управление административной единицей)|Скачать все типы обнаружения рисков|
|Administrative Unit Management (Управление административной единицей)|Скачать Бесплатные определения рисков для пользователей|
|Administrative Unit Management (Управление административной единицей)|Download users flagged for risk (Скачивание списка пользователей, находящихся в группе риска)|
|Управление приложениями|Batch invites processed (Обработка пакета приглашений)|
|Управление приложениями|Batch invites uploaded (Отправка пакета приглашений)|
|Управление приложениями|Add owner to policy (Добавление владельца политики)|
|Управление приложениями|Добавить политику|
|Управление приложениями|Удалить политику|
|Управление приложениями|Remove policy credentials (Удаление учетных данных политики)|
|Управление приложениями|Обновление политики|
|Управление приложениями|Set MFA registration policy (Настройка политики регистрации в MFA)|
|Управление приложениями|Set sign-in risk policy (Настройка политики риска для входа в систему)|
|Управление приложениями|Set user risk policy (Настройка политики риска пользователя)|
|Управление приложениями|Accept Terms Of Use (Принятие условий использования)|
|Управление приложениями|Create Terms Of Use (Создание условий использования)|
|Управление приложениями|Decline Terms Of Use (Отклонение условий использования)|
|Управление приложениями|Delete Terms Of Use (Удаление условий использования)|
|Управление приложениями|Edit Terms Of Use (Изменение условий использования)|
|Управление приложениями|Publish Terms Of Use (Публикация условий использования)|
|Управление приложениями|Unpublish Terms Of Use (Отмена публикации условий использования)|
|Управление приложениями|Add application SSL certificate (Добавление SSL-сертификата приложения)|
|Управление приложениями|Delete SSL binding (Удаление привязки SSL)|
|Управление приложениями|Register connector (Регистрация соединителя)|
|Управление приложениями|AdminPolicyDatas-RemoveResources|
|Управление приложениями|AdminPolicyDatas-SetResources|
|Управление приложениями|AdminUserJourneys-GetResources|
|Управление каталогом|AdminUserJourneys-RemoveResources|
|Управление каталогом|AdminUserJourneys-SetResources|
|Управление каталогом|Create IdentityProvider (Создание записи IdentityProvider)|
|Управление каталогом|Create a new AdminUserJourney (Создание записи AdminUserJourney)|
|Управление каталогом|Create localized resource json (Создание JSON-файла локализованных ресурсов)|
|Управление каталогом|Create new Custom IDP (Создание настраиваемого поставщика удостоверений)|
|Управление каталогом|Create new IDP (Создание поставщика удостоверений)|
|Управление каталогом|Create or update a B2C directory resource (Создание или обновление ресурса каталога B2C)|
|Управление каталогом|Создать политику|
|Управление каталогом|Create trustFramework policy (Создание политики trustFramework)|
|Управление каталогом|Create trustFramework policy with configurable prefix (Создание политики trustFramework с настраиваемым префиксом)|
|Управление каталогом|Create user attribute (Создание пользовательского атрибута)|
|Управление каталогом|CreateTrustFrameworkPolicy|
|Управление каталогом|Delete IDP (Удаление поставщика удостоверений)|
|Управление каталогом|Delete IdentityProvider (Удаление записи IdentityProvider)|
|Управление каталогом|Delete a B2C directory resource (Удаление ресурса каталога B2C)|
|Управление каталогом|Delete trustFramework policy (Удаление политики trustFramework)|
|Управление каталогом|Delete user attribute (Удаление пользовательского атрибута)|
|Управление каталогом|Get B2C directory resources in a resource group (Получение данных ресурсов каталога B2C в группе ресурсов)|
|Управление каталогом|Get B2C directory resources in a subscription (Получение данных ресурсов каталога B2C в подписке)|
|Управление каталогом|Get Custom IDP (Получение данных настраиваемого поставщика удостоверений)|
|Управление каталогом|Get IDP (Получение данных поставщика удостоверений)|
|Управление каталогом|Получение ресурса каталога B2C|
|Управление каталогом|Get a user journey (Получение пути взаимодействия пользователя)|
|Управление каталогом|Get allowed application claims for user journey (Получение допустимых утверждений приложения для пути взаимодействия пользователя)|
|Управление каталогом|Get allowed self-asserted claims for user journey (Получение допустимых самоподтвержденных утверждений для пути взаимодействия пользователя)|
|Управление каталогом|Get allowed self-asserted claims of policy (Получение допустимых самоподтвержденных утверждений политики)|
|Управление каталогом|Get available output claims list (Получение списка доступных исходящих утверждений)|
|Управление каталогом|Get content definitions for user journey (Получение определений содержимого для пути взаимодействия пользователя)|
|Управление каталогом|Get idps for a specific admin flow (Получение данных поставщиков удостоверений для определенной последовательности операций администрирования)|
|Управление каталогом|Get list of all admin flows (Получение списка всех последовательностей операций администрирования)|
|Управление каталогом|Get list of tags for all admin flows for all users (Получение списка тегов всех последовательностей операций администрирования для всех пользователей)|
|Управление группами|Члены группы для группового скачивания — запущены|
|Управление группами|Члены группы группового скачивания — завершено|
|Управление группами|Элементы группы группового импорта — запущены|
|Управление группами|Члены группы группового импорта — завершено|
|Управление группами|Выполняется групповое удаление членов группы — запущено|
|Управление группами|Групповое удаление членов группы — завершено|
|Управление группами|Группы группового скачивания — запущено|
|Управление группами|Группы группового скачивания — завершено|
|Управление группами|Get list of tenants for a user (Получение списка клиентов для пользователя)|
|Управление группами|Get local accounts' self-asserted claims (Получение самоподтвержденных утверждений для локальных учетных записей)|
|Управление группами|Get localized resource json (Получение JSON-файла локализованных ресурсов)|
|Управление группами|Get operations of Microsoft.AzureActiveDirectory resource provider (Получение списка операций поставщика ресурсов Microsoft.AzureActiveDirectory)|
|Управление группами|Get policies (Получение политик)|
|Управление группами|Get policy (Получение политики)|
|Управление группами|Get supported IDP list (Получение списка поддерживаемых поставщиков удостоверений)|
|Управление группами|Get supported IDP list of the user journey (Получение списка поддерживаемых поставщиков удостоверений для пути взаимодействия пользователя)|
|Управление группами|Get tenant defined Custom IDP list (Получение списка настраиваемых поставщиков удостоверений, определенных для клиента)|
|Управление группами|Get tenant defined IDP list (Получение списка поставщиков удостоверений, определенных для клиента)|
|Управление группами|Get tenant defined local IDP list (Получение списка локальных поставщиков удостоверений, определенных для клиента)|
|Управление группами|Get tenant details for a user for resource creation (Получение сведений о клиенте для пользователя для создания ресурса)|
|Управление группами|Get the default supported culture for CPIM (Получение сведений о поддерживаемых по умолчанию языке и региональных параметрах для CPIM)|
|Управление группами|Get the details of an admin flow (Получение сведений о последовательности операций администрирования)|
|Управление группами|Get the list of UserJourneys for this tenant (Получение списка путей взаимодействия пользователя для этого клиента)|
|Управление группами|Get the set of available supported cultures for CPIM (Получение набора доступных поддерживаемых языков и региональных параметров для CPIM)|
|Управление группами|Get trustFramework policy (Получение политики trustFramework)|
|Управление группами|Get trustFramework policy as xml (Получение политики trustFramework в формате XML)|
|Управление группами|Get user attribute (Получение пользовательского атрибута)|
|Управление политикой|Get user attributes (Получение пользовательских атрибутов)|
|Управление политикой|Get user journey list (Получение списка путей взаимодействия пользователя)|
|Управление политикой|GetIEFPolicies|
|Управление политикой|GetIdentityProviders|
|Управление политикой|GetTrustFrameworkPolicy|
|Resource|MigrateTenantMetadata|
|Resource|Перемещение ресурсов|
|Resource|Patch IdentityProvider (Исправление для поставщика удостоверений)|
|Resource|PutTrustFrameworkPolicy|
|Resource|PutTrustFrameworkpolicy|
|Resource|Remove a user journey (Удаление пути взаимодействия пользователя)|
|Resource|Update Custom IDP (Изменение настраиваемого поставщика удостоверений)|
|Resource|Update IDP (Изменение поставщика удостоверений)|
|Resource|Изменение локального поставщика удостоверений|
|Resource|Update a B2C directory resource (Обновление ресурса каталога B2C)|
|Resource|Обновление политики|
|Resource|Update subscription status (Обновления состояния подписки)|
|Управление ролями|Update user attribute (Обновление пользовательского атрибута)|
|Управление ролями|Validate move resources (Проверка перемещаемых ресурсов)|
|Управление ролями|Add device (Добавление устройства)|
|Управление ролями|Add device configuration (Добавление конфигурации устройства)|
|Управление ролями|Add registered owner to device (Добавление зарегистрированного владельца устройства)|
|Управление ролями|Add registered users to device (Добавление зарегистрированных пользователей устройства)|
|Управление ролями|Удалить устройство|
|Управление ролями|Удаление конфигурации устройства|
|Управление ролями|Device no longer compliant (Устройство не является совместимым)|
|Управление ролями|Device no longer managed (Устройство не является управляемым)|
|Управление пользователями|AccessReview_Review|
|Управление пользователями|AccessReview_Update|
|Управление пользователями|ActivationAborted|
|Управление пользователями|ActivationApproved|
|Управление пользователями|ActivationCanceled|
|Управление пользователями|ActivationRequested|
|Управление пользователями|Add eligible member to role (Добавление соответствующего участника в роль)|
|Управление пользователями|Add member to role (Добавление участника в роль)|
|Управление пользователями|Add role assignment to role definition (Добавление назначения роли в определение роли)|
|Управление пользователями|Add role from template (Добавление роли на основе шаблона)|
|Управление пользователями|Add scoped member to role (Добавление участника с заданной областью в роль)|
|Управление пользователями|Добавлено|
|Управление пользователями|Назначить|
|Управление пользователями|Создание пользователей с массовым запуском|
|Управление пользователями|Создание пользователей с массовым управлением — завершено|
|Управление пользователями|Пользователи с массовым удалением — запущено|
|Управление пользователями|Пользователи с массовым удалением — завершено|
|Управление пользователями|Пользователи с пакетной загрузкой — запущено|
|Управление пользователями|Пользователи с пакетной загрузкой — завершено|
|Управление пользователями|Выполняется групповое восстановление удаленных пользователей — запущено|
|Управление пользователями|Групповое восстановление удаленных пользователей — завершено|
|Управление пользователями|Неполное приглашение для пользователей — запущено|
|Управление пользователями|Неполное приглашение для пользователей — завершено|
|Управление пользователями|Remove registered owner from device (Удаление зарегистрированного владельца устройства)|
|Управление пользователями|Remove registered users from device (Удаление зарегистрированных пользователей устройства)|
|Управление пользователями|Remove eligible member from role (Удаление соответствующего участника из роли)|
|Управление пользователями|Удаление участника из роли|
|Управление пользователями|Remove role assignment to role definition (Удаление назначения роли из определения роли)|
|Управление пользователями|Remove scoped member from role (Удаление участника с заданной областью из роли)|
|Управление пользователями|Обновить устройство|
|Управление пользователями|Update device configuration (Обновление конфигурации устройства)|
|Управление пользователями|Update role (Обновление роли)|






## <a name="identity-protection"></a>Защита идентификации

|Категория аудита|Действия|
|---|---|
|Управление каталогом|Повысить|
|Управление каталогом|Удалено|
|Управление каталогом|Изменения параметров ролей|
|Другой|ScanAlertsNow|
|Другой|Регистрация|
|Другой|Заблокировать доступ к ресурсам|
|Другой|UpdateAlertSettings|
|Другой|UpdateCurrentState|
|Управление политикой|Окончание проверки доступа|
|Управление политикой|Добавление утверждающего в утверждение запроса|
|Управление политикой|Добавление утверждающего в проверку доступа|
|Управление пользователями|Применить проверку доступа|
|Управление пользователями|Создать проверку доступа|


## <a name="invited-users"></a>Приглашенные пользователи

|Категория аудита|Действия|
|---|---|
|Другой|Создание утверждения запроса|
|Другой|Удаление проверки доступа|
|Управление пользователями|Удаление утверждающего из проверки доступа|
|Управление пользователями|Запросить результат применения проверки|
|Управление пользователями|Запрос остановки проверки|
|Управление пользователями|Проверка назначения приложений|
|Управление пользователями|Проверка членства в группах|
|Управление пользователями|Проверка членства в ролях RBAC|


## <a name="microsoft-identity-manager-mim"></a>Microsoft Identity Manager (MIM)

|Категория аудита|Действия|
|---|---|
|Управление группами|Проверка запроса на утверждение запроса|
|Управление группами|Обновление проверки доступа|
|Управление группами|Изменение параметров для уведомлений по почте при проверке доступа|
|Управление группами|Изменить число повторений проверок доступа|
|Управление группами|Изменить длительность повторений проверок доступа в днях|
|Управление пользователями|Изменить тип окончания повторений проверок доступа|
|Управление пользователями|Изменить тип повторений проверок доступа|



## <a name="privileged-identity-management"></a>Управление привилегированными пользователями

|Категория аудита|Действия|
|---|---|
|PIM|ActivationAborted|
|PIM|ActivationApproved|
|PIM|ActivationCanceled|
|PIM|ActivationDenied|
|PIM|ActivationRequested|
|PIM|Добавлено|
|PIM|AddedOutsidePIM|
|PIM|Назначить|
|PIM|DismissAlert|
|PIM|Повысить|
|PIM|ReactivateAlert|
|PIM|Удалено|
|PIM|RemovedOutsidePIM|
|PIM|Запрос остановки проверки|
|PIM|Изменения параметров ролей|
|PIM|ScanAlertsNow|
|PIM|Регистрация|
|PIM|Unassign|
|PIM|Заблокировать доступ к ресурсам|
|PIM|UpdateAlertSettings|
|PIM|UpdateCurrentState|


## <a name="self-service-group-management"></a>Самостоятельное управление группами

|Категория аудита|Действия|
|---|---|
|Управление группами|Сброс пароля пользователя|
|Управление группами|Восстановить учетную запись пользователя|
|Управление группами|Установка принудительной смены пароля пользователя|
|Управление группами|Set user manager (Настройка диспетчера пользователей)|
|Управление группами|Set users oath token metadata enabled (Включение отображения метаданных для OATH-токена пользователей)|
|Управление группами|Update StsRefreshTokenValidFrom Timestamp (Обновление метки времени StsRefreshTokenValidFrom)|
|Управление группами|Обновление внешних секретов.|
|Управление группами|Изменить пользователя|
|Управление группами|Admin generates a temporary password (Создание временного пароля администратором)|


## <a name="self-service-password-management"></a>Self-service password management (Самостоятельное управление паролями);

|Категория аудита|Действия|
|---|---|
|Управление каталогом|Admins requires the user to reset their password (Требование администратора сбросить пароль пользователя)|
|Управление каталогом|Assign external user to application (Назначение внешнего пользователя для приложения)|
|Управление пользователями|Email not sent, user unsubscribed (Сообщение электронной почты не отправлено. Пользователь отменил подписку.)|
|Управление пользователями|Invite external user (Приглашение внешнего пользователя)|
|Управление пользователями|Redeem external user invite (Активация приглашения внешнего пользователя)|
|Управление пользователями|Viral tenant creation (Создание вирусного клиента)|
|Управление пользователями|Viral user creation (Создание вирусного пользователя)|
|Управление пользователями|User Password Registration (Регистрация пароля пользователя)|
|Управление пользователями|User Password Reset (Сброс пароля пользователя)|
|Управление пользователями|Blocked from self-service password reset (Блокировка от самостоятельного сброса пароля)|


## <a name="terms-of-use"></a>Условия использования

|Категория аудита|Действия|
|---|---|
|Условия использования|Accept Terms Of Use (Принятие условий использования)|
|Условия использования|Create Terms Of Use (Создание условий использования)|
|Условия использования|Decline Terms Of Use (Отклонение условий использования)|
|Условия использования|Delete Consent (Удаление согласия)|
|Условия использования|Delete Terms Of Use (Удаление условий использования)|
|Условия использования|Edit Terms Of Use (Изменение условий использования)|
|Условия использования|Expire Terms Of Use (Настройка срока действия условий использования)|
|Условия использования|Hard Delete Terms Of Use (Необратимое удаление условий использования)|
|Условия использования|Publish Terms Of Use (Публикация условий использования)|
|Условия использования|Unpublish Terms Of Use (Отмена публикации условий использования)|


## <a name="next-steps"></a>Следующие шаги

- [Что такое отчеты в Azure Active Directory](overview-reports.md).
- [Отчет о журналах аудита](concept-audit-logs.md). 
- [Программный доступ к отчетам Azure Active Directory](concept-reporting-api.md).
