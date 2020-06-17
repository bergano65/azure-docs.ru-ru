---
title: Настройка единого входа на основе SAML с помощью API Microsoft Graph
titleSuffix: Azure Active Directory
description: Вам нужно настроить единый вход на основе SAML для нескольких экземпляров одного приложения? Узнайте, как сэкономить время, автоматизировав настройку единого входа на основе SAML при помощи API Microsoft Graph.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
ms.reviewer: luleon
ms.openlocfilehash: fd59dcdd566110d1df02333f5701c0c206442d5d
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83846466"
---
# <a name="automate-saml-based-sso-app-configuration-with-microsoft-graph-api"></a>Автоматизация настройки единого входа на основе SAML для приложений с помощью API Microsoft Graph

В этой статье рассказывается, как создать приложение из коллекции Azure Active Directory (Azure AD) и настроить его. В этой статье в качестве примера используется AWS, но описанные в этой статье шаги можно применить для любого другого приложения на основе SAML, включенного в коллекцию Azure AD.

**Шаги по автоматизации настройки единого входа на основе SAML с помощью API Microsoft Graph**

| Шаг  | Сведения  |
|---------|---------|
| [1. Создание приложения из коллекции](#step-1-create-the-gallery-application) | Вход в клиент API <br> Получение приложения из коллекции <br> Создание приложения из коллекции|
| [2. Настройка единого входа](#step-2-configure-single-sign-on) | Получение идентификаторов объектов для приложения и субъекта-службы <br> Настройка режима единого входа <br> Настройка основных URL-адресов SAML, таких как идентификатор, URL-адрес ответа и URL-адрес входа <br> Добавление ролей приложения (необязательно)|
| [3. Настройка сопоставления утверждений](#step-3-configure-claims-mapping) | Создание политики сопоставления утверждений <br> Назначение политики сопоставления утверждений субъекту-службе|
| [4. Настройка сертификата для подписи](#step-4-configure-signing-certificate) | Создание сертификата <BR> Добавление пользовательского ключа подписывания <br> Активация пользовательского ключа подписывания|
| [5. Назначение пользователей](#step-5-assign-users) | Назначение пользователей и групп для приложения
| [6. Настройка на стороне приложения](#step-6-configure-the-application-side)| Получение метаданных SAML из Azure AD

**Список всех API, используемых в этой документации**

Убедитесь, что у вас есть необходимые разрешения для вызова следующих API.

|Тип ресурса |Метод |
|---------|---------|
|[applicationTemplate](https://docs.microsoft.com/graph/api/resources/applicationtemplate?view=graph-rest-beta)|[List applicationTemplate](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) <br>[Instantiate applicationTemplate](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http)|
|[servicePrincipals](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta)|[Update servicePrincipal](https://docs.microsoft.com/graph/api/serviceprincipal-update?view=graph-rest-beta&tabs=http) <br> [Create appRoleAssignments](https://docs.microsoft.com/graph/api/serviceprincipal-post-approleassignments?view=graph-rest-beta&tabs=http) <br> [Assign claimsMappingPolicies](https://docs.microsoft.com/graph/api/serviceprincipal-post-claimsmappingpolicies?view=graph-rest-beta&tabs=http)|
|[applications](https://docs.microsoft.com/graph/api/resources/application?view=graph-rest-1.0)|[Update application](https://docs.microsoft.com/graph/api/application-update?view=graph-rest-beta&tabs=http)|
|[claimsMappingPolicy](https://docs.microsoft.com/graph/api/resources/claimsmappingpolicy?view=graph-rest-beta)| [Create claimsMappingPolicy](https://docs.microsoft.com/graph/api/claimsmappingpolicy-post-claimsmappingpolicies?view=graph-rest-beta&tabs=http)

>[!NOTE]
>Объекты ответа, приведенные в этой статье, могут быть сокращены для удобства чтения. В реальном вызове возвращаются все свойства.

## <a name="step-1-create-the-gallery-application"></a>Шаг 1. Создание приложения из коллекции

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>Войдите в Microsoft Graph Explorer (рекомендуется), Postman или любой другой клиент API на ваш выбор.

1. Запустите [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).
2. Выберите вариант **Вход с помощью учетной записи Майкрософт** и войдите, используя учетные данные глобального администратора Azure AD или администратора приложения.
3. После успешного входа вы увидите данные учетной записи пользователя на панели слева.

### <a name="retrieve-the-gallery-application-template-identifier"></a>Получение идентификатора шаблона для приложения в коллекции

У каждого приложения в коллекции приложений Azure AD есть свой [шаблон](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http), который описывает метаданные для этого приложения. По этому шаблону вы можете создать экземпляр приложения и субъект-службу в клиенте для управления им.

#### <a name="request"></a>Запрос

<!-- {
  "blockType": "request",
  "name": "get_applicationtemplates"
}-->

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates
```

#### <a name="response"></a>Ответ

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationTemplate",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [
  {
    "id": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "Amazon Web Services (AWS)",
        "homePageUrl": "http://aws.amazon.com/",
        "supportedSingleSignOnModes": [
             "password",
             "saml",
             "external"
         ],
         "supportedProvisioningTypes": [
             "sync"
         ],
         "logoUrl": "https://az495088.vo.msecnd.net/app-logo/aws_215.png",
         "categories": [
             "developerServices"
         ],
         "publisher": "Amazon",
         "description": null    
  
}
```

### <a name="create-the-gallery-application"></a>Создание приложения из коллекции

Используя идентификатор шаблона, который вы получили для приложения на предыдущем шаге, [создайте экземпляр](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http) приложения и субъект-службу в клиенте.

#### <a name="request"></a>Запрос

<!-- {
  "blockType": "request",
  "name": "applicationtemplate_instantiate"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/beta/applicationTemplates/8b1025e4-1dd2-430b-a150-2ef79cd700f5/instantiate
Content-type: application/json

{
  "displayName": "AWS Contoso"
}
```

#### <a name="response"></a>Ответ


<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationServicePrincipal"
} -->

```http
HTTP/1.1 201 OK
Content-type: application/json


{
    "application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63",
        "appId": "92653dd4-aa3a-3323-80cf-e8cfefcc8d5d",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Contoso",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "logoutUrl": null,
        "samlMetadataUrl": null,
    },
    "servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e",
        "appDisplayName": "AWS Contoso",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "appRoleAssignmentRequired": true,
        "displayName": "My custom name",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "servicePrincipalNames": [
            "93653dd4-aa3a-4323-80cf-e8cfefcc8d7d"
        ],
        "tags": [
            "WindowsAzureActiveDirectoryIntegratedApp"
        ],
    }
}
```

## <a name="step-2-configure-single-sign-on"></a>Шаг 2. Настройка единого входа

### <a name="retrieve-app-object-id-and-service-principal-object-id"></a>Получение идентификаторов объектов для приложения и субъекта-службы

Из ответа на предыдущий вызов извлеките и сохраните идентификаторы объекта для приложения и субъекта-службы.

```
"application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63"
}
"servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e"
}
```
### <a name="set-single-sign-on-mode"></a>Настройка режима единого входа

В этом примере вы настроите режим единого входа `saml` для [типа ресурса servicePrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta). Также вы можете настроить другие свойства единого входа SAML: `notificationEmailAddresses`, `loginUrl` и `samlSingleSignOnSettings.relayState`.

#### <a name="request"></a>Запрос

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/beta/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e
Content-type: servicePrincipal/json

{
    "preferredSingleSignOnMode": "saml",
    "notificationEmailAddresses": [
        "john@contoso.com"
      ]
}
```

#### <a name="response"></a>Ответ

<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 204
```

### <a name="set-basic-saml-urls-such-as-identifier-reply-url-sign-on-url"></a>Настройка основных URL-адресов SAML, таких как идентификатор, URL-адрес ответа и URL-адрес входа

Задайте идентификатор и URL-адреса ответа для AWS в объекте приложения.

#### <a name="request"></a>Запрос

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/beta/applications/cbc071a6-0fa5-4859-8g55-e983ef63df63
Content-type: applications/json

{
    "web": {
        "redirectUris": [
            "https://signin.aws.amazon.com/saml"
        ] 
    },
    "identifierUris": [
        "https://signin.aws.amazon.com/saml"
    ]    
}
```
#### <a name="response"></a>Ответ

<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 204
```
### <a name="add-app-roles-optional"></a>Добавление ролей приложения (необязательно)

Если приложение ожидает получить в токене сведения о роли, добавьте определение ролей в объект приложения. Для AWS вы можете [включить подготовку пользователя](../app-provisioning/application-provisioning-configure-api.md), чтобы автоматически получать все роли из учетной записи AWS. 

Дополнительные сведения см. в статье о [настройке утверждения роли, выдаваемого в токене SAML](../develop/active-directory-enterprise-app-role-management.md).

> [!NOTE] 
> При добавлении ролей приложения не изменяйте стандартные роли приложения (msiam_access). 

#### <a name="request"></a>Запрос

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/beta/serviceprincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e
Content-type: serviceprincipals/json

{
"appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "454dc4c2-8176-498e-99df-8c4efcde41ef",
            "isEnabled": true,
            "value": "arn:aws:iam::212743507312:role/accountname-aws-admin,arn:aws:iam::212743507312:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Finance,WAAD",
            "displayName": "Finance,WAAD",
            "id": "8642d5fa-18a3-4245-ab8c-a96000c1a217",
            "isEnabled": true,
            "value": "arn:aws:iam::212743507312:role/accountname-aws-finance,arn:aws:iam::212743507312:saml-provider/WAAD"
        }
    ]

}
```

#### <a name="response"></a>Ответ

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

## <a name="step-3-configure-claims-mapping"></a>Шаг 3. Настройка сопоставления утверждений

### <a name="create-claims-mapping-policy"></a>Создание политики сопоставления утверждений

В дополнение к основным утверждениям настройте следующие, чтобы Azure AD включала их в токен SAML.

| Имя утверждения | Источник  |
|---------|---------|
| `https://aws.amazon.com/SAML/Attributes/Role` | assignedroles| 
| `https://aws.amazon.com/SAML/Attributes/RoleSessionName` | userprincipalname |
| `https://aws.amazon.com/SAML/Attributes/SessionDuration` | "900" |
| roles | assignedroles |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` | userprincipalname |

#### <a name="request"></a>Запрос

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/beta/policies/claimsMappingPolicies
Content-type: claimsMappingPolicies/json

{
    "definition":[
            "{\"ClaimsMappingPolicy\": {
                \"Version\": 1,
                \"IncludeBasicClaimSet\": \"true\",
                \"ClaimsSchema\": [
                    {
                        \"Source\": \"user\",
                        \"ID\": \"assignedroles\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/Role\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"userprincipalname\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/RoleSessionName\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"900\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/SessionDuration\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"assignedroles\",
                        \"SamlClaimType\": \"appRoles\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"userprincipalname\",
                        \"SamlClaimType\": \"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\"
                    }
                ]
            }
        }"

    ],
    "displayName": "AWS Claims policy",
    "isOrganizationDefault": false
}
```

#### <a name="response"></a>Ответ

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.claimsMappingPolicies",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: claimsMappingPolicies/json

{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#policies/claimsMappingPolicies/$entity",
    "id": "6b33aa8e-51f3-41a6-a0fd-d660d276197a",
    "definition": [
        "{\"ClaimsMappingPolicy\": {\"Version\": 1,\"IncludeBasicClaimSet\": \"true\",\"ClaimsSchema\": [{\"Source\": \"user\",\"ID\": \"assignedroles\",\"SamlClaimType\":\"https://aws.amazon.com/SAML/Attributes/Role\"\r\n                    },{\"Source\": \"user\",\"ID\": \"userprincipalname\",\"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/RoleSessionName\"},{\"Source\": \"user\",\"ID\": \"900\",\"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/SessionDuration\"},{\"Source\": \"user\",\"ID\": \"assignedroles\",\"SamlClaimType\":\"appRoles\"},{\"Source\": \"user\",\"ID\": \"userprincipalname\",\"SamlClaimType\": \"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\"}]}}"
    ],
    "displayName": "AWS Claims policy",
    "isOrganizationDefault": false
}
```

### <a name="assign-claims-mapping-policy-to-service-principal"></a>Назначение политики сопоставления утверждений субъекту-службе

#### <a name="request"></a>Запрос

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e/claimsMappingPolicies/$ref

Content-type: claimsMappingPolicies/json

{
  "@odata.id":"https://graph.microsoft.com/beta/policies/claimsMappingPolicies/6b33aa8e-51f3-41a6-a0fd-d660d276197a"
}
```

#### <a name="response"></a>Ответ

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

## <a name="step-4-configure-signing-certificate"></a>Шаг 4. Настройка сертификата для подписи

При использовании API [applicationTemplate](https://docs.microsoft.com/graph/api/resources/applicationtemplate?view=graph-rest-beta) сертификат для подписи не создается автоматически. Вам следует создать пользовательский сертификат для подписи и назначить его приложению самостоятельно. 

### <a name="create-a-custom-signing-certificate"></a>Создание пользовательского сертификата для подписи

Для целей тестирования можно создать самозаверяющий сертификат с помощью приведенных ниже команд PowerShell. При создании сертификата для подписи для рабочей среды примените все самые передовые практики безопасности, используемые в вашей компании.

```powershell
Param(
    [Parameter(Mandatory=$true)]
    [string]$fqdn,
    [Parameter(Mandatory=$true)]
    [string]$pwd,
    [Parameter(Mandatory=$true)]
    [string]$location
) 

if (!$PSBoundParameters.ContainsKey('location'))
{
    $location = "."
} 

$cert = New-SelfSignedCertificate -certstorelocation cert:\currentuser\my -DnsName $fqdn
$pwdSecure = ConvertTo-SecureString -String $pwd -Force -AsPlainText
$path = 'cert:\currentuser\my\' + $cert.Thumbprint
$cerFile = $location + "\\" + $fqdn + ".cer"
$pfxFile = $location + "\\" + $fqdn + ".pfx" 

Export-PfxCertificate -cert $path -FilePath $pfxFile -Password $pwdSecure
Export-Certificate -cert $path -FilePath $cerFile
```

### <a name="add-a-custom-signing-key"></a>Добавление пользовательского ключа подписывания

Добавьте следующие сведения в субъект-службу.

* Закрытый ключ
* Пароль
* Открытый ключ 

Извлеките закрытый и открытый ключи в кодировке Base64 из PFX-файла. Дополнительные сведения об их свойствах см. в [описании типа ресурса keyCredential](https://docs.microsoft.com/graph/api/resources/keycredential?view=graph-rest-1.0).

Убедитесь, что значение keyId для keyCredential, для которого указано назначение Sign (подпись), соответствует значению keyId для passwordCredential.

Вы можете создать `customkeyIdentifier`, получив хэш отпечатка сертификата.

```csharp
  public string GetSha256FromThumbprint(string thumbprint)
  {
      var message = Encoding.ASCII.GetBytes(thumbprint);
      SHA256Managed hashString = new SHA256Managed();
      return Convert.ToBase64String(hashString.ComputeHash(message));
  }
```

#### <a name="request"></a>Запрос

> [!NOTE]
> Значение key в свойстве keyCredentials сокращено для удобства чтения.

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
PATCH https://graph.microsoft.com/beta/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json

{
    "keyCredentials":[
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "AsymmetricX509Cert",
            "usage": "Sign",
            "key":"MIIKIAIBAz.....HBgUrDgMCERE20nuTptI9MEFCh2Ih2jaaLZBZGeZBRFVNXeZmAAgIH0A==",
            "displayName": "CN=awsAPI"
        },
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "e35a7d11-fef0-49ad-9f3e-aacbe0a42c42",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "key": "MIIDJzCCAg+gAw......CTxQvJ/zN3bafeesMSueR83hlCSyg==",
            "displayName": "CN=awsAPI"
        }

    ],
    "passwordCredentials": [
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "endDateTime": "2022-01-27T19:40:33Z",
            "startDateTime": "2020-04-20T19:40:33Z",
            "secretText": "61891f4ee44d"
        }
    ]
}
```

#### <a name="response"></a>Ответ

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

### <a name="activate-the-custom-signing-key"></a>Активация пользовательского ключа подписывания

Вам нужно задать для свойства `preferredTokenSigningKeyThumbprint` значение отпечатка сертификата, с которым Azure AD будет подписывать ответ SAML. 

#### <a name="request"></a>Запрос

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
PATCH https://graph.microsoft.com/beta/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json

{
    "preferredTokenSigningKeyThumbprint": "AC09FEF18DDE6983EE2A164FBA3C4DD7518BD787"
}
```

#### <a name="response"></a>Ответ

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```
## <a name="step-5-assign-users"></a>Шаг 5. Назначение пользователей

### <a name="assign-users-and-groups-to-the-application"></a>Назначение пользователей и групп для приложения

Назначьте субъекту-службе следующего пользователя и роль AWS_Role1. 

| Имя  | ID  |
|---------|---------|
| Идентификатор пользователя (principalId) | 6cad4079-4e79-4a3f-9efb-ea30a14bdb26 |
| Тип (principalType) | Пользователь |
| Идентификатор роли приложения (appRoleId) | 454dc4c2-8176-498e-99df-8c4efcde41ef |
| servicePrincipalID (resourceId) | 515f62cb-d18a-4dca-bec3-bb0bf31deeea |

#### <a name="request"></a>Запрос

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e/appRoleAssignments

Content-type: appRoleAssignments/json

{
  "principalId": "6cad4079-4e79-4a3f-9efb-ea30a14bdb26",
  "principalType": "User",
  "appRoleId":"454dc4c2-8176-498e-99df-8c4efcde41ef",
  "resourceId":"515f62cb-d18a-4dca-bec3-bb0bf31deeea"
}
```
#### <a name="response"></a>Ответ

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 201 
Content-type: appRoleAssignments/json

{
    "id": "rq7hyzl4yECaNZleMrTpDV-OCe5TEl5Ao_o76XMrRFU",
    "creationTimestamp": "2020-04-23T17:38:13.2508567Z",
    "appRoleId": "454dc4c2-8176-498e-99df-8c4efcde41ef",
    "principalDisplayName": "User 1",
    "principalId": "6cad4079-4e79-4a3f-9efb-ea30a14bdb26",
    "principalType": "User",
    "resourceDisplayName": "AWS API Created",
    "resourceId": "515f62cb-d18a-4dca-bec3-bb0bf31deeea"
}
```

Дополнительные сведения см. в документации по типу ресурса [appRoleAssignment](https://docs.microsoft.com/graph/api/resources/approleassignment?view=graph-rest-beta).

## <a name="step-6-configure-the-application-side"></a>Шаг 6. Настройка на стороне приложения

### <a name="get-azure-ad-saml-metadata"></a>Получение метаданных SAML из Azure AD

Используйте следующий URL-адрес, чтобы получить метаданные SAML из Azure AD для конкретного настроенного приложения. Эти метаданные содержат такие сведения, как сертификат для подписи, идентификатор сущности Azure AD (entityID), служба единого входа Azure AD (SingleSignOnService) и т. п.

https://login.microsoftonline.com/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={app-id}

## <a name="next-steps"></a>Дальнейшие действия
- [Настройка подготовки пользователей с помощью API Microsoft Graph](../app-provisioning/application-provisioning-configure-api.md)
- [Использование отчета о действиях приложения AD FS для миграции приложений в Azure AD](migrate-adfs-application-activity.md)
