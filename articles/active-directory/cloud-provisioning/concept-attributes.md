---
title: Основные сведения о схеме Azure AD и пользовательских выражениях
description: В этом разделе описывается схема Azure AD, атрибуты, которые проходят агент подготовки, и пользовательские выражения.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eae594bcc20e3c4ed1c6fbd0333699de8c9f4452
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794501"
---
# <a name="understanding-the-azure-ad-schema"></a>Основные сведения о схеме Azure AD
Объект в Azure AD, как и любой каталог, представляет собой программную конструкцию высокого уровня данных, которая представляет такие вещи, как пользователи, группы и контакты.  При создании нового пользователя или контакта в Azure AD создается новый экземпляр этого объекта.  Эти экземпляры можно отличить в зависимости от их свойств.

Свойства в Azure AD — это элементы, отвечающие за хранение сведений об экземпляре объекта в Azure AD.  

Схема Azure AD определяет правила, для которых свойства могут использоваться в записи, типы значений, которые могут иметь эти свойства, и как пользователи могут взаимодействовать с этими значениями. 

В Azure AD есть два типа свойств.  Существуют следующие свойства.
- **Встроенные свойства** — свойства, предварительно определенные в схеме Azure AD.  Эти свойства предоставляют различные варианты использования и могут быть недоступны.
- **Расширения каталогов** — предоставляемые свойства, которые позволяют настроить Azure AD для собственного использования.  Например, если вы расширили локальную Active Directory с определенным атрибутом и хотите поработать с этим атрибутом, можно использовать одно из предоставленных пользовательских свойств. 

## <a name="attributes-and-expressions"></a>Атрибуты и выражения
Когда объект, например пользователь, подготавливается к Azure AD, создается новый экземпляр объекта пользователя.  Это создание включает свойства этого объекта, которые также называются атрибутами.  Изначально созданный объект будет иметь атрибуты, заданные в качестве значений, определяемых правилами синхронизации.  Эти атрибуты затем сохраняются в актуальном виде с помощью агента подготовки облака.

![](media/concept-attributes/attribute1.png)

Например, если пользователь является частью отдела маркетинга, его атрибут "Отдел" Azure AD изначально будет создан при подготовке, а затем значение будет установлено в "маркетинг".  Но затем, через шесть месяцев, они меняются на Sales.  Атрибут «отдел AD» в локальной среде изменится на Sales.  Затем это изменение будет синхронизироваться с Azure AD и будет отражено в объекте пользователя Azure AD.

Синхронизация атрибутов может быть либо прямой, где значение в Azure AD напрямую задано значением атрибута локальный.  Или может существовать программное выражение, которое обрабатывает эту синхронизацию.  Программное выражение требуется в тех случаях, когда для заполнения значения требуется определенная логика или определение.

Например, если я имел атрибут mail («john.smith@contoso.com») и хотел бы выпустить часть «@contoso.com» и получит только значение «Джон. Иванов», я бы использовал примерно следующий текст:

`Replace([mail], "@contoso.com", , ,"", ,)`  

**Пример ввода и вывода:** <br>

* **Входные данные** (mail): "john.smith@contoso.com"
* **Выходные данные**: John. Smith

Дополнительные сведения о написании пользовательских выражений и синтаксисе см. в разделе [Написание выражений для сопоставления атрибутов в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data).

Ниже перечислены общие атрибуты и способы их синхронизации с Azure AD.


|Локальная служба Active Directory|Тип сопоставления|Azure AD|
|-----|-----|-----|
|cn|Direct|commonName
|countryCode|Direct|countryCode|
|displayName|Direct|displayName|
|givenName|Expression|givenName|
|objectGUID|Direct|sourceAnchorBinary|  
|userprincipalName|Direct|userPrincipalName|
|проксядресс|Direct|ProxyAddress|

## <a name="viewing-the-schema"></a>Просмотр схемы
Чтобы просмотреть схему и проверить ее, выполните следующие действия.

