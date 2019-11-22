---
title: Создание подписок Azure программными средствами
description: Узнайте, как программным способом создать дополнительные подписки Azure.
author: amberb
ms.topic: conceptual
ms.date: 04/10/2019
ms.author: banders
ms.openlocfilehash: 757a542c8583f6a2b3f73e8144b6281438d75ef2
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74273599"
---
# <a name="programmatically-create-azure-subscriptions-preview"></a>Создание подписок Azure программными средствами (Предварительная версия)

Клиенты Azure с использованием учетной записи выставления счетов [Соглашение Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [Microsoft Customer Agreement (MCA)](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) или [Microsoft Partner Agreement (](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) Майкрософт) могут создавать подписки программным способом. В этой статье Вы узнаете как создавать подписки программно с помощью Azure Resource Manager.

При создании подписки Azure программным образом эта подписка регулируется соглашением, по которому вы получили службы Azure от корпорации Майкрософт или у полномочного торгового посредника. Дополнительные сведения см. на странице [Юридическая информация Службы Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-subscriptions-for-an-ea-billing-account"></a>Создание подписок для учетной записи выставления счетов EA

### <a name="prerequisites"></a>предварительным требованиям

Для создания подписки необходимо иметь роль владельца для учетной записи регистрации. Существует два способа получить эту роль:

* Администратор предприятия регистрации может [сделать вас владельцем учетной записи](https://ea.azure.com/helpdocs/addNewAccount) (требуется вход), который предоставляет владельца учетной записи регистрации.

* Текущий владелец учетной записи регистрации может [предоставить вам права доступа](grant-access-to-create-subscription.md). Аналогично, если вы хотите использовать субъект-службу для создания подписки EA, необходимо [предоставить этому субъекту-службе возможность создания подписок](grant-access-to-create-subscription.md).

### <a name="find-accounts-you-have-access-to"></a>Поиск учетных записей, к которым у вас есть доступ

После добавления в учетную запись регистрации, связанную с владельцем учетной записи, Azure использует связь "счет-регистрация", чтобы определить, где следует оплачивать оплату за подписку. Все подписки, созданные под учетной записью, выставляются в соответствии с регистрацией EA, в которой находится учетная запись. Чтобы создать подписки, необходимо в значениях передать о регистрации учетной записи и субъектов-пользователя — владельца подписки. 

Чтобы выполнить следующие команды, войдите в *домашний каталог* владельца учетной записи, в котором по умолчанию создаются подписки.

### <a name="resttabrest"></a>[REST](#tab/rest)

Запрос на получение списка всех учетных записей регистрации, к которым у вас есть доступ:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

В ответе API перечислены все учетные записи регистрации, к которым у вас есть доступ:

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

Используйте свойство `principalName`, чтобы указать учетную запись для оплаты подписок. Скопируйте `name` этой учетной записи. Например, если вы хотите создать подписки в учетной записи регистрации SignUpEngineering@contoso.com, скопируйте ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Этот идентификатор является ИДЕНТИФИКАТОРом объекта учетной записи регистрации. Вставьте это значение в любое место, чтобы его можно было использовать на следующем шаге как `enrollmentAccountObjectId`.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Откройте [Azure Cloud Shell](https://shell.azure.com/) и выберите PowerShell.

Используйте командлет [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount), чтобы вывести список всех учетных записей регистрации, к которым у вас есть доступ.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure реагирует на список учетных записей регистрации, к которым у вас есть доступ:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
Используйте свойство `principalName`, чтобы указать учетную запись для оплаты подписок. Скопируйте `ObjectId` этой учетной записи. Например, если вы хотите создать подписки в учетной записи регистрации SignUpEngineering@contoso.com, скопируйте ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Вставьте этот идентификатор объекта где-то, чтобы его можно было использовать на следующем шаге в качестве `enrollmentAccountObjectId`.

### <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

Используйте [команду Get-EnrollmentAccount](https://aka.ms/EASubCreationPublicPreviewCLI), чтобы вывести список всех учетных записей регистрации, к которым у вас есть доступ.

```azurecli-interactive 
az billing enrollment-account list
```

Azure реагирует на список учетных записей регистрации, к которым у вас есть доступ:

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

Используйте свойство `principalName`, чтобы указать учетную запись для оплаты подписок. Скопируйте `name` этой учетной записи. Например, если вы хотите создать подписки в учетной записи регистрации SignUpEngineering@contoso.com, скопируйте ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Этот идентификатор является ИДЕНТИФИКАТОРом объекта учетной записи регистрации. Вставьте это значение в любое место, чтобы его можно было использовать на следующем шаге как `enrollmentAccountObjectId`.

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Создание подписок с помощью определенной учетной записи регистрации

В следующем примере создается подписка с именем " *Подписка на группу разработки* " в учетной записи регистрации, выбранной на предыдущем шаге. В качестве подписки используется *MS-AZR-0017P* (обычный Microsoft Соглашение Enterprise). Этот пример также при необходимости добавляет двух пользователей в качестве владельцев (RBAC) для подписки.

### <a name="resttabrest"></a>[REST](#tab/rest)

Выполните следующий запрос, заменив `<enrollmentAccountObjectId>` на `name`, скопированный на первом шаге (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Если вы хотите указать владельцев, Узнайте, [как получить идентификаторы объектов пользователя](grant-access-to-create-subscription.md#userObjectId).

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

| Имя элемента  | обязательные | введите   | ОПИСАНИЕ                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Нет      | Строка, | Отображаемое имя подписки. Если нет — ему присваивается имя предложения, например "Microsoft Azure Enterprise".                                 |
| `offerType`   | Yes      | Строка, | Предложение подписки. Доступны два варианта предложения EA: [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (для использования в рабочей среде) и [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (для разработки и тестирования, должен быть [включен с помощью портала EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Нет       | Строка, | Идентификатор объекта любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании.  |

В ответе возвращается объект `subscriptionOperation` для отслеживания. После завершения создания подписки объект `subscriptionOperation` вернет объект `subscriptionLink` с идентификатором подписки.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Сначала установите этот модуль предварительной версии, выполнив `Install-Module Az.Subscription -AllowPrerelease`. Чтобы убедиться в том, что `-AllowPrerelease` работает, установите последнюю версию PowerShellGet из статьи [Получение модуля PowerShellGet](/powershell/scripting/gallery/installing-psget).

Выполните приведенную ниже команду [New-азсубскриптион](/powershell/module/az.subscription) , заменив `<enrollmentAccountObjectId>` на `ObjectId`, собранные на первом шаге (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Если вы хотите указать владельцев, Узнайте, [как получить идентификаторы объектов пользователя](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Имя элемента  | обязательные | введите   | ОПИСАНИЕ                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Нет      | Строка, | Отображаемое имя подписки. Если нет — ему присваивается имя предложения, например "Microsoft Azure Enterprise".                                 |
| `OfferType`   | Yes      | Строка, | Предложение подписки. Доступны два варианта предложения EA: [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (для использования в рабочей среде) и [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (для разработки и тестирования, должен быть [включен с помощью портала EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Yes       | Строка, | Идентификатор объекта учетной записи для регистрации, в которой создается подписка и для которой выставляются счета. Это значение GUID, которое вы получили с помощью `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | Нет       | Строка, | Идентификатор объекта любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании.  |
| `OwnerSignInName`    | Нет       | Строка, | Адрес электронной почты любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании. Можно использовать этот параметр вместо `OwnerObjectId`.|
| `OwnerApplicationId` | Нет       | Строка, | Идентификатор приложения любого субъекта-службы, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании. Можно использовать этот параметр вместо `OwnerObjectId`. При использовании этого параметра служба должна иметь [доступ на чтение к каталогу](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Полный список параметров приведен в разделе о [New-AzSubscription](/powershell/module/az.subscription).

### <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

Сначала установите это расширение для предварительного просмотра, запустив `az extension add --name subscription`.

Выполните приведенную ниже команду [AZ Account Create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) , заменив `<enrollmentAccountObjectId>` на `name`, скопированный на первом шаге (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Если вы хотите указать владельцев, Узнайте, [как получить идентификаторы объектов пользователя](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Имя элемента  | обязательные | введите   | ОПИСАНИЕ                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Нет      | Строка, | Отображаемое имя подписки. Если нет — ему присваивается имя предложения, например "Microsoft Azure Enterprise".                                 |
| `offer-type`   | Yes      | Строка, | Предложение подписки. Доступны два варианта предложения EA: [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (для использования в рабочей среде) и [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (для разработки и тестирования, должен быть [включен с помощью портала EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Yes       | Строка, | Идентификатор объекта учетной записи для регистрации, в которой создается подписка и для которой выставляются счета. Это значение GUID, которое вы получили с помощью `az billing enrollment-account list`. |
| `owner-object-id`      | Нет       | Строка, | Идентификатор объекта любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании.  |
| `owner-upn`    | Нет       | Строка, | Адрес электронной почты любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании. Можно использовать этот параметр вместо `owner-object-id`.|
| `owner-spn` | Нет       | Строка, | Идентификатор приложения любого субъекта-службы, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании. Можно использовать этот параметр вместо `owner-object-id`. При использовании этого параметра служба должна иметь [доступ на чтение к каталогу](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Полный список параметров приведен в разделе о команде [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Ограничения API создания подписок Azure Enterprise

- С помощью этого API могут быть созданы только подписки Azure Enterprise.
- Существует ограничение в 200 подписок на учетную запись регистрации. После этого дополнительные подписки для учетной записи можно создать только в портал Azure. Если вы хотите создать больше подписок через API, создайте другую учетную запись регистрации.
- Пользователи, не являющиеся владельцами учетной записи, но добавленные в учетную запись регистрации через RBAC, не могут создавать подписки в портал Azure.
- Невозможно выбрать клиент для создания подписки. Подписка всегда создается в домашнем клиенте владельца учетной записи. Чтобы переместить подписку в другой клиент, ознакомьтесь с [изменением подписки клиента](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).


## <a name="create-subscriptions-for-an-mca-account"></a>Создание подписок для учетной записи MCA

### <a name="prerequisites"></a>предварительным требованиям

Для создания подписок необходимо иметь роль "владелец", "участник" или "создатель подписки Azure" в разделе "счет" или "роль владельца" или "участник" в профиле выставления счетов или счет выставления счетов. Дополнительные сведения см. в статье [Роли и задачи выставления счетов в подписке](../billing/billing-understand-mca-roles.md#subscription-billing-roles-and-tasks).

В примере, приведенном ниже, используются интерфейсы API для интерфейса RESTFUL. В настоящее время PowerShell и Azure CLI не поддерживаются.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Поиск учетных записей выставления счетов, к которым у вас есть доступ 

Сделайте запрос ниже, чтобы получить список всех учетных записей выставления счетов.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
В ответе API перечислены учетные записи выставления счетов, к которым у вас есть доступ.

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
Используйте свойство `displayName`, чтобы указать счет выставления счетов, для которого нужно создать подписки. Убедитесь, что Агриементтипе учетной записи — *микрософткустомерагримент*. Скопируйте `name` учетной записи.  Например, если вы хотите создать подписку для учетной записи `Contoso` выставления счетов, скопируйте `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Вставьте это значение в любое место, чтобы его можно было использовать на следующем шаге.

### <a name="find-invoice-sections-to-create-subscriptions"></a>Поиск разделов накладных для создания подписок

Плата за подписку отображается в разделе счета профиля выставления счетов. Используйте следующий API для получения списка разделов счета и профилей выставления счетов, по которым у вас есть разрешения на создание подписок Azure.

Выполните следующий запрос, заменив `<billingAccountName>` на `name`, скопированный на первом шаге (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```).

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```
В ответе API перечислены все разделы счета и их профили выставления счетов, к которым у вас есть доступ для создания подписок.

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

Используйте свойство `invoiceSectionDisplayName` для указания раздела счета, для которого необходимо создать подписки. Скопируйте `invoiceSectionId`, `billingProfileId` и одну из `skuId` для раздела счет. Например, если вы хотите создать подписку типа `Microsoft Azure plan` для раздела `Development` Invoice, скопируйте `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX`, `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` и `0001`. Вставьте эти значения в любое место, чтобы их можно было использовать на следующем шаге.

### <a name="create-a-subscription-for-an-invoice-section"></a>Создание подписки для раздела счета

В следующем примере создается подписка с именем *подписки группы разработчиков* типа *Microsoft Azure Plan* для счета на *разработку* . Для подписки будет выставлен счет по профилю выставления счетов *contoso Finance* и он появится в разделе " *Разработка* " соответствующего счета. 

Выполните следующий запрос, заменив `<invoiceSectionId>` на `invoiceSectionId`, скопированный на втором шаге (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```). Необходимо передать `billingProfileId` и `skuId`, скопированные на втором шаге в параметрах запроса API. Если вы хотите указать владельцев, Узнайте, [как получить идентификаторы объектов пользователя](grant-access-to-create-subscription.md#userObjectId).

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

| Имя элемента  | обязательные | введите   | ОПИСАНИЕ                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Yes      | Строка, | Отображаемое имя подписки.|
| `billingProfileId`   | Yes      | Строка, | Идентификатор профиля выставления счетов, для которого будет выставлен счет за оплату за подписку.  |
| `skuId` | Yes      | Строка, | Идентификатор SKU, определяющий тип плана Azure. |
| `owners`      | Нет       | Строка, | Идентификатор объекта любого пользователя или субъекта-службы, который вы хотите добавить в качестве владельца RBAC в подписке при ее создании.  |
| `costCenter` | Нет      | Строка, | Центр затрат, связанный с подпиской. Он отображается в CSV-файле использования. |
| `managementGroupId` | Нет      | Строка, | Идентификатор группы управления, в которую будет добавлена подписка. Чтобы получить список групп управления, см. раздел [API группы управления-List](https://docs.microsoft.com/rest/api/resources/managementgroups/list). Используйте идентификатор группы управления из API. |

В ответе возвращается объект `subscriptionCreationResult` для отслеживания. После завершения создания подписки объект `subscriptionCreationResult` вернет объект `subscriptionLink` с идентификатором подписки.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>Создание подписок для учетной записи для выставления счетов активации

### <a name="prerequisites"></a>предварительным требованиям

Для создания подписки на счет для выставления счетов необходимо иметь роль глобального администратора или агента администратора в учетной записи поставщика облачных решений вашей организации. Дополнительные сведения см. в разделе [Центр партнеров — назначение ролей и разрешений пользователей](https://docs.microsoft.com/partner-center/permissions-overview).

В примере, приведенном ниже, используются интерфейсы API для интерфейса RESTFUL. В настоящее время PowerShell и Azure CLI не поддерживаются.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>Поиск учетных записей выставления счетов, к которым у вас есть доступ 

Сделайте запрос ниже, чтобы получить список всех счетов выставления счетов, к которым у вас есть доступ.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
В списке ответов API перечислены учетные записи выставления счетов.

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
Используйте свойство `displayName`, чтобы указать счет выставления счетов, для которого нужно создать подписки. Убедитесь, что Агриементтипе учетной записи — *микрософтпартнерагримент*. Скопируйте `name` для учетной записи. Например, если вы хотите создать подписку для учетной записи `Contoso` выставления счетов, скопируйте `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Вставьте это значение в любое место, чтобы его можно было использовать на следующем шаге.

### <a name="find-customers-that-have-azure-plans"></a>Поиск клиентов, имеющих планы Azure

Выполните следующий запрос, заменив `<billingAccountName>` на `name`, скопированный на первом шаге (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```), чтобы получить список всех клиентов в счете выставления счетов, для которых вы можете создавать подписки Azure. 

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
В ответе API перечисляются клиенты в учетной записи выставления счетов с помощью планов Azure. Вы можете создавать подписки для этих клиентов.

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

Используйте свойство `displayName`, чтобы указать клиента, для которого необходимо создать подписки. Скопируйте `id` для клиента. Например, если вы хотите создать подписку для `Fabrikam toys`, скопируйте `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. Вставьте это значение в любое место, чтобы использовать его в последующих шагах.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Необязательно для косвенных поставщиков: получение торговых посредников для клиента

Если вы являетесь косвенным поставщиком в двухуровневой модели CSP, вы можете указать торгового посредника при создании подписок для клиентов. 

Выполните следующий запрос, заменив `<customerId>` на `id`, скопированный на втором шаге (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```), чтобы получить список всех посредников, доступных для клиента.

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
В ответе API перечисляются торговые посредники для клиента:

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
Используйте свойство `description`, чтобы указать торгового посредника, который будет связан с подпиской. Скопируйте `resellerId` для торгового посредника. Например, если необходимо связать `Wingtip`, скопируйте `3xxxxx`. Вставьте это значение в любое место, чтобы его можно было использовать на следующем шаге.

### <a name="create-a-subscription-for-a-customer"></a>Создание подписки для клиента

В следующем примере создается подписка с именем " *Подписка на группу разработчиков* " для *Fabrikam Toys* и связывается с подпискум торгового посредника *Wingtip* . T

Выполните следующий запрос, заменив `<customerId>` на `id`, скопированный на втором шаге (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Передайте необязательный *реселлерид* , скопированный на втором шаге, в параметрах запроса API. 

```json
POST https://management.azure.com<customerId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "skuId": "0001",
  "resellerId": "<resellerId>",
}'
```

| Имя элемента  | обязательные | введите   | ОПИСАНИЕ                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Yes      | Строка, | Отображаемое имя подписки.|
| `skuId` | Yes      | Строка, | Идентификатор SKU плана Azure. Используйте *0001* для подписок типа "план Microsoft Azure" |
| `resellerId`      | Нет       | Строка, | Идентификатор MPN торгового посредника, который будет связан с подпиской.  |

В ответе возвращается объект `subscriptionCreationResult` для отслеживания. После завершения создания подписки объект `subscriptionCreationResult` вернет объект `subscriptionLink` с идентификатором подписки.

## <a name="next-steps"></a>Дополнительная информация

* Пример создания подписки на Соглашение Enterprise (EA) с помощью .NET см. в разделе [пример кода на сайте GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* После создания подписки можно предоставить эту возможность для других пользователей и субъектов-служб. Дополнительные сведения см. статье [Предоставление доступа к созданию подписок Azure Enterprise (предварительная версия)](grant-access-to-create-subscription.md).
* Дополнительные сведения о переносе большого числа подписок с помощью групп управления см. в руководстве по [упорядочиванию ресурсов с помощью групп управления Azure](management-groups-overview.md).
