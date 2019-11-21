---
title: Programmatically create Azure subscriptions
description: Learn how to create additional Azure subscriptions programmatically.
author: amberb
ms.topic: conceptual
ms.date: 04/10/2019
ms.author: banders
ms.openlocfilehash: f6c7ff0ade741b3a7e9552147dbac34fc131e622
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224195"
---
# <a name="programmatically-create-azure-subscriptions-preview"></a>Programmatically create Azure subscriptions (preview)

Azure customers with an [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [Microsoft Customer Agreement (MCA)](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) or [Microsoft Partner Agreement (MPA)](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) billing account can create subscriptions programmatically. В этой статье Вы узнаете как создавать подписки программно с помощью Azure Resource Manager.

When you create an Azure subscription programmatically, that subscription is governed by the agreement under which you obtained Azure services from Microsoft or an authorized reseller. Дополнительные сведения см. на странице [Юридическая информация Службы Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-subscriptions-for-an-ea-billing-account"></a>Create subscriptions for an EA billing account

### <a name="prerequisites"></a>Технические условия

You must have an Owner role on an Enrollment Account to create a subscription. There are two ways to get the role:

* The Enterprise Administrator of your enrollment can [make you an Account Owner](https://ea.azure.com/helpdocs/addNewAccount) (sign in required) which makes you an Owner of the Enrollment Account.

* Текущий владелец учетной записи регистрации может [предоставить вам права доступа](grant-access-to-create-subscription.md). Similarly, if you want to use a service principal to create an EA subscription, you must [grant that service principal the ability to create subscriptions](grant-access-to-create-subscription.md).

### <a name="find-accounts-you-have-access-to"></a>Поиск учетных записей, к которым у вас есть доступ

After you're added to an Enrollment Account associated to an Account Owner, Azure uses the account-to-enrollment relationship to determine where to bill the subscription charges. All subscriptions created under the account are billed to the EA enrollment that the account is in. Чтобы создать подписки, необходимо в значениях передать о регистрации учетной записи и субъектов-пользователя — владельца подписки. 

Чтобы выполнить следующие команды, войдите в *домашний каталог* владельца учетной записи, в котором по умолчанию создаются подписки.

### <a name="resttabrest"></a>[REST](#tab/rest)

Request to list all enrollment accounts you have access to:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

The API response lists all enrollment accounts you have access to:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

Используйте свойство `principalName`, чтобы указать учетную запись для оплаты подписок. Copy the `name` of that account. For example, if you wanted to create subscriptions under the SignUpEngineering@contoso.com enrollment account, you'd copy ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. This identifier is the object ID of the enrollment account. Paste this value somewhere so that you can use it in the next step as `enrollmentAccountObjectId`.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Open [Azure Cloud Shell](https://shell.azure.com/) and select PowerShell.

Используйте командлет [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount), чтобы вывести список всех учетных записей регистрации, к которым у вас есть доступ.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure responds with a list of enrollment accounts you have access to:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
Используйте свойство `principalName`, чтобы указать учетную запись для оплаты подписок. Copy the `ObjectId` of that account. For example, if you wanted to create subscriptions under the SignUpEngineering@contoso.com enrollment account, you'd copy ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Paste this object ID somewhere so that you can use it in the next step as the `enrollmentAccountObjectId`.

### <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Используйте [команду Get-EnrollmentAccount](https://aka.ms/EASubCreationPublicPreviewCLI), чтобы вывести список всех учетных записей регистрации, к которым у вас есть доступ.

```azurecli-interactive 
az billing enrollment-account list
```

Azure responds with a list of enrollment accounts you have access to:

```json
[
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "SignUpEngineering@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  },
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "BillingPlatformTeam@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  }
]
```

Используйте свойство `principalName`, чтобы указать учетную запись для оплаты подписок. Copy the `name` of that account. For example, if you wanted to create subscriptions under the SignUpEngineering@contoso.com enrollment account, you'd copy ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. This identifier is the object ID of the enrollment account. Paste this value somewhere so that you can use it in the next step as `enrollmentAccountObjectId`.

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Создание подписок с помощью определенной учетной записи регистрации

The following example creates a subscription named *Dev Team Subscription* in the enrollment account selected in the previous step. The subscription offer is *MS-AZR-0017P* (regular Microsoft Enterprise Agreement). Этот пример также при необходимости добавляет двух пользователей в качестве владельцев (RBAC) для подписки.

### <a name="resttabrest"></a>[REST](#tab/rest)

Выполните следующий запрос, заменив `<enrollmentAccountObjectId>` на `name`, скопированный на первом шаге (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). If you'd like to specify owners, learn [how to get user object IDs](grant-access-to-create-subscription.md#userObjectId).

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

{
  "displayName": "Dev Team Subscription",
  "offerType": "MS-AZR-0017P",
  "owners": [
    {
      "objectId": "<userObjectId>"
    },
    {
      "objectId": "<servicePrincipalObjectId>"
    }
  ]
}
```

| Имя элемента  | Обязательно для заполнения | Тип   | Описание                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Нет      | Строка | Отображаемое имя подписки. Если нет — ему присваивается имя предложения, например "Microsoft Azure Enterprise".                                 |
| `offerType`   | ДА      | Строка | Предложение подписки. Доступны два варианта предложения EA: [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (для использования в рабочей среде) и [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (для разработки и тестирования, должен быть [включен с помощью портала EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Нет       | Строка | Идентификатор объекта любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании.  |

В ответе возвращается объект `subscriptionOperation` для отслеживания. После завершения создания подписки объект `subscriptionOperation` вернет объект `subscriptionLink` с идентификатором подписки.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

First, install this preview module by running `Install-Module Az.Subscription -AllowPrerelease`. Чтобы убедиться в том, что `-AllowPrerelease` работает, установите последнюю версию PowerShellGet из статьи [Получение модуля PowerShellGet](/powershell/scripting/gallery/installing-psget).

Run the [New-AzSubscription](/powershell/module/az.subscription) command below, replacing `<enrollmentAccountObjectId>` with the `ObjectId` collected in the first step (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). If you'd like to specify owners, learn [how to get user object IDs](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Имя элемента  | Обязательно для заполнения | Тип   | Описание                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Нет      | Строка | Отображаемое имя подписки. Если нет — ему присваивается имя предложения, например "Microsoft Azure Enterprise".                                 |
| `OfferType`   | ДА      | Строка | Предложение подписки. Доступны два варианта предложения EA: [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (для использования в рабочей среде) и [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (для разработки и тестирования, должен быть [включен с помощью портала EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | ДА       | Строка | Идентификатор объекта учетной записи для регистрации, в которой создается подписка и для которой выставляются счета. Это значение GUID, которое вы получили с помощью `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | Нет       | Строка | Идентификатор объекта любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании.  |
| `OwnerSignInName`    | Нет       | Строка | Адрес электронной почты любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании. Можно использовать этот параметр вместо `OwnerObjectId`.|
| `OwnerApplicationId` | Нет       | Строка | Идентификатор приложения любого субъекта-службы, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании. Можно использовать этот параметр вместо `OwnerObjectId`. При использовании этого параметра служба должна иметь [доступ на чтение к каталогу](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Полный список параметров приведен в разделе о [New-AzSubscription](/powershell/module/az.subscription).

### <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

First, install this preview extension by running `az extension add --name subscription`.

Run the [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) command below, replacing `<enrollmentAccountObjectId>` with the `name` you copied in the first step (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). If you'd like to specify owners, learn [how to get user object IDs](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Имя элемента  | Обязательно для заполнения | Тип   | Описание                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Нет      | Строка | Отображаемое имя подписки. Если нет — ему присваивается имя предложения, например "Microsoft Azure Enterprise".                                 |
| `offer-type`   | ДА      | Строка | Предложение подписки. Доступны два варианта предложения EA: [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (для использования в рабочей среде) и [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (для разработки и тестирования, должен быть [включен с помощью портала EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | ДА       | Строка | Идентификатор объекта учетной записи для регистрации, в которой создается подписка и для которой выставляются счета. Это значение GUID, которое вы получили с помощью `az billing enrollment-account list`. |
| `owner-object-id`      | Нет       | Строка | Идентификатор объекта любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании.  |
| `owner-upn`    | Нет       | Строка | Адрес электронной почты любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании. Можно использовать этот параметр вместо `owner-object-id`.|
| `owner-spn` | Нет       | Строка | Идентификатор приложения любого субъекта-службы, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании. Можно использовать этот параметр вместо `owner-object-id`. При использовании этого параметра служба должна иметь [доступ на чтение к каталогу](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Полный список параметров приведен в разделе о команде [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Ограничения API создания подписок Azure Enterprise

- С помощью этого API могут быть созданы только подписки Azure Enterprise.
- There's a limit of 200 subscriptions per enrollment account. After that, more subscriptions for the account can only be created in the Azure portal. If you want to create more subscriptions through the API, create another enrollment account.
- Users who aren't Account Owners, but were added to an enrollment account via RBAC, can't create subscriptions in the Azure portal.
- Невозможно выбрать клиент для создания подписки. Подписка всегда создается в домашнем клиенте владельца учетной записи. Чтобы переместить подписку в другой клиент, ознакомьтесь с [изменением подписки клиента](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).


## <a name="create-subscriptions-for-an-mca-account"></a>Create subscriptions for an MCA account

### <a name="prerequisites"></a>Технические условия

You must have an owner, contributor, or Azure subscription creator role on an invoice section or owner or contributor role on a billing profile or a billing account to create subscriptions. Дополнительные сведения см. в статье [Роли и задачи выставления счетов в подписке](../billing/billing-understand-mca-roles.md#subscription-billing-roles-and-tasks).

The example shown below use REST APIs. В настоящее время PowerShell и Azure CLI не поддерживаются.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Find billing accounts that you have access to 

Make the request below to list all the billing accounts.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
The API response lists the billing accounts that you have access to.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Use the `displayName` property to identify the billing account for which you want to create subscriptions. Ensure, the agreeementType of the account is *MicrosoftCustomerAgreement*. Copy the `name` of the account.  For example, if you want to create a subscription for the `Contoso` billing account, you'd copy `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Вставьте это значение в любое место, чтобы его можно было использовать на следующем шаге.

### <a name="find-invoice-sections-to-create-subscriptions"></a>Find invoice sections to create subscriptions

The charges for your subscription appear on a section of a billing profile's invoice. Use the following API to get the list of invoice sections and billing profiles on which you have permission to create Azure subscriptions.

Выполните следующий запрос, заменив `<billingAccountName>` на `name`, скопированный на первом шаге (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```).

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```
The API response lists all the invoice sections and their billing profiles on which you have access to create subscriptions:

```json
{
    "value": [{
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Development",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/invoiceSections/GJ77-xxxx-xxx-xxx"
    }, {
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Testing",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX"
  }]
}
    
```

Use the `invoiceSectionDisplayName` property to identify the invoice section for which you want to create subscriptions. Copy the `invoiceSectionId`, `billingProfileId` and one of the `skuId` for the invoice section. For example, if you want to create a subscription of type `Microsoft Azure plan` for `Development` invoice section, you'd copy `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX`, `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` , and `0001`. Paste these values somewhere so that you can use them in the next step.

### <a name="create-a-subscription-for-an-invoice-section"></a>Create a subscription for an invoice section

The following example creates a subscription named *Dev Team subscription* of type *Microsoft Azure Plan* for the *Development* invoice section. The subscription will be billed to the *Contoso finance's* billing profile and appear on the *Development* section of its invoice. 

Make the following request, replacing `<invoiceSectionId>` with the `invoiceSectionId` copied from the second step (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```). You'd need to pass the `billingProfileId` and `skuId` copied from the second step in the request parameters of the API. If you'd like to specify owners, learn [how to get user object IDs](grant-access-to-create-subscription.md#userObjectId).

```json
POST https://management.azure.com<invoiceSectionId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "billingProfileId": "<billingProfileId>",
  "skuId": "<skuId>",
  "owners": [
      {
        "objectId": "<userObjectId>"
      },
      {
        "objectId": "<servicePrincipalObjectId>"
      }
    ],
  "costCenter": "35683",
  "managementGroupId": "/providers/Microsoft.Management/managementGroups/xxxxxxx",",
}'

```

| Имя элемента  | Обязательно для заполнения | Тип   | Описание                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | ДА      | Строка | Отображаемое имя подписки.|
| `billingProfileId`   | ДА      | Строка | The ID of the billing profile that will be billed for the subscription's charges.  |
| `skuId` | ДА      | Строка | The sku ID that determines the type of Azure plan. |
| `owners`      | Нет       | Строка | The Object ID of any user or service principal that you'd like to add as an RBAC Owner on the subscription when it's created.  |
| `costCenter` | Нет      | Строка | The cost center associated with the subscription. It shows up in the usage csv file. |
| `managementGroupId` | Нет      | Строка | The ID of the management group to which the subscription will be added. To get the list of management groups, see [Management Groups - List API](https://docs.microsoft.com/rest/api/resources/managementgroups/list). Use the ID of a management group from the API. |

В ответе возвращается объект `subscriptionCreationResult` для отслеживания. После завершения создания подписки объект `subscriptionCreationResult` вернет объект `subscriptionLink` с идентификатором подписки.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>Create subscriptions for an MPA billing account

### <a name="prerequisites"></a>Технические условия

You must have a Global Admin or  Admin Agent role in your organization's cloud solution provider account to create subscription for your billing account. For more information, see [Partner Center - Assign users roles and permissions](https://docs.microsoft.com/partner-center/permissions-overview).

The example shown below use REST APIs. В настоящее время PowerShell и Azure CLI не поддерживаются.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>Find the billing accounts that you have access to 

Make the request below to list all billing accounts that you have access to.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
The API response list the billing accounts.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftPartnerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Use the `displayName` property to identify the billing account for which you want to create subscriptions. Ensure, the agreeementType of the account is *MicrosoftPartnerAgreement*. Copy the `name` for the account. For example, if you want to create a subscription for the `Contoso` billing account, you'd copy `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Вставьте это значение в любое место, чтобы его можно было использовать на следующем шаге.

### <a name="find-customers-that-have-azure-plans"></a>Find customers that have Azure plans

Make the following request, replacing `<billingAccountName>` with the `name` copied from the first step (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```) to list all customers in the billing account for whom you can create Azure subscriptions. 

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
The API response lists the customers in the billing account with Azure plans. You can create subscriptions for these customers.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso USD",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam sports",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam bakery"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }]
}
    
```

Use the `displayName` property to identify the customer for which you want to create subscriptions. Copy the `id` for the customer. For example, if you want to create a subscription for `Fabrikam toys`, you'd copy `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. Paste this value somewhere to use it in the subsequent steps.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Optional for Indirect providers: Get the resellers for a customer

If you're an Indirect provider in the CSP two-tier model, you can specify a reseller while creating subscriptions for customers. 

Make the following request, replacing `<customerId>` with the `id` copied from the second step (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) to list all resellers that are available for a customer.

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
The API response lists the resellers for the customer:

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
},
{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "5xxxxx",
        "description": "Tailspin"
      }
    ]
  }
}]
}
```
Use the `description` property to identify the reseller who will be associated with the subscription. Copy the `resellerId` for the reseller. For example, if you want to associate `Wingtip`, you'd copy `3xxxxx`. Вставьте это значение в любое место, чтобы его можно было использовать на следующем шаге.

### <a name="create-a-subscription-for-a-customer"></a>Create a subscription for a customer

The following example creates a subscription named *Dev Team subscription*  for *Fabrikam toys* and associate *Wingtip* reseller to the subscription. T

Выполните следующий запрос, заменив `<customerId>` на `id`, скопированный на первом шаге (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Pass the optional *resellerId* copied from the second step in the request parameters of the API. 

```json
POST https://management.azure.com<customerId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "skuId": "0001",
  "resellerId": "<resellerId>",
}'
```

| Имя элемента  | Обязательно для заполнения | Тип   | Описание                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | ДА      | Строка | Отображаемое имя подписки.|
| `skuId` | ДА      | Строка | The sku ID of the Azure plan. Use *0001* for subscriptions of type Microsoft Azure Plan |
| `resellerId`      | Нет       | Строка | The MPN ID of the reseller who will be associated with the subscription.  |

В ответе возвращается объект `subscriptionCreationResult` для отслеживания. После завершения создания подписки объект `subscriptionCreationResult` вернет объект `subscriptionLink` с идентификатором подписки.

## <a name="next-steps"></a>Дальнейшие действия

* For an example on creating an Enterprise Agreement (EA) subscription using .NET, see [sample code on GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* После создания подписки можно предоставить эту возможность для других пользователей и субъектов-служб. Дополнительные сведения см. статье [Предоставление доступа к созданию подписок Azure Enterprise (предварительная версия)](grant-access-to-create-subscription.md).
* Дополнительные сведения о переносе большого числа подписок с помощью групп управления см. в руководстве по [упорядочиванию ресурсов с помощью групп управления Azure](management-groups-overview.md).
