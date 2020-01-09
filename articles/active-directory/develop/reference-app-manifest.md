---
title: Основные сведения о манифесте приложения Azure Active Directory | Документация Майкрософт
description: Подробное описание манифеста приложения Azure Active Directory, который представляет конфигурацию удостоверений приложения в клиенте Azure AD и используется для упрощения авторизации OAuth, предоставления согласия и многого другого.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64823dbe2595d7896b9339745f6c8642a32f74e9
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75638703"
---
# <a name="azure-active-directory-app-manifest"></a>Манифест приложения Azure Active Directory

Манифест приложения содержит определения всех атрибутов для объекта приложения на платформе удостоверений Майкрософт. Также он служит механизмом для обновления объекта приложения. Дополнительные сведения о сущности приложения и ее схеме см. в документации по [сущности приложения API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity).

Атрибуты приложения можно настроить с помощью портал Azure или программными средствами с помощью [REST API](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) или [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#applications). Но в некоторых сценариях манифест приложения придется редактировать напрямую, чтобы настроить атрибут приложения. Ниже приведены соответствующие сценарии.

* Если вы зарегистрировали приложение в качестве нескольких клиентов и личных учетных записей Майкрософт Azure AD, вы не сможете изменить поддерживаемые учетные записи Майкрософт в пользовательском интерфейсе. Вместо этого для изменения типа поддерживаемых учетных записей придется использовать редактор манифеста приложения.
* Если вам нужно определить разрешения и роли, которые поддерживает приложение, необходимо изменить манифест приложения.

## <a name="configure-the-app-manifest"></a>Настройка манифеста приложения

Чтобы настроить манифест приложения, выполните следующее.

1. Перейдите на [портал Azure](https://portal.azure.com). Найдите и выберите службу **Azure Active Directory** .
1. Щелкните **Регистрация приложений**.
1. Выберите приложение, которое вам нужно настроить.
1. На странице **Обзор** выберите раздел **Манифест**. Откроется веб-редактор манифеста, который позволяет изменить манифест, не покидая портал. Также здесь можно выбрать команду **Скачать**, чтобы изменить манифест локально и применить к приложению обновленную версию с помощью команды **Отправить**.

## <a name="manifest-reference"></a>Справка по манифесту

> [!NOTE]
> Если не отображается столбец **Пример значения** после **Описания**, увеличьте окно браузера и прокрутите или проведите пальцем, пока не увидите столбец **Пример значения**.

| Ключ  | Тип значения | Description  | Пример значения |
|---------|---------|---------|---------|
| `accessTokenAcceptedVersion` | Int32, допускающий значение NULL | Указывает версию маркера доступа, ожидаемую ресурсом. Этот параметр изменяет версию и формат JWT, созданного независимо от конечной точки, или клиента, используемого для запроса маркера доступа.<br/><br/>Используемая конечная точка версии 1.0 или 2.0 выбирается клиентом и влияет только на версию id_tokens. В ресурсах необходимо явным образом настраивать `accesstokenAcceptedVersion` для указания поддерживаемого формата маркера доступа.<br/><br/>Возможные значения для `accesstokenAcceptedVersion`: 1, 2 или null. Если значение равно null, этот параметр по умолчанию равен 1, что соответствует конечной точке v 1.0. <br/><br/>Если `signInAudience` `AzureADandPersonalMicrosoftAccount`, значение должно быть `2`  | `2` |
| `addIns` | Коллекция | Определяет настраиваемое поведение, которое служба-клиент может использовать для вызова приложения в определенном контексте. Например, приложения, которые могут визуализировать файловые потоки, могут устанавливать свойство AddIns для его функции "FileHandler". Этот параметр позволяет таким службам, как Office 365, вызывать приложение в контексте документа, над которым работает пользователь. | <code>{<br>&nbsp;&nbsp;&nbsp;"id":"968A844F-7A47-430C-9163-07AE7C31D407"<br>&nbsp;&nbsp;&nbsp;"type": "FileHandler",<br>&nbsp;&nbsp;&nbsp;"properties": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{"key": "version", "value": "2" }<br>&nbsp;&nbsp;&nbsp;]<br>}</code>|
| `allowPublicClient` | Логическое | Указывает тип возврата приложения. Azure AD по умолчанию задает тип приложения из replyUrlsWithType. В некоторых сценариях Azure AD не удается определить тип клиентского приложения. Например, одним из таких сценариев является поток [ропк](https://tools.ietf.org/html/rfc6749#section-4.3) , где HTTP-запрос происходит без перенаправления URL-адреса). В таких случаях Azure AD будет интерпретировать тип приложения на основе значения этого свойства. Если для этого значения установлено значение true, тип резервного приложения устанавливается как общедоступный клиент, например, установленное приложение, работающее на мобильном устройстве. Значение по умолчанию — false. Это значит, что тип возврата приложения является закрытым клиентом, таким как веб-приложение. | `false` |
| `availableToOtherTenants` | Логическое | значение true, если приложение используется совместно с другими клиентами; в противном случае — значение false. <br><br> _Примечание. Эта возможность доступна только в Регистрация приложений (устаревшая). Заменяется `signInAudience` в интерфейсе [Регистрация приложений](https://go.microsoft.com/fwlink/?linkid=2083908) ._ | |
| `appId` | String | Указывает уникальный идентификатор приложения, который назначается приложению в Azure AD. | `"601790de-b632-4f57-9523-ee7cb6ceba95"` |
| `appRoles` | Коллекция | Указывает коллекцию ролей, которые может объявить приложение. Эти роли могут назначаться пользователям, группам или субъектам-службам. Ознакомьтесь с дополнительными сведениями и примерами в статье [Добавление ролей приложения в приложение, зарегистрированное в Azure Active Directory, и их получение в токене](howto-add-app-roles-in-azure-ad-apps.md) | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code>  |
| `displayName` | String | Отображаемое имя приложения. <br><br> _Примечание. Эта возможность доступна только в Регистрация приложений (устаревшая). Заменяется `name` в интерфейсе [Регистрация приложений](https://go.microsoft.com/fwlink/?linkid=2083908) ._ | `"MyRegisteredApp"` |
| `errorUrl` | String | Не поддерживается. | |
| `groupMembershipClaims` | String | Настраивает утверждение `groups`, выданное в маркере пользователя или маркере доступа OAuth 2.0, которого ожидает приложение. Чтобы задать этот атрибут, используйте одно из следующих допустимых строковых значений.<br/><br/>- `"None"`<br/>- `"SecurityGroup"` (группы безопасности и роли Azure AD).<br/>- `"All"` (это предоставит все группы безопасности, группы рассылки и роли каталога Azure AD, членом которых является выполнивший вход пользователь). | `"SecurityGroup"` |
| `homepage` | String | URL-адрес домашней страницы приложения. <br><br> _Примечание. Эта возможность доступна только в Регистрация приложений (устаревшая). Заменяется `signInUrl` в интерфейсе [Регистрация приложений](https://go.microsoft.com/fwlink/?linkid=2083908) ._ | `"https://MyRegisteredApp"` |
| `objectId` | String | Уникальный идентификатор для приложения в каталоге. <br><br> _Примечание. Эта возможность доступна только в Регистрация приложений (устаревшая). Заменяется `id` в интерфейсе [Регистрация приложений](https://go.microsoft.com/fwlink/?linkid=2083908) ._ | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `optionalClaims` | String | Необязательные утверждения, возвращаемые в маркере службой токенов безопасности для этого конкретного приложения.<br>На данный момент в приложениях, в которых поддерживаются как личные учетные записи, так и Azure AD (зарегистрированный с помощью портала регистрации приложений), невозможно использовать необязательные утверждения. Тем не менее приложения, которые были зарегистрированы, только чтобы использовать Azure AD и конечную точку версии 2.0, могут получить необязательные утверждения, запрашиваемые в манифесте. Дополнительные сведения см. в статье [Необязательные утверждения в Azure AD (предварительная версия)](active-directory-optional-claims.md). | `null` |
| `id` | String | Уникальный идентификатор для приложения в каталоге. Это не тот идентификатор, который используется для идентификации приложения в транзакциях протоколов. Он используется для ссылки на объект в запросах к каталогу. | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `identifierUris` | Массив строк | Определяемые пользователем URI, которые уникальным образом идентифицируют веб-приложение в клиенте Azure AD или в проверенном личном домене, если приложение является мультитенантным. | <code>[<br>&nbsp;&nbsp;"https://MyRegisteredApp"<br>]</code> |
| `informationalUrls` | String | Указывает ссылки на условия предоставления услуг и заявление о конфиденциальности приложения. Условия обслуживания и заявление о конфиденциальности отображаются в окне запроса согласия пользователя. Дополнительные сведения см. в статье [Условия обслуживания и заявление о конфиденциальности для зарегистрированных приложений Azure Active Directory](howto-add-terms-of-service-privacy-statement.md). | <code>{<br>&nbsp;&nbsp;&nbsp;"marketing":"https://MyRegisteredApp/marketing",<br>&nbsp;&nbsp;&nbsp;"privacy":"https://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"support":"https://MyRegisteredApp/support",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"https://MyRegisteredApp/termsofservice"<br>}</code> |
| `keyCredentials` | Коллекция | Содержит ссылки на учетные данные, назначенные приложению, строковые общие секреты и сертификаты X.509. Иначе будет отображено такое сообщение об ошибке несоответствия встроенного ПО: "Невозможно продолжить сброс параметров к значениям по умолчанию из-за несоответствия версий встроенного ПО". | <code>[<br>&nbsp;{<br>&nbsp;&These credentials are used when requesting access tokens (when the app is acting as a client rather that as resourcenbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> |
| `knownClientApplications` | Массив строк | Используется для объединения согласия, если у вас есть решение, которое содержит две части: клиентское приложение и пользовательское приложение веб-API. Если в это значение ввести appID клиентского приложения, пользователю нужно будет только единожды предоставить согласие на использование клиентского приложения. Azure AD будет иметь представление о том, что передача клиенту данных означает неявное отправку в веб-API. В этом случае автоматически подготавливаются субъекты-службы для клиента и интерфейса API. И клиентское приложение, и веб-API должны быть зарегистрированы в одном и том же клиенте. | `["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"]` |
| `logoUrl` | String | Значение "Только для чтения", указывает на URL-адрес CDN логотипа, который был загружен на портале. | `"https://MyRegisteredAppLogo"` |
| `logoutUrl` | String | URL-адрес для выхода из приложения. | `"https://MyRegisteredAppLogout"` |
| `name` | String | Отображаемое имя приложения. | `"MyRegisteredApp"` |
| `oauth2AllowImplicitFlow` | Логическое | Определяет, может ли это веб-приложение запрашивать маркеры доступа неявного потока OAuth 2.0. Значение по умолчанию — false. Этот флаг используется для браузерных приложений, таких как одностраничные приложения JavaScript. Чтобы получить дополнительные сведения, введите `OAuth 2.0 implicit grant flow` в поле над содержанием и просмотрите статьи о неявном потоке. | `false` |
| `oauth2AllowIdTokenImplicitFlow` | Логическое | Определяет, может ли это веб-приложение запрашивать маркеры идентификатора неявного потока OAuth 2.0. Значение по умолчанию — false. Этот флаг используется для браузерных приложений, таких как одностраничные приложения JavaScript. | `false` |
| `oauth2Permissions` | Коллекция | Указывает коллекцию областей разрешений доступа OAuth 2.0, которые веб-API (ресурс) предоставляет клиентским приложениям. Эти области действия разрешений могут быть назначены клиентским приложениям во время предоставления согласия. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>] </code>|
| `oauth2RequiredPostResponse` | Логическое | Указывает, будет ли Azure AD в рамках запросов маркеров OAuth 2.0 разрешать запросы POST в отличие от запросов GET. Значение по умолчанию — false. В таком случае будут разрешены только запросы GET. | `false` |
| `parentalControlSettings` | String | `countriesBlockedForMinors` указывает страны, в которых приложение заблокировано для несовершеннолетних.<br>`legalAgeGroupRule` определяет правило возрастной группы, применяемое к пользователям приложения. Можно установить `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids` или `BlockMinors`.  | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> |
| `passwordCredentials` | Коллекция | См. описание свойства `keyCredentials`. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> |
| `preAuthorizedApplications` | Коллекция | Список приложений и запрашиваемых разрешений для косвенного согласия. Требуется, чтобы администратор предоставил согласие для приложения. preAuthorizedApplications не требуют согласия пользователя на запрашиваемые разрешения. Разрешения, перечисленные в preAuthorizedApplications, не требуют согласия пользователя. Тем не менее любые дополнительные запрашиваемые разрешения, не указанные в preAuthorizedApplications, требуют согласия пользователя. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",<br>&nbsp;&nbsp;&nbsp;&nbsp;"permissionIds": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"8748f7db-21fe-4c83-8ab5-53033933c8f1"<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>]</code> |
| `publicClient` | Логическое | Указывает, является ли это приложение общедоступным клиентом (таким, как установленное приложение, работающее на мобильном устройстве). <br><br> _Примечание. это свойство доступно только в Регистрация приложений (устаревшая). Заменяется `allowPublicClient` в интерфейсе [Регистрация приложений](https://go.microsoft.com/fwlink/?linkid=2083908) ._ | |
| `publisherDomain` | String | Проверенный домен издателя для приложения. Только для чтения. | https://www.contoso.com |
| `replyUrls` | Массив строк | Это свойство с несколькими значениями хранит список зарегистрированных значений redirect_uri, которые Azure AD будет принимать в качестве назначений при возвращении маркеров. <br><br> _Примечание. это свойство доступно только в Регистрация приложений (устаревшая). Заменяется `replyUrlsWithType` в интерфейсе [Регистрация приложений](https://go.microsoft.com/fwlink/?linkid=2083908) ._ | |
| `replyUrlsWithType` | Коллекция | Это свойство с несколькими значениями хранит список зарегистрированных значений redirect_uri, которые Azure AD будет принимать в качестве назначений при возвращении маркеров. Каждое значение URI должно содержать связанное значение типа приложения. Поддерживаемые значения типа: <ul><li>`Web`</li><li>`InstalledClient`</li></ul><br> Дополнительные сведения об [ограничениях и](https://docs.microsoft.com/azure/active-directory/develop/reply-url)ограничениях реплюрл. | <code>"replyUrlsWithType":&nbsp;[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"url":&nbsp;"https://localhost:4400/services/office365/redirectTarget.html",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":&nbsp;"InstalledClient"&nbsp;&nbsp;&nbsp;<br>&nbsp;&nbsp;}<br>]</code> |
| `requiredResourceAccess` | Коллекция | При динамическом согласии `requiredResourceAccess` обеспечивает процедуру использования согласия администратора и согласия пользователя для пользователей, которые используют статическое согласие. Однако этот параметр не является процедурой согласия пользователя в общем случае.<br>`resourceAppId` — уникальный идентификатор для ресурса, доступ к которому нужен приложению. Это значение должно соответствовать appId, который был объявлен в целевом приложении ресурса.<br>`resourceAccess` — массив, в котором перечислены области разрешений OAuth 2.0 и роли приложений, которые требуются приложению из указанного ресурса. Содержит значения `id` и `type` указанных ресурсов. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>] </code> |
| `samlMetadataUrl` | String | URL-адрес метаданных SAML для приложения. | `https://MyRegisteredAppSAMLMetadata` |
| `signInUrl` | String | Указывает URL-адрес домашней страницы приложения. | `https://MyRegisteredApp` |
| `signInAudience` | String | Указывает, какие учетные записи Майкрософт поддерживаются для текущего приложения. Поддерживаются значения:<ul><li>**Азуреадмйорг** — пользователи с рабочей или учебной учетной записью Майкрософт в клиенте Azure AD моей организации (например, один клиент).</li><li>**Азуреадмултиплеоргс** — пользователи с рабочей или учебной учетной записью Майкрософт в любом клиенте Azure AD Организации (например, с несколькими клиентами).</li> <li>**AzureADandPersonalMicrosoftAccount**. Пользователи с личной учетной записью Майкрософт, рабочей или учебной учетной записью в клиенте Azure AD любой организации.</li></ul> | `AzureADandPersonalMicrosoftAccount` |
| `tags` | Массив строк | Пользовательские строки, которые могут использоваться для классификации и идентификации приложения. | <code>[<br>&nbsp;&nbsp;"ProductionApp"<br>]</code> |

## <a name="common-issues"></a>Типичные проблемы

### <a name="manifest-limits"></a>Ограничения манифеста

Манифест приложения имеет несколько атрибутов, которые называются коллекциями; Например, approles, keycredentials, knownClientApplications, identifierUris, Редиретурис, requiredResourceAccess и oauth2Permissions. В полном манифесте приложения для любого приложения общее число записей во всех Объединенных коллекциях было ограничено на 1200. Если ранее в манифесте приложения вы указали URI перенаправления 100, то вы оставляете только 1100 оставшихся записей для использования во всех остальных коллекциях, составляющих манифест.

> [!NOTE]
> Если вы попытаетесь добавить более 1200 записей в манифест приложения, может появиться сообщение об ошибке **"не удалось обновить приложение XXXXXX. Сведения об ошибке: Размер манифеста превысил установленный предел. Сократите количество значений и повторите запрос ".**

### <a name="unsupported-attributes"></a>Неподдерживаемые атрибуты

Манифест приложения представляет схему базовой модели приложения в Azure AD. По мере развития базовой схемы редактор манифеста будет обновлен, чтобы отразить новую схему от времени. В результате вы можете заметить, что новые атрибуты отображаются в манифесте приложения. В редких случаях может обнаружиться синтаксические или семантическое изменение существующих атрибутов, или же атрибут, который существовал ранее, больше не поддерживается. Например, вы увидите новые атрибуты в [Регистрация приложений](https://go.microsoft.com/fwlink/?linkid=2083908), которые называются разными именами в интерфейсе регистрация приложений (прежних версий).


| Регистрация приложений (устаревший)| Регистрация приложений           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Описание этих атрибутов см. в разделе [Справочник по манифесту](#manifest-reference) .

При попытке загрузить ранее скачанный манифест может появиться одна из следующих ошибок. Эта ошибка может быть вызвана тем, что редактор манифестов теперь поддерживает более новую версию схемы, которая не соответствует той, которую вы пытаетесь передать.

- "**Не удалось обновить приложение XXXXXX. Сведения об ошибке: недопустимый идентификатор объекта "undefine". [].** "
- "**Не удалось обновить приложение XXXXXX. Сведения об ошибке: одно или несколько указанных значений свойств недопустимы. [].** "
- "**Не удалось обновить приложение XXXXXX. Сведения об ошибке: не разрешено задавать Аваилаблетусертенантс в этой версии API для обновления. [].** "
- "**Не удалось обновить приложение XXXXXX. Сведения об ошибке: обновление свойства "replyUrls" не разрешено для этого приложения. Используйте вместо этого свойство "Реплюрлсвистипе". [].** "
- "**Не удалось обновить приложение XXXXXX. Сведения об ошибке: обнаружено значение без имени типа, и ожидаемый тип недоступен. При указании модели каждое значение в полезных данных должно иметь тип, который может быть указан в полезных данных явным образом вызывающим объектом или неявно выведен из родительского значения. []** "

Если вы видите одну из этих ошибок, мы рекомендуем выполнить следующие действия:

1. Измените атрибуты по отдельности в редакторе манифеста вместо отправки ранее скачанного манифеста. Используйте [ссылочную таблицу манифеста](#manifest-reference) , чтобы понять синтаксис и семантику старых и новых атрибутов, чтобы можно было успешно изменять нужные атрибуты. 
1. Если рабочий процесс требует сохранить манифесты в исходном репозитории для последующего использования, мы рекомендуем переназначить сохраненные манифесты в репозитории, используя тот, который вы видите в **Регистрация приложенийном** интерфейсе.

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о связи между объектами приложения и субъекта-службы см. [в разделе объекты приложения и субъекта службы в Azure AD](app-objects-and-service-principals.md).
* Определения основных концепций разработчика платформы идентификации Майкрософт см. в [глоссарии разработчика платформы удостоверений Майкрософт](developer-glossary.md) .

Оставляйте свои замечания и пожелания в разделе ниже. Ваши отзывы помогают нам улучшать содержимое веб-сайта.

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
