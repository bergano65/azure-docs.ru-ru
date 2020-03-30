---
title: Основные сведения о манифесте приложения Azure Active Directory | Документация Майкрософт
description: Подробное описание манифеста приложения Azure Active Directory, который представляет конфигурацию удостоверений приложения в клиенте Azure AD и используется для упрощения авторизации OAuth, предоставления согласия и многого другого.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/23/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: 6d9a4af5ee814282589959fcf840c1061358ca18
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80383945"
---
# <a name="azure-active-directory-app-manifest"></a>Манифест приложения Azure Active Directory

Манифест приложения содержит определения всех атрибутов для объекта приложения на платформе удостоверений Майкрософт. Также он служит механизмом для обновления объекта приложения. Для получения дополнительной информации о сущности приложения [Graph API Application entity documentation](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)и его схеме см.

Вы можете настроить атрибуты приложения через портал Azure или программно использовать [REST API](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) или [PowerShell.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#applications) Но в некоторых сценариях манифест приложения придется редактировать напрямую, чтобы настроить атрибут приложения. Ниже приведены соответствующие сценарии.

* Если вы зарегистрировали приложение как мультитенантные и личные учетные записи Microsoft, вы не можете изменить поддерживаемые учетные записи Майкрософт в пользовательском доступе. Вместо этого для изменения типа поддерживаемых учетных записей придется использовать редактор манифеста приложения.
* Если вам нужно определить разрешения и роли, которые поддерживает приложение, необходимо изменить манифест приложения.

## <a name="configure-the-app-manifest"></a>Настройка манифеста приложения

Чтобы настроить манифест приложения, выполните следующее.

1. Перейдите на [портал Azure](https://portal.azure.com). Поиск и выбор службы **активного каталога Azure.**
1. Выберите **регистрацию приложений**.
1. Выберите приложение, которое вам нужно настроить.
1. На странице **Обзор** выберите раздел **Манифест**. Откроется веб-редактор манифеста, который позволяет изменить манифест, не покидая портал. Также здесь можно выбрать команду **Скачать**, чтобы изменить манифест локально и применить к приложению обновленную версию с помощью команды **Отправить**.

## <a name="manifest-reference"></a>Справка по манифесту

В этом разделе описаны атрибуты, найденные в манифесте приложения.

### <a name="accesstokenacceptedversion-attribute"></a>accessTokenAcceptedVersion атрибут

| Ключ | Тип значения |
| :--- | :--- |
| accessTokenAcceptedVersion | Int32, допускающий значение NULL |

Указывает версию маркера доступа, ожидаемую ресурсом. Этот параметр изменяет версию и формат JWT, созданный независимо от конечной точки или клиента, используемого для запроса токена доступа.

Используемая конечная точка версии 1.0 или 2.0 выбирается клиентом и влияет только на версию id_tokens. В ресурсах необходимо явным образом настраивать `accesstokenAcceptedVersion` для указания поддерживаемого формата маркера доступа.

Возможные значения для `accesstokenAcceptedVersion`: 1, 2 или null. Если значение является нулевым, этот параметр по умолчанию до 1, что соответствует конечному пункту v1.0.

Если `signInAudience` `AzureADandPersonalMicrosoftAccount`есть, значение `2`должно быть .

Пример

```json
    "accessTokenAcceptedVersion": 2,
```

### <a name="addins-attribute"></a>addIns атрибут

| Ключ | Тип значения |
| :--- | :--- |
| addIns | Collection |

Определяет настраиваемое поведение, которое служба-клиент может использовать для вызова приложения в определенном контексте. Например, приложения, которые могут отображать `addIns` потоки файлов, могут настроить свойство для функциональности "FileHandler". Этот параметр позволит службам, таким как Office 365, звонить в приложение в контексте документа, над которым работает пользователь.

Пример

```json
    "addIns": [
       {
        "id": "968A844F-7A47-430C-9163-07AE7C31D407",
        "type":" FileHandler",
        "properties": [
           {
              "key": "version",
              "value": "2"
           }
        ]
       }
    ],
```

### <a name="allowpublicclient-attribute"></a>allowPublicClient атрибут

| Ключ | Тип значения |
| :--- | :--- |
| allowPublicClient | Логическое |

Указывает тип возврата приложения. Azure AD по умолчанию задает тип приложения из replyUrlsWithType. Существуют определенные сценарии, в которых Azure AD не может определить тип клиентского приложения. Например, одним из таких сценариев является поток [ROPC,](https://tools.ietf.org/html/rfc6749#section-4.3) в котором запрос HTTP происходит без перенаправления URL). В этих случаях Azure AD будет интерпретировать тип приложения на основе значения этого свойства. Если для этого значения установлено значение true, тип резервного приложения устанавливается как общедоступный клиент, например, установленное приложение, работающее на мобильном устройстве. Значение по умолчанию — false. Это значит, что тип возврата приложения является закрытым клиентом, таким как веб-приложение.

Пример

```json
    "allowPublicClient": false,
```

### <a name="availabletoothertenants-attribute"></a>доступныеАтрибутыToOtherTenants

| Ключ | Тип значения |
| :--- | :--- |
| AvailableToOtherTenants | Логическое |

Установить, чтобы исправить, если приложение совместно с другими арендаторами; в противном случае, ложно.

> [!NOTE]
> Этот атрибут доступен только в опыте **регистрации приложений (Наследие).** Заменено `signInAudience` в опыте [регистрации App.](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="appid-attribute"></a>атрибут appId

| Ключ | Тип значения |
| :--- | :--- |
| appId | Строка |

Указывает уникальный идентификатор приложения, который назначается приложению в Azure AD.

Пример

```json
    "appId": "601790de-b632-4f57-9523-ee7cb6ceba95",
```

### <a name="approles-attribute"></a>appRoles атрибут

| Ключ | Тип значения |
| :--- | :--- |
| appRoles | Collection |

Указывает коллекцию ролей, которые может объявить приложение. Эти роли могут назначаться пользователям, группам или субъектам-службам. Для получения дополнительных примеров и [информации см.](howto-add-app-roles-in-azure-ad-apps.md)

Пример

```json
    "appRoles": [
        {
           "allowedMemberTypes": [
               "User"
           ],
           "description": "Read-only access to device information",
           "displayName": "Read Only",
           "id": "601790de-b632-4f57-9523-ee7cb6ceba95",
           "isEnabled": true,
           "value": "ReadOnly"
        }
    ],
```

### <a name="displayname-attribute"></a>Атрибут displayName

| Ключ | Тип значения |
| :--- | :--- |
| displayName | Строка |

Отображаемое имя приложения.

> [!NOTE]
> Этот атрибут доступен только в опыте **регистрации приложений (Наследие).** Заменено `name` в опыте [регистрации App.](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="errorurl-attribute"></a>атрибут errorUrl

| Ключ | Тип значения |
| :--- | :--- |
| errorUrl | Строка |

Не поддерживается.

### <a name="groupmembershipclaims-attribute"></a>атрибут groupMembershipClaims

| Ключ | Тип значения |
| :--- | :--- |
|groupMembershipClaims | Строка |

Настраивает утверждение `groups`, выданное в маркере пользователя или маркере доступа OAuth 2.0, которого ожидает приложение. Чтобы задать этот атрибут, используйте одно из следующих допустимых строковых значений.

- `"None"`
- `"SecurityGroup"` (группы безопасности и роли Azure AD).
- `"All"` (это предоставит все группы безопасности, группы рассылки и роли каталога Azure AD, членом которых является выполнивший вход пользователь).

Пример

```json
    "groupMembershipClaims": "SecurityGroup",
```

### <a name="homepage-attribute"></a>атрибут домашней страницы

| Ключ | Тип значения |
| :--- | :--- |
| homepage |Строка |

URL-адрес домашней страницы приложения.

> [!NOTE]
> Этот атрибут доступен только в опыте **регистрации приложений (Наследие).** Заменено `signInUrl` в опыте [регистрации App.](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="objectid-attribute"></a>objectId атрибут

| Ключ | Тип значения |
| :--- | :--- |
|objectId | Строка |

Уникальный идентификатор для приложения в каталоге.

Это доступно только в опыте **регистрации приложений (Наследие).** Заменено `id` в опыте [регистрации App.](https://go.microsoft.com/fwlink/?linkid=2083908)

Пример

```json
    "objectId": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="optionalclaims-attribute"></a>дополнительные претензии атрибут

| Ключ | Тип значения |
| :--- | :--- |
| optionalClaims | Строка |

Необязательные утверждения, возвращаемые в маркере службой токенов безопасности для этого конкретного приложения.

На данный момент в приложениях, в которых поддерживаются как личные учетные записи, так и Azure AD (зарегистрированный с помощью портала регистрации приложений), невозможно использовать необязательные утверждения. Тем не менее приложения, которые были зарегистрированы, только чтобы использовать Azure AD и конечную точку версии 2.0, могут получить необязательные утверждения, запрашиваемые в манифесте. Для получения дополнительной информации [см.](active-directory-optional-claims.md)

Пример

```json
    "optionalClaims": null,
```

### <a name="id-attribute"></a>id, атрибут

| Ключ | Тип значения |
| :--- | :--- |
| идентификатор | Строка |

Уникальный идентификатор для приложения в каталоге. Это не тот идентификатор, который используется для идентификации приложения в транзакциях протоколов. Он используется для ссылки на объект в запросах к каталогу.

Пример

```json
    "id": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="identifieruris-attribute"></a>идентификаторUris атрибут

| Ключ | Тип значения |
| :--- | :--- |
| identifierUris | Массив строк |

Определяемые пользователем URI, которые уникальным образом идентифицируют веб-приложение в клиенте Azure AD или в проверенном личном домене, если приложение является мультитенантным.

Пример

```json
    "identifierUris": "https://MyRegisteredApp",
```

### <a name="informationalurls-attribute"></a>атрибут informationalUrls

| Ключ | Тип значения |
| :--- | :--- |
| informationalUrls | Строка |

Указывает ссылки на условия предоставления услуг и заявление о конфиденциальности приложения. Условия обслуживания и заявление о конфиденциальности отображаются в окне запроса согласия пользователя. Дополнительные сведения см. в статье [Условия обслуживания и заявление о конфиденциальности для зарегистрированных приложений Azure Active Directory](howto-add-terms-of-service-privacy-statement.md).

Пример

```json
    "informationalUrls": {
        "termsOfService": "https://MyRegisteredApp/termsofservice",
        "support": "https://MyRegisteredApp/support",
        "privacy": "https://MyRegisteredApp/privacystatement",
        "marketing": "https://MyRegisteredApp/marketing"
    },
```

### <a name="keycredentials-attribute"></a>атрибут keyCredentials

| Ключ | Тип значения |
| :--- | :--- |
| keyCredentials | Collection |

Содержит ссылки на учетные данные, назначенные приложению, строковые общие секреты и сертификаты X.509. Эти учетные данные используются при запросе токенов доступа (когда приложение выступает в качестве клиента, а не как ресурс).

Пример

```json
    "keyCredentials": [
        {
           "customKeyIdentifier":null,
           "endDate":"2018-09-13T00:00:00Z",
           "keyId":"<guid>",
           "startDate":"2017-09-12T00:00:00Z",
           "type":"AsymmetricX509Cert",
           "usage":"Verify",
           "value":null
        }
    ],
```

### <a name="knownclientapplications-attribute"></a>известный атрибут клиент-приложений

| Ключ | Тип значения |
| :--- | :--- |
| knownClientApplications | Массив строк |

Используется для объединения согласия, если у вас есть решение, которое содержит две части: клиентское приложение и пользовательское приложение веб-API. Если в это значение ввести appID клиентского приложения, пользователю нужно будет только единожды предоставить согласие на использование клиентского приложения. Azure AD будет знать, что согласие с клиентом означает неявное согласие на Web API. Он будет автоматически предоставлять услуги принципы для клиента и веб-API в то же время. И клиентское приложение, и веб-API должны быть зарегистрированы в одном и том же клиенте.

Пример

```json
    "knownClientApplications": ["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"],
```

### <a name="logourl-attribute"></a>logoUrl атрибут

| Ключ | Тип значения |
| :--- | :--- |
| logoUrl | Строка |

Значение "Только для чтения", указывает на URL-адрес CDN логотипа, который был загружен на портале.

Пример

```json
    "logoUrl": "https://MyRegisteredAppLogo",
```

### <a name="logouturl-attribute"></a>logoutUrl атрибут

| Ключ | Тип значения |
| :--- | :--- |
| logoutUrl | Строка |

URL-адрес для выхода из приложения.

Пример

```json
    "logoutUrl": "https://MyRegisteredAppLogout",
```

### <a name="name-attribute"></a>атрибут name

| Ключ | Тип значения |
| :--- | :--- |
| name | Строка |

Отображаемое имя приложения.

Пример

```json
    "name": "MyRegisteredApp",
```

### <a name="oauth2allowimplicitflow-attribute"></a>oauth2AllowImplicitFlow атрибут

| Ключ | Тип значения |
| :--- | :--- |
| oauth2AllowImplicitFlow | Логическое |

Определяет, может ли это веб-приложение запрашивать маркеры доступа неявного потока OAuth 2.0. Значение по умолчанию — false. Этот флаг используется для браузерных приложений, таких как одностраничные приложения JavaScript. Чтобы получить дополнительные сведения, введите `OAuth 2.0 implicit grant flow` в поле над содержанием и просмотрите статьи о неявном потоке.

Пример

```json
    "oauth2AllowImplicitFlow": false,
```

### <a name="oauth2allowidtokenimplicitflow-attribute"></a>oauth2AllowIdTokenImplicitFlowFlowFlowFlow

| Ключ | Тип значения |
| :--- | :--- |
| oauth2AllowIdTokenImplicitFlowFlow | Логическое |

Определяет, может ли это веб-приложение запрашивать маркеры идентификатора неявного потока OAuth 2.0. Значение по умолчанию — false. Этот флаг используется для браузерных приложений, таких как одностраничные приложения JavaScript.

Пример

```json
    "oauth2AllowIdTokenImplicitFlow": false,
```

### <a name="oauth2permissions-attribute"></a>атрибут oauth2Permissions

| Ключ | Тип значения |
| :--- | :--- |
| oauth2Permissions | Collection |

Указывает коллекцию областей разрешений доступа OAuth 2.0, которые веб-API (ресурс) предоставляет клиентским приложениям. Эти области действия разрешений могут быть назначены клиентским приложениям во время предоставления согласия.

Пример

```json
    "oauth2Permissions": [
       {
          "adminConsentDescription": "Allow the app to access resources on behalf of the signed-in user.",
          "adminConsentDisplayName": "Access resource1",
          "id": "<guid>",
          "isEnabled": true,
          "type": "User",
          "userConsentDescription": "Allow the app to access resource1 on your behalf.",
          "userConsentDisplayName": "Access resources",
          "value": "user_impersonation"
        }
    ],
```

### <a name="oauth2requiredpostresponse-attribute"></a>oauth2RequiredPostResponse атрибут

| Ключ | Тип значения |
| :--- | :--- |
| oauth2RequiredPostResponse | Логическое |

Указывает, будет ли Azure AD в рамках запросов маркеров OAuth 2.0 разрешать запросы POST в отличие от запросов GET. Значение по умолчанию — false. В таком случае будут разрешены только запросы GET.

Пример

```json
    "oauth2RequirePostResponse": false,
```

### <a name="parentalcontrolsettings-attribute"></a>Атрибут родительскогоControlSettings

| Ключ | Тип значения |
| :--- | :--- |
| РодительскиеКонтрольНастройки | Строка |

- `countriesBlockedForMinors` указывает страны, в которых приложение заблокировано для несовершеннолетних.
- `legalAgeGroupRule` определяет правило возрастной группы, применяемое к пользователям приложения. Можно установить `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids` или `BlockMinors`.  

Пример

```json
    "parentalControlSettings": {
        "countriesBlockedForMinors": [],
        "legalAgeGroupRule": "Allow"
    },
```

### <a name="passwordcredentials-attribute"></a>passwordCredentials атрибут

| Ключ | Тип значения |
| :--- | :--- |
| passwordCredentials | Collection |

См. описание свойства `keyCredentials`.

Пример

```json
    "passwordCredentials": [
      {
        "customKeyIdentifier": null,
        "endDate": "2018-10-19T17:59:59.6521653Z",
        "keyId": "<guid>",
        "startDate":"2016-10-19T17:59:59.6521653Z",
        "value":null
      }
    ],
```

### <a name="preauthorizedapplications-attribute"></a>атрибут preAuthorizedApplications

| Ключ | Тип значения |
| :--- | :--- |
| предварительноавторизованныеприложения | Collection |

Список приложений и запрашиваемых разрешений для косвенного согласия. Требуется, чтобы администратор предоставил согласие для приложения. preAuthorizedApplications не требуют согласия пользователя на запрашиваемые разрешения. Разрешения, перечисленные в preAuthorizedApplications, не требуют согласия пользователя. Тем не менее любые дополнительные запрашиваемые разрешения, не указанные в preAuthorizedApplications, требуют согласия пользователя.

Пример

```json
    "preAuthorizedApplications": [
       {
          "appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",
          "permissionIds": [
             "8748f7db-21fe-4c83-8ab5-53033933c8f1"
            ]
        }
    ],
```

### <a name="publicclient-attribute"></a>PublicClient атрибут

| Ключ | Тип значения |
| :--- | :--- |
| publicClient | Логическое|

Указывает, является ли это приложение общедоступным клиентом (таким, как установленное приложение, работающее на мобильном устройстве). 

Это свойство доступно только в опыте **регистрации приложений (Наследие).** Заменено `allowPublicClient` в опыте [регистрации App.](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="publisherdomain-attribute"></a>publisherDomain атрибут

| Ключ | Тип значения |
| :--- | :--- |
| publisherDomain | Строка |

Проверенный домен издателя для приложения. Только для чтения.

Пример

```json
    "publisherDomain": "https://www.contoso.com",
````

### <a name="replyurls-attribute"></a>атрибут replyUrls

| Ключ | Тип значения |
| :--- | :--- |
| replyUrls | Массив строк |

Это свойство с несколькими значениями хранит список зарегистрированных значений redirect_uri, которые Azure AD будет принимать в качестве назначений при возвращении маркеров.

Это свойство доступно только в опыте **регистрации приложений (Наследие).** Заменено `replyUrlsWithType` в опыте [регистрации App.](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="replyurlswithtype-attribute"></a>атрибут replyUrlsWithType

| Ключ | Тип значения |
| :--- | :--- |
| replyUrlsWithType | Collection |

Это свойство с несколькими значениями хранит список зарегистрированных значений redirect_uri, которые Azure AD будет принимать в качестве назначений при возвращении маркеров. Каждое значение URI должно содержать связанное значение типа приложения. Поддерживаемые значения типа:

- `Web`
- `InstalledClient`

Чтобы узнать больше, смотрите [ограничения и ограничения replyUrl](https://docs.microsoft.com/azure/active-directory/develop/reply-url).

Пример

```json
    "replyUrlsWithType": [
       {
          "url": "https://localhost:4400/services/office365/redirectTarget.html",
          "type": "InstalledClient"
       }
    ],
```

### <a name="requiredresourceaccess-attribute"></a>требуется атрибутResourceAccess

| Ключ | Тип значения |
| :--- | :--- |
| requiredResourceAccess | Collection |

При динамическом согласии `requiredResourceAccess` обеспечивает процедуру использования согласия администратора и согласия пользователя для пользователей, которые используют статическое согласие. Однако этот параметр не дает понять, что пользователь соглашается на общий случай.

- `resourceAppId` — уникальный идентификатор для ресурса, доступ к которому нужен приложению. Это значение должно соответствовать appId, который был объявлен в целевом приложении ресурса.
- `resourceAccess` — массив, в котором перечислены области разрешений OAuth 2.0 и роли приложений, которые требуются приложению из указанного ресурса. Содержит значения `id` и `type` указанных ресурсов.

Пример

```json
    "requiredResourceAccess": [
        {
            "resourceAppId": "00000002-0000-0000-c000-000000000000",
            "resourceAccess": [
                {
                    "id": "311a71cc-e848-46a1-bdf8-97ff7156d8e6",
                    "type": "Scope"
                }
            ]
        }
    ],
```

### <a name="samlmetadataurl-attribute"></a>samlMetadataUrl атрибут

| Ключ | Тип значения |
| :--- | :--- |
| samlMetadataUrl | Строка |

URL-адрес метаданных SAML для приложения.

Пример

```json
    "samlMetadataUrl": "https://MyRegisteredAppSAMLMetadata",
```

### <a name="signinurl-attribute"></a>signInUrl атрибут

| Ключ | Тип значения |
| :--- | :--- |
| signInUrl | Строка |

Указывает URL-адрес домашней страницы приложения.

Пример

```json
    "signInUrl": "https://MyRegisteredApp",
```

### <a name="signinaudience-attribute"></a>signInAudience атрибут

| Ключ | Тип значения |
| :--- | :--- |
| signInAudience | Строка |

Указывает, какие учетные записи Майкрософт поддерживаются для текущего приложения. Поддерживаются значения:
- `AzureADMyOrg`- Пользователи с учетной записью Майкрософт или школой в арендаторе Azure AD моей организации (например, один арендатор)
- `AzureADMultipleOrgs`- Пользователи с учетной записью Майкрософт или школой в арендаторе Azure AD любой организации (например, мультитенант)
- `AzureADandPersonalMicrosoftAccount`- Пользователи с персональной учетной записью Майкрософт или рабочей или школьной учетной записью в aD-арендаторе любой организации Azure AD
- `PersonalMicrosoftAccount`- Личные аккаунты, которые используются для вхастывавот в такие сервисы, как Xbox и Skype.

Пример

```json
    "signInAudience": "AzureADandPersonalMicrosoftAccount",
```

### <a name="tags-attribute"></a>Атрибут тегов

| Ключ | Тип значения |
| :--- | :--- |
| tags | Массив строк  |

Пользовательские строки, которые могут использоваться для классификации и идентификации приложения.

Пример

```json
    "tags": [
       "ProductionApp"
    ],
```

## <a name="common-issues"></a>Распространенные проблемы

### <a name="manifest-limits"></a>Прояснять пределы

Манифест приложения имеет несколько атрибутов, которые называются коллекциями; например, appRoles, keyCredentials, известные Клиентские Приложения, идентификаторы, перенаправлениеUris, requiredResourceAccess и oauth2Permissions. В рамках полного манифеста приложения для любого приложения, общее количество записей во всех коллекциях вместе взятых была ограничена на уровне 1200. Если ранее вы указали 100 перенаправления URI в манифесте приложения, то у вас осталось только 1100 оставшихся записей для использования во всех других коллекциях вместе взятых, которые составляют манифест.

> [!NOTE]
> В случае, если вы попытаетесь добавить более 1200 записей в манифесте приложения, вы можете увидеть ошибку **"Не удалось обновить приложение xxxxxx. Детали ошибки: Размер манифеста превысил свой предел. Пожалуйста, уменьшите количество значений и повторно попробуйте свой запрос»...**

### <a name="unsupported-attributes"></a>Неподдерживаемые атрибуты

Манифест приложения представляет собой схему базовой модели приложения в Azure AD. По мере развития основной схемы редактор манифеста будет время от времени обновляться с учетом новой схемы. В результате вы можете заметить новые атрибуты, появляющиеся в манифесте приложения. В редких случаях вы можете заметить синтаксические или семантические изменения в существующих атрибутах или вы можете обнаружить атрибут, который существовал ранее, больше не поддерживается. Например, в [регистрации приложений](https://go.microsoft.com/fwlink/?linkid=2083908)будут увидеться новые атрибуты, которые известны с другим именем в опыте регистрации приложений (Legacy).

| Регистрация приложений (Наследие)| Регистрация приложений           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Для описания этих атрибутов [см.](#manifest-reference)

При попытке загрузить ранее загруженный манифест можно увидеть одну из следующих ошибок. Эта ошибка, вероятно, потому, что редактор манифеста теперь поддерживает более новую версию схемы, которая не совпадает с той, которую вы пытаетесь загрузить.

* "Не удалось обновить приложение xxxxxx. Ошибка: Идентификатор недействительных объектов 'неопределенный'. []."
* "Не удалось обновить приложение xxxxxx. Детали ошибки: Одна или несколько указанных значений свойств недействительны. []."
* "Не удалось обновить приложение xxxxxx. Детали ошибки: Не разрешается устанавливать доступныеToOtherTenants в этой версии api для обновления. []."
* "Не удалось обновить приложение xxxxxx. Детали ошибки: Обновления в свойстве 'replyUrls' не допускаются для этого приложения. Вместо этого используйте свойство 'replyUrlsWithType'. []."
* "Не удалось обновить приложение xxxxxx. Деталь ошибки: Было найдено значение без имени типа и ожидаемый тип недоступен. Когда модель указана, каждое значение в полезной нагрузке должно иметь тип, который может быть указан в полезной нагрузке, явно вызывая или косвенно выведенный из родительского значения. []"

Когда вы видите одну из этих ошибок, мы рекомендуем следующие действия:

1. Отредить атрибуты индивидуально в редакторе манифеста вместо загрузки ранее загруженного манифеста. Используйте [таблицу ссылок,](#manifest-reference) чтобы понять синтаксис и семантику старых и новых атрибутов, чтобы можно было успешно отсеивать интересующие вас атрибуты. 
1. Если ваш рабочий процесс требует сохранения манифестов в исходном репозитории для последующего использования, мы предлагаем перебазировать сохраненные манифесты в репозитории с тем, который вы видите в опыте **регистрации приложений.**

## <a name="next-steps"></a>Дальнейшие действия

* Для получения дополнительной информации о взаимосвязи между приложением приложения [Application and service principal objects in Azure AD](app-objects-and-service-principals.md)и основным объектом службы см.
* Ознакомиться с [глоссарием разработчика платформы идентификации Майкрософт](developer-glossary.md) можно для определения некоторых основных концепций разработчика идентификационных платформ Майкрософт.

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
