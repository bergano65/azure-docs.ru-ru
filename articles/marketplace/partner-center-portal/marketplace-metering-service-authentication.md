---
title: Стратегии проверки подлинности службы измерения Marketplace | Azure Marketplace
description: Стратегии проверки подлинности службы контроля использования, поддерживаемые в Azure Marketplace.
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/03/2020
ms.openlocfilehash: 31b9d4d57e38adcd079082a4f32770c4cbc8fbb3
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2020
ms.locfileid: "82736205"
---
# <a name="marketplace-metering-service-authentication-strategies"></a>Стратегии проверки подлинности службы измерения Marketplace

Служба измерения Azure Marketplace поддерживает две стратегии проверки подлинности:

* [Маркер безопасности Azure AD](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
* [управляемые удостоверения](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 

Мы объясним, когда и как использовать различные стратегии проверки подлинности для безопасной отправки пользовательских счетчиков с помощью службы измерения Marketplace.

## <a name="using-the-azure-ad-security-token"></a>Использование маркера безопасности Azure AD

Применимые типы предложений: SaaS и приложения Azure с типом плана управляемого приложения.  

Отправка пользовательских счетчиков с помощью предопределенного фиксированного идентификатора приложения для проверки подлинности.

Для предложений SaaS Azure AD является единственным доступным вариантом.

Для приложений Azure с управляемым планом приложений следует использовать эту стратегию в следующих случаях.

* У вас уже есть механизм взаимодействия с серверными службами и вы хотите расширить этот механизм для создания пользовательских счетчиков из Центральной службы.
* У вас есть сложная логика пользовательских измерительных приборов.  Запустите эту логику в центральном расположении, а не на ресурсах управляемого приложения.

После регистрации приложения можно программно запросить маркер безопасности Azure AD. Издатель должен использовать этот маркер и выполнить запрос на его разрешение.

Дополнительные сведения об этих токенах см. в разделе [Azure Active Directory доступа к маркерам](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).

### <a name="get-a-token-based-on-the-azure-ad-app"></a>Получение токена для приложения Azure AD

#### <a name="http-method"></a>Метод HTTP

**Поместить**

#### <a name="request-url"></a>*Request URL (URL-адрес запроса)*

**`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`**

#### <a name="uri-parameter"></a>*Параметр универсального кода ресурса (URI)*

|  **Имя параметра** |  **Обязательное**  |  **Описание**          |
|  ------------------ |--------------- | ------------------------  |
|  `tenantId`         |   True         | Идентификатор клиента зарегистрированного приложения Azure AD.   |
| | | |

#### <a name="request-header"></a>*Заголовок запроса*

|  **Имя заголовка**    |  **Обязательное**  |  **Описание**          |
|  ------------------ |--------------- | ------------------------  |
|  `Content-Type`     |   True         | Связанный с этим запросом тип содержимого. Значение по умолчанию — `application/x-www-form-urlencoded`.  |
| | | |

#### <a name="request-body"></a>*Текст запроса*

|  **Имя свойства**  |  **Обязательное**  |  **Описание**          |
|  ------------------ |--------------- | ------------------------  |
|  `Grant_type`       |   True         | Тип предоставления разрешения. Значение по умолчанию — `client_credentials`. |
|  `Client_id`        |   True         | Идентификатор клиента или приложения, связанный с приложением Azure AD.|
|  `client_secret`    |   True         | Пароль, связанный с приложением Azure AD.  |
|  `Resource`         |   True         | Целевой ресурс, для которого запрашивается токен. Значение по умолчанию — `20e940b3-4c77-4b0b-9a53-9e16a1b010a7`.  |
| | | |

#### <a name="response"></a>*Ответ*

|  **имя**;    |  **Тип**  |  **Описание**          |
|  ------------------ |--------------- | ----------------------  |
|  `200 OK`     |   `TokenResponse`    | Запрос прошел.  |
| | | |

#### <a name="tokenresponse"></a>*TokenResponse*

Пример токена ответа.

```JSON
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

## <a name="using-the-azure-managed-identities-token"></a>Использование токена удостоверений, управляемых Azure

Применимый тип предложения — это приложения Azure с типом плана управляемого приложения.

При использовании этого подхода удостоверение развернутых ресурсов будет проходить проверку подлинности для отправки пользовательских событий использования счетчиков.  Вы можете внедрить код, который порождает использование в пределах развертывания.

>[!Note]
>Издатель должен убедиться, что ресурсы, порождаемые использование, заблокированы, поэтому они не будут изменены.

Управляемое приложение может содержать ресурсы различных типов — от виртуальных машин до функций Azure.  Дополнительные сведения о проверке подлинности с помощью управляемых удостоверений для различных служб см. [в статье Использование управляемых удостоверений для ресурсов Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-can-i-use-managed-identities-for-azure-resources).

Например, выполните следующие действия для проверки подлинности с помощью виртуальной машины Windows.

1. Убедитесь, что управляемое удостоверение настроено с помощью одного из методов:
    * [портал Azure пользовательский интерфейс](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)
    * [CLI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm)
    * [PowerShell](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm)
    * [Шаблон Azure Resource Manager](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm)
    * [REST](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-rest-vm#system-assigned-managed-identity)
    * [Пакеты Azure SDK](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm)

1. Получите маркер доступа для идентификатора приложения службы измерения Marketplace (`20e940b3-4c77-4b0b-9a53-9e16a1b010a7`), используя удостоверение системы, RDP для виртуальной машины, откройте консоль PowerShell и выполните следующую команду.

    ```powershell
    # curl is an alias to Web-Invoke PowerShell command
    # Get system identity access tokenn
    $MetadataUrl = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F"
    $Token = curl -H @{"Metadata" = "true"} $MetadataUrl | Select-Object -Expand Content | ConvertFrom-Json
    $Headers = @{}
    $Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
    ```

1. Получить идентификатор управляемого приложения из текущего свойства группы ресурсов "ManagedBy"

    ```powershell
    # Get subscription and resource group
    $metadata = curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-06-01 | select -ExpandProperty Content | ConvertFrom-Json 
    
    # Make sure the system identity has at least reader permission on the resource group
    $managementUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "?api-version=2019-10-01"
    $resourceGroupInfo = curl -Headers $Headers $managementUrl | select -ExpandProperty Content | ConvertFrom-Json
    $managedappId = $resourceGroupInfo.managedBy 
    ```

1. Служба измерения Marketplace должна сообщать об использовании в `resourceID`и `resourceUsageId` в случае управляемого приложения.

    ```powershell
    # Get resourceUsageId from the managed app
    $managedAppUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "/providers/Microsoft.Solutions/applications/" + $managedappId + "\?api-version=2019-07-01"
    $ManagedApp = curl $managedAppUrl -H $Headers | Select-Object -Expand Content | ConvertFrom-Json
    # Use this resource ID to emit usage 
    $resourceUsageId = $ManagedApp.properties.billingDetails.resourceUsageId
    ```

1. Использование [API службы измерения Marketplace](https://review.docs.microsoft.com/azure/marketplace/partner-center-portal/marketplace-metering-service-apis?branch=pr-en-us-101847) для выпуска использования.

## <a name="next-steps"></a>Дальнейшие действия

* [Создание предложения приложения Azure](./create-new-azure-apps-offer.md)