1.  Перейдите в [проводник Graph](https://developer.microsoft.com/graph/graph-explorer).
2.  Вход с помощью учетной записи глобального администратора
3.  В левой части экрана щелкните **изменить разрешения** и убедитесь, что параметр **Directory. ReadWrite. ALL** имеет значение.
4.  Выполните следующий запрос: https://graph.microsoft.com/beta/serviceprincipals/.  Этот запрос вернет список субъектов-служб.
5.  Найдите "appDisplayName": "Active Directory для Azure Active Directory подготовки" и запишите значение "ID:".
    ```
    "value": [
            {
                "id": "00d41b14-7958-45ad-9d75-d52fa29e02a1",
                "deletedDateTime": null,
                "accountEnabled": true,
                "appDisplayName": "Active Directory to Azure Active Directory Provisioning",
                "appId": "1a4721b3-e57f-4451-ae87-ef078703ec94",
                "applicationTemplateId": null,
                "appOwnerOrganizationId": "47df5bb7-e6bc-4256-afb0-dd8c8e3c1ce8",
                "appRoleAssignmentRequired": false,
                "displayName": "Active Directory to Azure Active Directory Provisioning",
                "errorUrl": null,
                "homepage": "https://account.activedirectory.windowsazure.com:444/applications/default.aspx?metadata=AD2AADProvisioning|ISV9.1|primary|z",
                "loginUrl": null,
                "logoutUrl": null,
                "notificationEmailAddresses": [],
                "preferredSingleSignOnMode": null,
                "preferredTokenSigningKeyEndDateTime": null,
                "preferredTokenSigningKeyThumbprint": null,
                "publisherName": "Active Directory Application Registry",
                "replyUrls": [],
                "samlMetadataUrl": null,
                "samlSingleSignOnSettings": null,
                "servicePrincipalNames": [
                    "http://adapplicationregistry.onmicrosoft.com/adprovisioningtoaad/primary",
                    "1a4721b3-e57f-4451-ae87-ef078703ec94"
                ],
                "signInAudience": "AzureADMultipleOrgs",
                "tags": [
                    "WindowsAzureActiveDirectoryIntegratedApp"
                ],
                "addIns": [],
                "api": {
                    "resourceSpecificApplicationPermissions": []
                },
                "appRoles": [
                    {
                        "allowedMemberTypes": [
                            "User"
                        ],
                        "description": "msiam_access",
                        "displayName": "msiam_access",
                        "id": "a0326856-1f51-4311-8ae7-a034d168eedf",
                        "isEnabled": true,
                        "origin": "Application",
                        "value": null
                    }
                ],
                "info": {
                    "termsOfServiceUrl": null,
                    "supportUrl": null,
                    "privacyStatementUrl": null,
                    "marketingUrl": null,
                    "logoUrl": null
                },
                "keyCredentials": [],
                "publishedPermissionScopes": [
                    {
                        "adminConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on behalf of the signed-in user.",
                        "adminConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "id": "d40ed463-646c-4efe-bb3e-3fa7d0006688",
                        "isEnabled": true,
                        "type": "User",
                        "userConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on your behalf.",
                        "userConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "value": "user_impersonation"
                    }
                ],
                "passwordCredentials": []
            },
    ```
6. Замените {Service Principal ID} значением и выполните следующий запрос: `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/`
7. Найдите раздел "ID": "AD2AADProvisioning. fd1c9b9e8077402c8bc03a7186c8f976" и запишите идентификатор "ID:".
    ```
    {
                "id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976",
                "templateId": "AD2AADProvisioning",
                "schedule": {
                    "expiration": null,
                    "interval": "PT2M",
                    "state": "Active"
                },
                "status": {
                    "countSuccessiveCompleteFailures": 0,
                    "escrowsPruned": false,
                    "code": "Active",
                    "lastSuccessfulExecutionWithExports": null,
                    "quarantine": null,
                    "steadyStateFirstAchievedTime": "2019-11-08T15:48:05.7360238Z",
                    "steadyStateLastAchievedTime": "2019-11-20T16:17:24.7957721Z",
                    "troubleshootingUrl": "",
                    "lastExecution": {
                        "activityIdentifier": "2dea06a7-2960-420d-931e-f6c807ebda24",
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 15,
                        "countEscrowedRaw": 15,
                        "countExported": 0,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "2019-11-20T16:15:21.116098Z",
                        "timeEnded": "2019-11-20T16:17:24.7488681Z"
                    },
                    "lastSuccessfulExecution": {
                        "activityIdentifier": null,
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 0,
                        "countEscrowedRaw": 0,
                        "countExported": 5,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "0001-01-01T00:00:00Z",
                        "timeEnded": "2019-11-20T14:09:46.8855027Z"
                    },
                    "progress": [],
                    "synchronizedEntryCountByType": [
                        {
                            "key": "group to Group",
                            "value": 33
                        },
                        {
                            "key": "user to User",
                            "value": 3
                        }
                    ]
                },
                "synchronizationJobSettings": [
                    {
                        "name": "Domain",
                        "value": "{\"DomainFQDN\":\"contoso.com\",\"DomainNetBios\":\"CONTOSO\",\"ForestFQDN\":\"contoso.com\",\"ForestNetBios\":\"CONTOSO\"}"
                    },
                    {
                        "name": "DomainFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "DomainNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "ForestFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "ForestNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "QuarantineTooManyDeletesThreshold",
                        "value": "500"
                    }
                ]
            }
    ```
8. Теперь выполните следующий запрос: `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal Id}/synchronization/jobs/{AD2AAD Provisioning id}/schema`
 
    Пример: https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema

 Замените {Service Principal ID} и {AD2ADD ИД подготовки} значениями.

9. Этот запрос возвратит схему.
  ![](media/concept-attributes/schema1.png)
 
## <a name="next-steps"></a>Дальнейшие действия 

- [Что такое подготовка?](what-is-provisioning.md)
- [Что такое Azure AD Connect подготовки облака?](what-is-cloud-provisioning.md)
