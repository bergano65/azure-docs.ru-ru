---
title: Программное создание подписок Azure Enterprise | Документация Майкрософт
description: Узнайте, как программно создавать дополнительные подписки Azure Enterprise или Azure "Enterprise — разработка и тестирование".
services: azure-resource-manager
author: jureid
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/10/2019
ms.author: jureid
ms.openlocfilehash: cf325b93c626e0c7f9584449154e2d531995cdc5
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204348"
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Программное создание подписок Azure Enterprise (предварительная версия)

Как пользователь Azure, подписавший [Соглашение Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), вы можете программно создавать подписки EA (MS-AZR-0017P) и EA "Разработка и тестирование"(MS-AZR-0148P). В этой статье Вы узнаете как создавать подписки программно с помощью Azure Resource Manager.

Когда Вы создаете подписки Azure, создаваемые с помощью этого API, где подписка регулируются соглашением, в соответствии с которым вы получили службы Microsoft Azure от корпорации Майкрософт или уполномоченного торгового посредника. Дополнительные сведения см. на странице [Юридическая информация Службы Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Технические условия

Необходимо иметь роли "Владелец" для создания подписок в учетной записи регистрации. Получить эти роли можно двумя способами:

* Администратор регистрации может [сделать вы владелец учетной записи](https://ea.azure.com/helpdocs/addNewAccount) (требуется вход) чему вы владельцем учетной записи регистрации. Следуйте инструкциям из приглашения, полученного по электронной почте, чтобы вручную создать начальную подписку. Подтвердите владение учетной записью и вручную создайте начальную подписку EA, прежде чем переходить к следующему шагу. Просто добавить учетную запись в регистрацию недостаточно.

* Текущий владелец учетной записи регистрации может [предоставить вам права доступа](grant-access-to-create-subscription.md). Аналогично, если вы хотите использовать субъект-службу для создания подписки EA, необходимо [предоставить возможность создания подписки на нее](grant-access-to-create-subscription.md).

## <a name="find-accounts-you-have-access-to"></a>Поиск учетных записей, к которым у вас есть доступ

После того, как вы будете добавлены в регистрацию Azure EA в качестве владельца учетной записи, платформа Azure использует связь между регистрацией и учетной записью, чтобы определить цель для выставления счетов за подписки. Все подписки, созданные с учетной записью, оплачиваются в рамках регистрации EA, к которой относится эта учетная запись. Чтобы создать подписки, необходимо в значениях передать о регистрации учетной записи и субъектов-пользователя — владельца подписки. 

Чтобы выполнить следующие команды, войдите в *домашний каталог* владельца учетной записи, в котором по умолчанию создаются подписки.

## <a name="resttabrest"></a>[REST](#tab/rest)

Запрос, чтобы получить список всех учетных записей регистрации, которые у вас есть доступ к:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Платформа Azure выводит список всех учетных записей регистрации, к которым у вас есть доступ.

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

Используйте свойство `principalName`, чтобы указать учетную запись для оплаты подписок. Копировать `name` этой учетной записи. Например, если вы хотите создать подписки в SignUpEngineering@contoso.com учетной записи регистрации, вы будете копировать ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Это идентификатор объекта учетной записи регистрации. Вставьте это значение, где-нибудь, чтобы его можно использовать на следующем шаге как `enrollmentAccountObjectId`.

## <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Откройте [Azure Cloud Shell](https://shell.azure.com/) и выберите команду PowerShell.

Используйте командлет [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount), чтобы вывести список всех учетных записей регистрации, к которым у вас есть доступ.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Платформа Azure выводит список учетных записей регистрации, которые у вас есть доступ к:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
Используйте свойство `principalName`, чтобы указать учетную запись для оплаты подписок. Копировать `ObjectId` этой учетной записи. Например, если вы хотите создать подписки в SignUpEngineering@contoso.com учетной записи регистрации, вы будете копировать ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Вставьте этот идентификатор объекта, где-нибудь, чтобы его можно использовать на следующем шаге как `enrollmentAccountObjectId`.

## <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

Используйте [команду Get-EnrollmentAccount](https://aka.ms/EASubCreationPublicPreviewCLI), чтобы вывести список всех учетных записей регистрации, к которым у вас есть доступ.

```azurecli-interactive 
az billing enrollment-account list
```

Платформа Azure выводит список учетных записей регистрации, которые у вас есть доступ к:

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

Используйте свойство `principalName`, чтобы указать учетную запись для оплаты подписок. Копировать `name` этой учетной записи. Например, если вы хотите создать подписки в SignUpEngineering@contoso.com учетной записи регистрации, вы будете копировать ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Это идентификатор объекта учетной записи регистрации. Вставьте это значение, где-нибудь, чтобы его можно использовать на следующем шаге как `enrollmentAccountObjectId`.

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Создание подписок с помощью определенной учетной записи регистрации

В следующем примере создается подписка с именем *Dev Team Subscription* в учетной записи регистрации, выбранные на предыдущем шаге. Предложения подписки *MS-AZR - 0017P* (регулярных соглашения Microsoft Enterprise). Этот пример также при необходимости добавляет двух пользователей в качестве владельцев (RBAC) для подписки.

# <a name="resttabrest"></a>[REST](#tab/rest)

Сделать следующий запрос, заменив `<enrollmentAccountObjectId>` с `name` копируется из первого шага (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Если вы хотите указать владельцев, узнайте [способы получения идентификаторов объектов пользователя](grant-access-to-create-subscription.md#userObjectId).

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

| Имя элемента  | Обязательно для заполнения | type   | Описание                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Нет      | String | Отображаемое имя подписки. Если нет — ему присваивается имя предложения, например "Microsoft Azure Enterprise".                                 |
| `offerType`   | Да      | String | Предложение подписки. Доступны два варианта предложения EA: [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (для использования в рабочей среде) и [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (для разработки и тестирования, должен быть [включен с помощью портала EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Нет       | String | Идентификатор объекта любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании.  |

В ответе возвращается объект `subscriptionOperation` для отслеживания. После завершения создания подписки объект `subscriptionOperation` вернет объект `subscriptionLink` с идентификатором подписки.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Во-первых, установите эту предварительную версию модуля, выполнив `Install-Module Az.Subscription -AllowPrerelease`. Чтобы убедиться в том, что `-AllowPrerelease` работает, установите последнюю версию PowerShellGet из статьи [Получение модуля PowerShellGet](/powershell/gallery/installing-psget).

Запустите [New AzSubscription](/powershell/module/az.subscription) команду ниже, заменив `<enrollmentAccountObjectId>` с `ObjectId` собранные на первом шаге (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Если вы хотите указать владельцев, узнайте [способы получения идентификаторов объектов пользователя](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Имя элемента  | Обязательно для заполнения | type   | Описание                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Нет      | String | Отображаемое имя подписки. Если нет — ему присваивается имя предложения, например "Microsoft Azure Enterprise".                                 |
| `OfferType`   | Да      | String | Предложение подписки. Доступны два варианта предложения EA: [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (для использования в рабочей среде) и [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (для разработки и тестирования, должен быть [включен с помощью портала EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Да       | String | Идентификатор объекта учетной записи для регистрации, в которой создается подписка и для которой выставляются счета. Это значение GUID, которое вы получили с помощью `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | Нет       | String | Идентификатор объекта любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании.  |
| `OwnerSignInName`    | Нет       | String | Адрес электронной почты любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании. Можно использовать этот параметр вместо `OwnerObjectId`.|
| `OwnerApplicationId` | Нет       | String | Идентификатор приложения любого субъекта-службы, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании. Можно использовать этот параметр вместо `OwnerObjectId`. При использовании этого параметра служба должна иметь [доступ на чтение к каталогу](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Полный список параметров приведен в разделе о [New-AzSubscription](/powershell/module/az.subscription.preview).

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

Во-первых, установите эту предварительную версию расширения, выполнив `az extension add --name subscription`.

Запустите [Создание учетной записи az](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) команду ниже, заменив `<enrollmentAccountObjectId>` с `name` вы скопировали на первом шаге (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Если вы хотите указать владельцев, узнайте [способы получения идентификаторов объектов пользователя](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Имя элемента  | Обязательно для заполнения | type   | Описание                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Нет      | String | Отображаемое имя подписки. Если нет — ему присваивается имя предложения, например "Microsoft Azure Enterprise".                                 |
| `offer-type`   | Да      | String | Предложение подписки. Доступны два варианта предложения EA: [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (для использования в рабочей среде) и [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (для разработки и тестирования, должен быть [включен с помощью портала EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Да       | String | Идентификатор объекта учетной записи для регистрации, в которой создается подписка и для которой выставляются счета. Это значение GUID, которое вы получили с помощью `az billing enrollment-account list`. |
| `owner-object-id`      | Нет       | String | Идентификатор объекта любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании.  |
| `owner-upn`    | Нет       | String | Адрес электронной почты любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании. Можно использовать этот параметр вместо `owner-object-id`.|
| `owner-spn` | Нет       | String | Идентификатор приложения любого субъекта-службы, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании. Можно использовать этот параметр вместо `owner-object-id`. При использовании этого параметра служба должна иметь [доступ на чтение к каталогу](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Полный список параметров приведен в разделе о команде [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

---

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Ограничения API создания подписок Azure Enterprise

- С помощью этого API могут быть созданы только подписки Azure Enterprise.
- Нет изначальное ограничение в 50 подписок на учетную запись регистрации, но вы можете [создать запрос в службу поддержки](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) увеличить до 200. После этого подписки могут создаваться только в центре учетных записей.
- В учетной записи должна быть по крайней мере одна подписка EA или подписка EA "Разработка и тестирование". Это означает, что владелец учетной записи по крайней мере один раз выполнил регистрацию вручную.
- Пользователи, которые не являются владельцами учетной записи, но были добавлены в учетную запись регистрации с помощью RBAC, не могут создавать подписки, используя центр учетных записей.
- Невозможно выбрать клиент для создания подписки. Подписка всегда создается в домашнем клиенте владельца учетной записи. Чтобы переместить подписку в другой клиент, ознакомьтесь с [изменением подписки клиента](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>Дальнейшие действия

* Создание подписок с помощью .NET: [пример кода на сайте GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* После создания подписки можно предоставить эту возможность для других пользователей и субъектов-служб. Дополнительные сведения см. статье [Предоставление доступа к созданию подписок Azure Enterprise (предварительная версия)](grant-access-to-create-subscription.md).
* Дополнительные сведения о переносе большого числа подписок с помощью групп управления см. в руководстве по [упорядочиванию ресурсов с помощью групп управления Azure](management-groups-overview.md).
