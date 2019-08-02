---
title: Программное создание подписок Azure Enterprise | Документация Майкрософт
description: Узнайте, как программно создавать дополнительные подписки Azure Enterprise или Azure "Enterprise — разработка и тестирование".
services: azure-resource-manager
author: jureid
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/10/2019
ms.author: jureid
ms.openlocfilehash: 701b35d99cb98009ec0116c23eaeab94ff967f51
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68678934"
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Программное создание подписок Azure Enterprise (предварительная версия)

Как пользователь Azure, подписавший [Соглашение Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), вы можете программно создавать подписки EA (MS-AZR-0017P) и EA "Разработка и тестирование"(MS-AZR-0148P). В этой статье Вы узнаете как создавать подписки программно с помощью Azure Resource Manager.

Когда Вы создаете подписки Azure, создаваемые с помощью этого API, где подписка регулируются соглашением, в соответствии с которым вы получили службы Microsoft Azure от корпорации Майкрософт или уполномоченного торгового посредника. Дополнительные сведения см. на странице [Юридическая информация Службы Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>предварительные требования

У вас должна быть роль владельца учетной записи регистрации, в которой вы хотите создавать подписки. Получить эти роли можно двумя способами:

* Администратор регистрации может [сделать вас владельцем учетной записи](https://ea.azure.com/helpdocs/addNewAccount) (требуется вход), который создает владельца учетной записи регистрации. Следуйте инструкциям из приглашения, полученного по электронной почте, чтобы вручную создать начальную подписку. Подтвердите владение учетной записью и вручную создайте начальную подписку EA, прежде чем переходить к следующему шагу. Просто добавить учетную запись в регистрацию недостаточно.

* Текущий владелец учетной записи регистрации может [предоставить вам права доступа](grant-access-to-create-subscription.md). Аналогично, если вы хотите использовать субъект-службу для создания подписки EA, необходимо [предоставить возможность создания подписки на нее](grant-access-to-create-subscription.md).

## <a name="find-accounts-you-have-access-to"></a>Поиск учетных записей, к которым у вас есть доступ

После того, как вы будете добавлены в регистрацию Azure EA в качестве владельца учетной записи, платформа Azure использует связь между регистрацией и учетной записью, чтобы определить цель для выставления счетов за подписки. Все подписки, созданные с учетной записью, оплачиваются в рамках регистрации EA, к которой относится эта учетная запись. Чтобы создать подписки, необходимо в значениях передать о регистрации учетной записи и субъектов-пользователя — владельца подписки. 

Чтобы выполнить следующие команды, войдите в *домашний каталог* владельца учетной записи, в котором по умолчанию создаются подписки.

## <a name="resttabrest"></a>[REST](#tab/rest)

Запрос на получение списка всех учетных записей регистрации, к которым у вас есть доступ:

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

Используйте свойство `principalName`, чтобы указать учетную запись для оплаты подписок. Скопируйте эту `name` учетную запись. Например, если вы хотите создать подписки в SignUpEngineering@contoso.com учетной записи регистрации, скопируйте. ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` Это идентификатор объекта учетной записи регистрации. Вставьте это значение в любое место, чтобы его можно было использовать на следующем шаге `enrollmentAccountObjectId`как.

## <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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
Используйте свойство `principalName`, чтобы указать учетную запись для оплаты подписок. Скопируйте эту `ObjectId` учетную запись. Например, если вы хотите создать подписки в SignUpEngineering@contoso.com учетной записи регистрации, скопируйте. ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` Вставьте этот идентификатор объекта где-то, чтобы его можно было использовать на следующем шаге в `enrollmentAccountObjectId`качестве.

## <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

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

Используйте свойство `principalName`, чтобы указать учетную запись для оплаты подписок. Скопируйте эту `name` учетную запись. Например, если вы хотите создать подписки в SignUpEngineering@contoso.com учетной записи регистрации, скопируйте. ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` Это идентификатор объекта учетной записи регистрации. Вставьте это значение в любое место, чтобы его можно было использовать на следующем шаге `enrollmentAccountObjectId`как.

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Создание подписок с помощью определенной учетной записи регистрации

В следующем примере создается подписка с именем " *Подписка на группу разработки* " в учетной записи регистрации, выбранной на предыдущем шаге. В качестве подписки используется *MS-AZR-0017P* (обычный Microsoft Соглашение Enterprise). Этот пример также при необходимости добавляет двух пользователей в качестве владельцев (RBAC) для подписки.

# <a name="resttabrest"></a>[REST](#tab/rest)

Выполните следующий запрос, заменив `<enrollmentAccountObjectId>` на `name` копию, скопированную с первого шага```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```(). Если вы хотите указать владельцев, Узнайте, [как получить идентификаторы объектов пользователя](grant-access-to-create-subscription.md#userObjectId).

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

| Имя элемента  | Обязательное значение | Тип   | Описание                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Нет      | Строковое | Отображаемое имя подписки. Если нет — ему присваивается имя предложения, например "Microsoft Azure Enterprise".                                 |
| `offerType`   | Да      | Строковое | Предложение подписки. Доступны два варианта предложения EA: [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (для использования в рабочей среде) и [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (для разработки и тестирования, должен быть [включен с помощью портала EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Нет       | Строковое | Идентификатор объекта любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании.  |

В ответе возвращается объект `subscriptionOperation` для отслеживания. После завершения создания подписки объект `subscriptionOperation` вернет объект `subscriptionLink` с идентификатором подписки.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Сначала установите этот модуль предварительной версии, `Install-Module Az.Subscription -AllowPrerelease`выполнив. Чтобы убедиться в том, что `-AllowPrerelease` работает, установите последнюю версию PowerShellGet из статьи [Получение модуля PowerShellGet](/powershell/gallery/installing-psget).

Выполните приведенную ниже команду [New-азсубскриптион](/powershell/module/az.subscription) , `<enrollmentAccountObjectId>` заменив `ObjectId` данными, собранными на первом```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```шаге (). Если вы хотите указать владельцев, Узнайте, [как получить идентификаторы объектов пользователя](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Имя элемента  | Обязательное значение | Тип   | Описание                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Нет      | Строковое | Отображаемое имя подписки. Если нет — ему присваивается имя предложения, например "Microsoft Azure Enterprise".                                 |
| `OfferType`   | Да      | Строковое | Предложение подписки. Доступны два варианта предложения EA: [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (для использования в рабочей среде) и [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (для разработки и тестирования, должен быть [включен с помощью портала EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Да       | Строковое | Идентификатор объекта учетной записи для регистрации, в которой создается подписка и для которой выставляются счета. Это значение GUID, которое вы получили с помощью `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | Нет       | Строковое | Идентификатор объекта любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании.  |
| `OwnerSignInName`    | Нет       | Строковое | Адрес электронной почты любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании. Можно использовать этот параметр вместо `OwnerObjectId`.|
| `OwnerApplicationId` | Нет       | Строковое | Идентификатор приложения любого субъекта-службы, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании. Можно использовать этот параметр вместо `OwnerObjectId`. При использовании этого параметра служба должна иметь [доступ на чтение к каталогу](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Полный список параметров приведен в разделе о [New-AzSubscription](/powershell/module/az.subscription.preview).

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

Сначала установите это расширение для предварительного просмотра, `az extension add --name subscription`выполнив.

Выполните следующую команду [AZ Account Create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) , заменив `<enrollmentAccountObjectId>` `name` на имя, скопированное на первом шаге (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Если вы хотите указать владельцев, Узнайте, [как получить идентификаторы объектов пользователя](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Имя элемента  | Обязательное значение | Type   | Описание                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Нет      | Строковое | Отображаемое имя подписки. Если нет — ему присваивается имя предложения, например "Microsoft Azure Enterprise".                                 |
| `offer-type`   | Да      | Строковое | Предложение подписки. Доступны два варианта предложения EA: [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (для использования в рабочей среде) и [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (для разработки и тестирования, должен быть [включен с помощью портала EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Да       | Строковое | Идентификатор объекта учетной записи для регистрации, в которой создается подписка и для которой выставляются счета. Это значение GUID, которое вы получили с помощью `az billing enrollment-account list`. |
| `owner-object-id`      | Нет       | Строковое | Идентификатор объекта любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании.  |
| `owner-upn`    | Нет       | Строковое | Адрес электронной почты любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании. Можно использовать этот параметр вместо `owner-object-id`.|
| `owner-spn` | Нет       | Строковое | Идентификатор приложения любого субъекта-службы, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании. Можно использовать этот параметр вместо `owner-object-id`. При использовании этого параметра служба должна иметь [доступ на чтение к каталогу](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Полный список параметров приведен в разделе о команде [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

---

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Ограничения API создания подписок Azure Enterprise

- С помощью этого API могут быть созданы только подписки Azure Enterprise.
- Существует ограничение в 200 подписок на учетную запись регистрации. После этого подписки можно будет создавать только через центр учетных записей. Если вы хотите создать больше подписок через API, создайте другую учетную запись регистрации.
- Пользователи, не являющиеся владельцами учетной записи, но добавленные в учетную запись регистрации через RBAC, не могут создавать подписки с помощью центра управления учетной записью.
- Невозможно выбрать клиент для создания подписки. Подписка всегда создается в домашнем клиенте владельца учетной записи. Чтобы переместить подписку в другой клиент, ознакомьтесь с [изменением подписки клиента](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>Следующие шаги

* Создание подписок с помощью .NET: [пример кода на сайте GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* После создания подписки можно предоставить эту возможность для других пользователей и субъектов-служб. Дополнительные сведения см. статье [Предоставление доступа к созданию подписок Azure Enterprise (предварительная версия)](grant-access-to-create-subscription.md).
* Дополнительные сведения о переносе большого числа подписок с помощью групп управления см. в руководстве по [упорядочиванию ресурсов с помощью групп управления Azure](management-groups-overview.md).
