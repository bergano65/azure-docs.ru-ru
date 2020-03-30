---
title: Понимание схемы Azure AD и пользовательских выражений
description: В этой статье описывается схема Azure AD, атрибуты, в которых течет агент подготовки, и пользовательские выражения.
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
ms.date: 02/18/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ac09fb3faf55be6c07a1e0a88b6e2032c9ab8ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299335"
---
# <a name="understand-the-azure-ad-schema"></a>Понять схему Azure AD
Объект в Active Directory Azure (Azure AD), как и любой каталог, представляет собой программную конструкцию данных высокого уровня, представляющую такие вещи, как пользователи, группы и контакты. При создании нового пользователя или контакта в Azure AD создается новый экземпляр этого объекта. Эти экземпляры могут быть дифференцированы в зависимости от их свойств.

Свойства в Azure AD являются элементами, ответственными за хранение информации о экземпляре объекта в Azure AD.

Схема Azure AD определяет правила, для которых свойства могут использоваться в записи, типы значений, которые могут иметь эти свойства, и то, как пользователи могут взаимодействовать с этими значениями. 

Azure AD имеет два типа свойств:
- **Встроенные свойства:** Свойства, предопределенные схемой Azure AD. Эти свойства обеспечивают различное использование и могут быть доступны или недоступны.
- **Расширения каталога:** Свойства, которые предоставляются так, что вы можете настроить Azure AD для собственного использования. Например, если вы расширили свой предпосылок Active Directory с определенным атрибутом и хотите протекать этим атрибутом, можно использовать одно из предоставленных пользовательских свойств. 

## <a name="attributes-and-expressions"></a>Атрибуты и выражения
Когда объект, например пользователь, приготовлен к Azure AD, создается новый экземпляр объекта пользователя. Это творение включает в себя свойства этого объекта, которые также известны как атрибуты. Первоначально вновь созданный объект имеет свои атрибуты, установленные значениями, которые определяются правилами синхронизации. Эти атрибуты затем обновляются с помощью агента подготовки облака.

![Подготовка объектов](media/concept-attributes/attribute1.png)

Например, пользователь может быть частью отдела маркетинга. Атрибут отдела Azure AD первоначально создается, когда они подготовлены, и значение настроено на маркетинг. Шесть месяцев спустя, если они переоделись на продажи, их атрибут отдела активного каталога в помещениях будет изменен на Sales. Это изменение синхронизируется с Azure AD и отражено в их объекте пользователя Azure AD.

Синхронизация атрибутов может быть прямой, если значение в Azure AD непосредственно устанавливается значением атрибута на месте. Или программное выражение может обрабатывать синхронизацию. Программное выражение необходимо в тех случаях, когда для заполнения значения необходимо логическое выражение.

Например, если у васjohn.smith@contoso.comесть атрибут почты "" и необходимо лишить "@contoso.comчасть и поток только значение "john.smith", вы бы использовать что-то вроде этого:

`Replace([mail], "@contoso.com", , ,"", ,)`  

**Пример ввода и вывода:** <br>

* **Входные данные** (mail): "john.smith@contoso.com"
* **OUTPUT**: "john.smith"

Для получения дополнительной информации о том, как [Writing expressions for attribute mappings in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)писать пользовательские выражения и синтаксис, см.

В следующей таблице перечислены общие атрибуты и способ их синхронизации с Azure AD.


|Локальная служба Active Directory|Тип картирования|Azure AD|
|-----|-----|-----|
|cn|Прямой доступ|commonName
|countryCode|Прямой доступ|countryCode|
|displayName|Прямой доступ|displayName|
|givenName|Выражение|givenName|
|objectGUID|Прямой доступ|источникЯЯЯЯЯЯДвоинная|  
|userprincipalName|Прямой доступ|userPrincipalName|
|ПроксиАдресс|Прямой доступ|ProxyAddress|

## <a name="view-the-schema"></a>Просмотр схемы
> [!WARNING]
> Конфигурация подготовки облака создает основную службу. Директор службы виден на портале Azure. Не следует изменять отображение атрибутов, используя основной опыт службы на портале Azure.  Такой режим работы не поддерживается.

Чтобы просмотреть схему и проверить ее, выполните следующие действия.

1.  Перейти к [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).
1.  Вопийте с учетной записью глобального администратора.
1.  Слева выберите **изменение разрешений** и убедитесь, что **каталог.ReadWrite.All** *согласован.*
1.  Выполнить запрос `https://graph.microsoft.com/beta/serviceprincipals/?$filter=startswith(Displayname,'Active')`. Этот запрос возвращает отфильтрованный список директоров служб.
1.  Найдите `"appDisplayName": "Active Directory to Azure Active Directory Provisioning"` и обратите `"id"`внимание на значение.
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
1. Замените `{Service Principal id}` значение и запустите `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/`запрос.
1. Найдите `"id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976"` и обратите `"id"`внимание на значение.
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
1. Теперь запустите `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal Id}/synchronization/jobs/{AD2AAD Provisioning id}/schema`запрос.
 
    Например, https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema.

   `{Service Principal Id}` Замените `{AD2ADD Provisioning Id}` и с вашими значениями.

1. Этот запрос возвращает схему.

   ![Возвращалась схема](media/concept-attributes/schema1.png)
 
## <a name="next-steps"></a>Дальнейшие действия

- [Что собой представляет подготовка?](what-is-provisioning.md)
- [Что такое подготовка облака Azure AD Connect?](what-is-cloud-provisioning.md)
