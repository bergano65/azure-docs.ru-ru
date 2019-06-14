---
title: Ошибки регистрации поставщика ресурсов Azure | Документация Майкрософт
description: В этой статье объясняется, как устранять ошибки регистрации поставщика ресурсов Azure.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 02/15/2019
ms.author: tomfitz
ms.openlocfilehash: 2f3db5e6260b065c83f0e337306d38dca6e5ff51
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60389957"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Устранение ошибок регистрации поставщика ресурсов

В этой статье описываются ошибки, которые могут возникнуть при использовании поставщика ресурсов, который вы еще не использовали в своей подписке.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Симптом

При развертывании ресурсов вы можете получить следующий код ошибки и сообщение об ошибке:

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

Или может появиться похожее сообщение.

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

В сообщении об ошибке должны быть указаны поддерживаемые расположения и версии API. Вы можете изменить шаблон, используя одно из предложенных значений. Большинство поставщиков, но не все, регистрируются автоматически порталом Azure или интерфейсом командной строки, который вы используете. Если ранее вы не использовали конкретный поставщик ресурсов, возможно, потребуется зарегистрировать такой поставщик.

Или при выключении автоматического завершения работы для виртуальных машин может появиться похожее сообщение об ошибке:

```
Code: AuthorizationFailed
Message: The client '<identifier>' with object id '<identifier>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope ...
```

## <a name="cause"></a>Причина:

Эти ошибки возникают по одной из следующих причин:

* Для подписки не зарегистрирован необходимый поставщик ресурсов.
* Версия API не поддерживается для выбранного типа ресурса.
* Расположение не поддерживается для выбранного типа ресурса.
* Для автоматического завершения работы виртуальных машин должен быть зарегистрирован поставщик ресурсов Microsoft.DevTestLab.

## <a name="solution-1---powershell"></a>Решение 1 — PowerShell

Чтобы просмотреть состояние регистрации, используйте командлет PowerShell **Get-AzResourceProvider**.

```powershell
Get-AzResourceProvider -ListAvailable
```

Чтобы зарегистрировать поставщик, используйте командлет **Register-AzResourceProvider** и укажите имя поставщика ресурсов, который необходимо зарегистрировать.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Cdn
```

Чтобы получить поддерживаемые расположения для определенного типа ресурсов, используйте следующую команду:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Чтобы получить поддерживаемые версии API для определенного типа ресурсов, используйте следующую команду:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

## <a name="solution-2---azure-cli"></a>Решение 2 — Azure CLI

Чтобы узнать, зарегистрирован ли поставщик, используйте команду `az provider list` .

```azurecli-interactive
az provider list
```

Чтобы зарегистрировать поставщик ресурсов, используйте команду `az provider register` и укажите *пространство имен* , которое следует зарегистрировать.

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

Чтобы просмотреть поддерживаемые расположения и версии API для типа ресурса, используйте следующую команду.

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>Решение 3 — портал Azure

Просмотреть состояние регистрации и зарегистрировать пространство имен поставщика ресурсов можно на портале.

1. На портале щелкните **Все службы**.

   ![Выбор всех служб](./media/resource-manager-register-provider-errors/select-all-services.png)

1. Выберите **Подписки**.

   ![Выбор пункта "Подписки"](./media/resource-manager-register-provider-errors/select-subscriptions.png)

1. Из списка подписок выберите ту, которую вы хотите использовать для регистрации поставщика ресурсов.

   ![Выбор подписки для регистрации поставщика ресурсов.](./media/resource-manager-register-provider-errors/select-subscription-to-register.png)

1. Для своей подписки выберите **Поставщики ресурсов**.

   ![Выбор поставщика ресурсов](./media/resource-manager-register-provider-errors/select-resource-provider.png)

1. Просмотрите список поставщиков ресурсов и, при необходимости, щелкните ссылку **Зарегистрировать**, чтобы зарегистрировать поставщик ресурсов типа, который вы пытаетесь развернуть.

   ![Перечислить поставщиков ресурсов](./media/resource-manager-register-provider-errors/list-resource-providers.png)
