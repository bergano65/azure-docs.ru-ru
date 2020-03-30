---
title: Создание подписок Azure программными средствами
description: Узнайте, как создать дополнительные подписки Azure программно.
author: amberbhargava
ms.topic: conceptual
ms.date: 03/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.openlocfilehash: 33f1d154f438b917b79cd299e81c9078e2f2e81d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460404"
---
# <a name="programmatically-create-azure-subscriptions-preview"></a>Программное создание подписок Azure (предварительный просмотр)

Клиенты Azure с [корпоративным соглашением (EA),](https://azure.microsoft.com/pricing/enterprise-agreement/) [Microsoft Customer Agreement (MCA)](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) или биллинговой учетной записью [Microsoft Partner Agreement (MPA)](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) могут создавать подписки программно. В этой статье Вы узнаете как создавать подписки программно с помощью Azure Resource Manager.

При программном создании подписки Azure эта подписка регулируется соглашением, в соответствии с которым вы получили услуги Azure от корпорации Майкрософт или уполномоченного реселлера. Дополнительные сведения см. на странице [Юридическая информация Службы Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


## <a name="create-subscriptions-for-an-ea-billing-account"></a>Создание подписки для биллинговой учетной записи EA

Используйте информацию в следующих разделах для создания подписок EA.

### <a name="prerequisites"></a>Предварительные требования

Для создания подписки необходимо иметь роль владельца в учетной записи. Есть два способа получить роль:

* Администратор предприятия вашей регистрации может [сделать вас владельцем учетной записи](https://ea.azure.com/helpdocs/addNewAccount) (введать в требуемый), что делает вас владельцем учетной записи.

* Текущий владелец учетной записи регистрации может [предоставить вам права доступа](grant-access-to-create-subscription.md). Аналогичным образом, если вы хотите использовать принцип службы для создания подписки EA, вы должны [предоставить этой основной службе возможность создания подписок.](grant-access-to-create-subscription.md)

### <a name="find-accounts-you-have-access-to"></a>Поиск учетных записей, к которым у вас есть доступ

После добавления учетной записи, связанной с владельцем учетной записи, Azure использует отношение к учету и регистрации, чтобы определить, где выставить счет за подписку. Все подписки, созданные в соответствии с учетной записью, выставляются в регистрацию EA, в которую находится учетная запись. Чтобы создать подписки, необходимо в значениях передать о регистрации учетной записи и субъектов-пользователя — владельца подписки.

Чтобы выполнить следующие команды, войдите в *домашний каталог* владельца учетной записи, в котором по умолчанию создаются подписки.

### <a name="rest"></a>[Остальные](#tab/rest)

Запрос на список всех учетных записей, к которых у вас есть доступ:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

В ответе API перечислены все учетные записи, к которых у вас есть доступ:

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

Используйте свойство `principalName`, чтобы указать учетную запись для оплаты подписок. Копируйте `name` эту учетную запись. Например, если вы хотите создать SignUpEngineering@contoso.com подписку под учетной ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```записью регистрации, вы копируете. Этот идентификатор является идентификатором объекта учетной записи регистрации. Вставьте это значение где-то так, что `enrollmentAccountObjectId`вы можете использовать его в следующем шаге, как .

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Откройте [облачную оболочку Azure](https://shell.azure.com/) и выберите PowerShell.

Используйте командлет [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount), чтобы вывести список всех учетных записей регистрации, к которым у вас есть доступ.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure отвечает списком учетных записей, к которых у вас есть доступ:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
Используйте свойство `principalName`, чтобы указать учетную запись для оплаты подписок. Копируйте `ObjectId` эту учетную запись. Например, если вы хотите создать SignUpEngineering@contoso.com подписку под учетной ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```записью регистрации, вы копируете. Вставьте этот идентификатор объекта где-то, `enrollmentAccountObjectId`так что вы можете использовать его на следующем шаге в качестве.

### <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

Используйте [команду Get-EnrollmentAccount](https://aka.ms/EASubCreationPublicPreviewCLI), чтобы вывести список всех учетных записей регистрации, к которым у вас есть доступ.

```azurecli-interactive
az billing enrollment-account list
```

Azure отвечает списком учетных записей, к которых у вас есть доступ:

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

Используйте свойство `principalName`, чтобы указать учетную запись для оплаты подписок. Копируйте `name` эту учетную запись. Например, если вы хотите создать SignUpEngineering@contoso.com подписку под учетной ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```записью регистрации, вы копируете. Этот идентификатор является идентификатором объекта учетной записи регистрации. Вставьте это значение где-то так, что `enrollmentAccountObjectId`вы можете использовать его в следующем шаге, как .

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Создание подписок с помощью определенной учетной записи регистрации

В следующем примере создается подписка под названием *Dev Team Subscription* в учетной записи, выбранной на предыдущем этапе. Предложение о подписке - *MS-A'A'-0017P* (обычное корпоративное соглашение корпорации Майкрософт). Этот пример также при необходимости добавляет двух пользователей в качестве владельцев (RBAC) для подписки.

### <a name="rest"></a>[Остальные](#tab/rest)

Выполните следующий запрос, заменив `<enrollmentAccountObjectId>` на `name`, скопированный на первом шаге (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Если вы хотите указать владельцев, узнайте, [как получить иное икзаписи объектов пользователя.](grant-access-to-create-subscription.md#userObjectId)

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

| Имя элемента  | Обязательно | Тип   | Описание                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | нет      | Строка | Отображаемое имя подписки. Если нет — ему присваивается имя предложения, например "Microsoft Azure Enterprise".                                 |
| `offerType`   | Да      | Строка | Предложение подписки. Доступны два варианта предложения EA: [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (для использования в рабочей среде) и [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (для разработки и тестирования, должен быть [включен с помощью портала EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | нет       | Строка | Идентификатор объекта любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании.  |

В ответе возвращается объект `subscriptionOperation` для отслеживания. После завершения создания подписки объект `subscriptionOperation` вернет объект `subscriptionLink` с идентификатором подписки.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Во-первых, установить `Install-Module Az.Subscription -AllowPrerelease`этот модуль предварительного просмотра, запустив . Чтобы убедиться в том, что `-AllowPrerelease` работает, установите последнюю версию PowerShellGet из статьи [Получение модуля PowerShellGet](/powershell/scripting/gallery/installing-psget).

Выполнить команду [New-AzSubscription](/powershell/module/az.subscription) ниже, `<enrollmentAccountObjectId>` `ObjectId` заменив собранные```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```в первом этапе ( ). Если вы хотите указать владельцев, узнайте, [как получить иное икзаписи объектов пользователя.](grant-access-to-create-subscription.md#userObjectId)

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Имя элемента  | Обязательно | Тип   | Описание                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | нет      | Строка | Отображаемое имя подписки. Если нет — ему присваивается имя предложения, например "Microsoft Azure Enterprise".                                 |
| `OfferType`   | Да      | Строка | Предложение подписки. Доступны два варианта предложения EA: [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (для использования в рабочей среде) и [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (для разработки и тестирования, должен быть [включен с помощью портала EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Да       | Строка | Идентификатор объекта учетной записи для регистрации, в которой создается подписка и для которой выставляются счета. Это значение GUID, которое вы получили с помощью `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | нет       | Строка | Идентификатор объекта любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании.  |
| `OwnerSignInName`    | нет       | Строка | Адрес электронной почты любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании. Можно использовать этот параметр вместо `OwnerObjectId`.|
| `OwnerApplicationId` | нет       | Строка | Идентификатор приложения любого субъекта-службы, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании. Можно использовать этот параметр вместо `OwnerObjectId`. При использовании этого параметра служба должна иметь [доступ на чтение к каталогу](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).|

Полный список параметров приведен в разделе о [New-AzSubscription](/powershell/module/az.subscription).

### <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

Во-первых, установите `az extension add --name subscription`это расширение предварительного просмотра, запустив.

Выполнить [учетную запись az](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) `<enrollmentAccountObjectId>` создать команду ниже, заменив с `name` вы скопировали на первом этапе ( ).```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` Если вы хотите указать владельцев, узнайте, [как получить иное икзаписи объектов пользователя.](grant-access-to-create-subscription.md#userObjectId)

```azurecli-interactive
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Имя элемента  | Обязательно | Тип   | Описание                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | нет      | Строка | Отображаемое имя подписки. Если нет — ему присваивается имя предложения, например "Microsoft Azure Enterprise".                                 |
| `offer-type`   | Да      | Строка | Предложение подписки. Доступны два варианта предложения EA: [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (для использования в рабочей среде) и [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (для разработки и тестирования, должен быть [включен с помощью портала EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Да       | Строка | Идентификатор объекта учетной записи для регистрации, в которой создается подписка и для которой выставляются счета. Это значение GUID, которое вы получили с помощью `az billing enrollment-account list`. |
| `owner-object-id`      | нет       | Строка | Идентификатор объекта любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании.  |
| `owner-upn`    | нет       | Строка | Адрес электронной почты любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании. Можно использовать этот параметр вместо `owner-object-id`.|
| `owner-spn` | нет       | Строка | Идентификатор приложения любого субъекта-службы, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании. Можно использовать этот параметр вместо `owner-object-id`. При использовании этого параметра служба должна иметь [доступ на чтение к каталогу](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).|

Полный список параметров приведен в разделе о команде [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Ограничения API создания подписок Azure Enterprise

- С помощью этого API могут быть созданы только подписки Azure Enterprise.
- На учетную запись приходится 500 подписок. После этого больше подписок на учетную запись можно создать только на портале Azure. Если вы хотите создать больше подписок через API, создайте другую учетную запись регистрации.
- Пользователи, которые не являются владельцами учетных записей, но были добавлены в учетную запись регистрации через RBAC, не могут создавать подписки на портале Azure.
- Невозможно выбрать клиент для создания подписки. Подписка всегда создается в домашнем клиенте владельца учетной записи. Чтобы переместить подписку в другой клиент, ознакомьтесь с [изменением подписки клиента](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).


## <a name="create-subscriptions-for-an-mca-account"></a>Создание подписки для учетной записи MCA

### <a name="prerequisites"></a>Предварительные требования

Для создания подписок необходимо иметь роль владельца, автора подписки Azure в разделе счета-фактуры или роль владельца или участника в профиле выставления счетов или счет-фактуру. Дополнительные сведения см. в статье [Роли и задачи выставления счетов в подписке](../../cost-management-billing/manage/understand-mca-roles.md#subscription-billing-roles-and-tasks).

В приведенном ниже примере используются АПЫ REST. В настоящее время PowerShell и Azure CLI не поддерживаются.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Поиск учетных записей, к которым у вас есть доступ

Сделайте запрос ниже, чтобы перечислить все счета биллинга.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
Ответ API перечисляет счета биллинга, к которым у вас есть доступ.

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
Используйте `displayName` свойство для определения учетной записи, для которой вы хотите создать подписку. Убедитесь, что согласованиеТип учетной записи — *это MicrosoftCustomerAgreement*. Копируйте `name` учетную запись.  Например, если требуется создать подписку на `Contoso` счет биллинга, вы копируете. `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` Вставьте это значение в любое место, чтобы его можно было использовать на следующем шаге.

### <a name="find-invoice-sections-to-create-subscriptions"></a>Поиск разделов счета-фактуры для создания подписок

Плата за подписку отображается в разделе счета-фактуры биллингового профиля. Используйте следующий API, чтобы получить список разделов счетов-фактур и профилей выставления счетов, на которые у вас есть разрешение на создание подписок Azure.

Выполните следующий запрос, заменив `<billingAccountName>` на `name`, скопированный на первом шаге (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```).

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```
В ответе API перечислены все разделы счетов-фактур и их профили выставления счетов, на которых у вас есть доступ для создания подписок:

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

Используйте `invoiceSectionDisplayName` свойство для определения раздела счета-фактуры, для которого требуется создать подписку. Копирование `invoiceSectionId` `billingProfileId` , и `skuId` один из раздела счета-фактуры. Например, если вы хотите создать `Microsoft Azure plan` подписку типа `Development` для раздела `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX` `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` накладных, вы копируете и `0001`. Вставьте эти значения где-то, так что вы можете использовать их в следующем шаге.

### <a name="create-a-subscription-for-an-invoice-section"></a>Создание подписки для раздела счета-фактуры

В следующем примере создается подписка под названием *Dev Team по типу* Microsoft *Azure Plan* для раздела "Счетная *работа".* Подписка будет выставлена на счет биллинга *Contoso finance* и отображается в разделе *Развития* его счета-фактуры.

Сделайте следующий запрос, `<invoiceSectionId>` `invoiceSectionId` заменив скопированным со второго шага ().```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX``` Необходимо пройти `billingProfileId` и `skuId` скопировать со второго шага в параметрах запроса API. Если вы хотите указать владельцев, узнайте, [как получить иное икзаписи объектов пользователя.](grant-access-to-create-subscription.md#userObjectId)

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

| Имя элемента  | Обязательно | Тип   | Описание                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Да      | Строка | Отображаемое имя подписки.|
| `billingProfileId`   | Да      | Строка | Идентификатор профиля выставления счетов за сборы по подписке.  |
| `skuId` | Да      | Строка | Идентификатор sku, определяющий тип плана Azure. |
| `owners`      | нет       | Строка | Идентификатор объекта любого пользователя или основного обслуживания, который вы хотели бы добавить в качестве владельца RBAC в подписке при ее создании.  |
| `costCenter` | нет      | Строка | Центр затрат, связанный с подпиской. Он отображается в файле использования csv. |
| `managementGroupId` | нет      | Строка | Идентификатор группы управления, к которой будет добавлена подписка. Чтобы получить список групп управления, [см.](/rest/api/resources/managementgroups/list) Используйте идентификатор группы управления из API. |

В ответе возвращается объект `subscriptionCreationResult` для отслеживания. После завершения создания подписки объект `subscriptionCreationResult` вернет объект `subscriptionLink` с идентификатором подписки.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>Создание подписки для биллинговой учетной записи MPA

### <a name="prerequisites"></a>Предварительные требования

Для создания подписки на учетную запись биллинга необходимо иметь роль глобального админа или агента-админ-агента в учетной записи поставщика облачных решений вашей организации. Для получения дополнительной информации [см.](https://docs.microsoft.com/partner-center/permissions-overview)

В приведенном ниже примере используются АПЫ REST. В настоящее время PowerShell и Azure CLI не поддерживаются.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>Найдите учетные записи, к которым у вас есть доступ

Сделайте запрос ниже, чтобы перечислить все счета биллинга, к которым у вас есть доступ.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
Список ответов API — счета выставления счетов.

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
Используйте `displayName` свойство для определения учетной записи, для которой вы хотите создать подписку. Убедитесь, что согласованиеТип учетной записи - *MicrosoftPartnerAgreement*. Копирование `name` учетной записи. Например, если требуется создать подписку на `Contoso` счет биллинга, вы копируете. `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` Вставьте это значение в любое место, чтобы его можно было использовать на следующем шаге.

### <a name="find-customers-that-have-azure-plans"></a>Поиск клиентов с планами Azure

Сделайте следующий запрос, `<billingAccountName>` `name` заменив скопированным с первого шага ()```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```перечислить всех клиентов в счете биллинга, для которых можно создать подписки Azure.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
Ответ API перечисляет клиентов в счете выставления счетов с планами Azure. Вы можете создать подписку для этих клиентов.

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

Используйте `displayName` свойство для идентификации клиента, для которого вы хотите создать подписку. Копирование `id` для клиента. Например, если вы хотите создать `Fabrikam toys`подписку на, вы бы скопировать. `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx` Вставьте это значение где-нибудь, чтобы использовать его в последующих шагах.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Необязательно для косвенных поставщиков: Получить реселлеров для клиента

Если вы являетесь косвенным поставщиком в двухуровневой модели CSP, вы можете указать реселлера при создании подписки для клиентов.

Сделайте следующий запрос, `<customerId>` `id` заменив скопированным со второго шага ()```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```перечислить всех реселлеров, которые доступны для клиента.

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
Ответ API перечисляет посредников для клиента:

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
Используйте `description` свойство, чтобы определить реселлера, который будет связан с подпиской. Копирование `resellerId` для реселлера. Например, если вы `Wingtip`хотите связать, `3xxxxx`вы бы скопировать . Вставьте это значение в любое место, чтобы его можно было использовать на следующем шаге.

### <a name="create-a-subscription-for-a-customer"></a>Создание подписки для клиента

Следующий пример создает *подписку* под названием Dev Team на *игрушки Fabrikam* и ассоциированного реселлера *Wingtip* по подписке. T

Сделайте следующий запрос, `<customerId>` `id` заменив скопированным со второго шага ().```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` Пройдите дополнительный *реселлерId,* скопированный со второго шага в параметрах запроса API.

```json
POST https://management.azure.com<customerId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "skuId": "0001",
  "resellerId": "<resellerId>",
}'
```

| Имя элемента  | Обязательно | Тип   | Описание                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Да      | Строка | Отображаемое имя подписки.|
| `skuId` | Да      | Строка | Идентификатор sku плана Azure. Используйте *0001* для подписки типа Microsoft Azure Plan |
| `resellerId`      | нет       | Строка | MPN ID реселлера, который будет связан с подпиской.  |

В ответе возвращается объект `subscriptionCreationResult` для отслеживания. После завершения создания подписки объект `subscriptionCreationResult` вернет объект `subscriptionLink` с идентификатором подписки.

## <a name="next-steps"></a>Дальнейшие действия

* Для примера создания подписки Enterprise Agreement (EA) [sample code on GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)с помощью .NET см.
* После создания подписки можно предоставить эту возможность для других пользователей и субъектов-служб. Дополнительные сведения см. статье [Предоставление доступа к созданию подписок Azure Enterprise (предварительная версия)](grant-access-to-create-subscription.md).
* Дополнительные сведения о переносе большого числа подписок с помощью групп управления см. в руководстве по [упорядочиванию ресурсов с помощью групп управления Azure](../../governance/management-groups/overview.md).
