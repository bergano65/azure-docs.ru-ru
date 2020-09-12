---
title: Перемещение ресурсов между регионами с помощью PowerShell в средстве перемещения ресурсов Azure
description: Узнайте, как перемещать ресурсы между регионами с помощью PowerShell в службе "перенос ресурсов Azure".
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 3236e0a95c6a4b4f57ac38ed067011c3d6848b5a
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89670799"
---
# <a name="move-resources-across-regions-in-powershell"></a>Перемещение ресурсов между регионами в PowerShell

Узнайте, как переместить ресурсы Azure в другой регион с помощью PowerShell в службе "перенос ресурсов Azure". 

> [!NOTE]
> Сейчас для перемещения ресурсов Azure доступна предварительная версия.



## <a name="before-you-start"></a>Перед началом работы

- Ваша подписка Azure должна иметь доступ к переписке ресурсов, и у вас должны быть разрешения [владельца](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) или [администратора доступа пользователей](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) для подписки.
- Средство перемещения ресурсов не следит за изменениями и обновлениями, поэтому перед перемещением необходимо внести необходимые изменения в ресурсы.
- При перемещении ресурсов с помощью PowerShell невозможно изменить параметры целевой области. Измените эти параметры непосредственно на портале.
- При добавлении ресурсов в коллекцию перемещения при подготовке к перемещению в другой регион метаданные о перемещении сохраняются в группе ресурсов, созданной для этой цели. В настоящее время эта группа ресурсов может находиться в восточной части США 2 или Северной Европе. Ресурсы Azure можно перемещать между любыми общедоступными регионами с помощью метаданных, имеющихся в любом из этих регионов.

## <a name="sample-values"></a>Примеры значений

Мы используем эти значения в нашем примере сценария:

**Параметр** | **Значение** 
--- | ---
Идентификатор подписки | subscription-id
Расположение службы перемещения ресурсов | восточная часть США 2
Группа ресурсов метаданных | Регионмоверг-centralus-westcentralus
Расположение группы ресурсов метаданных | восточная часть США 2
Переместить имя коллекции | Мовеколлектион-centralus-westcentralus
Исходный регион |  centralus
Исходная группа ресурсов | псдеморм
Целевой регион | westcentralus
Целевая группа ресурсов | псдемормтгт
Виртуальная машина для перемещения | псдемовм

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите в подписку Azure.

```azurepowershell-interactive
# Sign in to an Azure subscription
Connect-AzAccount – Subscription "<subscription-id>"
```

## <a name="create-the-resource-group-for-metadata"></a>Создание группы ресурсов для метаданных

Создайте группу ресурсов для хранения метаданных и сведений о конфигурации перемещения коллекции.

```azurepowershell-interactive
# Sign in to an Azure subscription
New-AzResourceGroup -Name MoveCollection-centralus-westcentralus -Location "East US 2"
```

**Ожидаемый результат**:

![Вывод текста после создания группы ресурсов](./media/move-region-powershell/output-create-resource-group.png)

## <a name="register-the-resource-provider"></a>Регистрация поставщика ресурсов

Зарегистрируйте поставщик ресурсов Microsoft. Migrate, чтобы можно было создать ресурс Мовеколлектион.

```azurepowershell-interactive
# Register the provider
Register-AzResourceProvider -ProviderNamespace Microsoft.Migrate 

# Wait for registration
While(((Get-AzResourceProvider -ProviderNamespace Microsoft.Migrate)| where {$_.RegistrationState -eq "Registered" -and $_.ResourceTypes.ResourceTypeName -eq "moveCollections"}|measure).Count -eq 0)
  {
      Start-Sleep -Seconds 5
      Write-Output "Waiting for registration to complete."
  }
```

## <a name="create-the-move-collection"></a>Создание коллекции перемещения

Объект Мовеколлектион хранит метаданные и сведения о конфигурации ресурсов, которые требуется переместить.

- Чтобы объект Мовеколлектион имела доступ к подписке, в которой находится служба перемещения ресурсов Azure, ей требуется [управляемое системой удостоверение](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (ранее известное как управляемая служба идентификации (MSI)), которому доверяет подписка.
- Удостоверению назначается роль "участник" или "администратор доступа пользователей" для исходной подписки.
- Чтобы назначить роль удостоверению, требуется роль в подписке (например, владелец или администратор доступа пользователей) с этими разрешениями:
    -  Microsoft.Authorization/roleAssignments/write.
    - Microsoft.Authorization/roleAssignments/delete


```azurepowershell-interactive
# Create a MoveCollection object
New-AzResourceMoverMoveCollection -Name "MoveCollection-centralus-westcentralus " -ResourceGroupName "RegionMoveRG-centralus-westcentralus " -SubscriptionId "<subscription-id>" -SourceRegion "centralus" -TargetRegion "westcentralus" -Location  "East US 2" -IdentityType SystemAssigned
```
**Ожидаемый результат**:

![Выходной текст после создания коллекции перемещений](./media/move-region-powershell/output-move-collection.png)


## <a name="assign-a-managed-identity"></a>Назначение управляемого удостоверения 

Задайте идентификатор подписки, получите субъект удостоверения объекта Мовеколлектион и назначьте ему роли Azure.


```azurepowershell-interactive
# Set the subscriptionId
$subscriptionId = "<subscription-id>"

# Retrieve the principal managed identity of the MoveCollection
$moveCollection = Get-AzResourceMoverMoveCollection -SubscriptionId $subscriptionId -ResourceGroupName "RegionMoveRG-centralus-westcentralus " -Name "MoveCollection-centralus-westcentralus "

# Set the IdentityPrincipalID
$identityPrincipalId = $moveCollection.IdentityPrincipalId

# Assign the role to the IdentityPrincipalID
New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName Contributor -Scope "/subscriptions/$subscriptionId"

New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName "User Access Administrator" -Scope "/subscriptions/$subscriptionId"

```

## <a name="add-resources-to-the-move-collection"></a>Добавление ресурсов в коллекцию перемещения

Получите идентификатор исходного ресурса, который необходимо переместить. Затем добавьте его в коллекцию Move.

```azurepowershell-interactive
# Retrieve the resource ID
Get-AzResource -Name PSDemoVM -ResourceGroupName PSDemoRM
```

**Ожидаемые выходные данные**

![Выходной текст после получения идентификатора ресурса](./media/move-region-powershell/output-retrieve-resource.png)


```azurepowershell-interactive
# Add the resource to the move collection
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -SourceId “/subscriptions/e80eb9fa-c996-4435-aa32-5af6f3d3077c/resourceGroups/PSDemoRM/providers/Microsoft.Compute/virtualMachines/PSDemoVM” -Name “PSDemoVM” -ResourceSettingResourceType “ Microsoft.Compute/virtualMachines” -ResourceSettingTargetResourceName “PSDemoVM”
```

**Ожидаемый результат** 
 ![ Вывод текста после добавления ресурса](./media/move-region-powershell/output-add-resource.png)

## <a name="resolve-dependencies"></a>Устранение ошибок, связанных с зависимостями

Проверьте добавленные ресурсы и устраните все зависимости от других ресурсов.

```azurepowershell-interactive
# Resolve dependencies
Resolve-AzResourceMoverMoveCollectionDependency -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus”
```
**Вывод, если существуют зависимости**

Если ресурс в коллекции Move имеет зависимости от других ресурсов, выдается сообщение о сбое.

![Вывод текста после проверки зависимостей](./media/move-region-powershell/dependency-error.png)

### <a name="retrieve-dependencies"></a>Получить зависимости

Если ресурс, который требуется переместить, зависит от других ресурсов, можно получить сведения об отсутствующих зависимостях.

```azurepowershell-interactive
# Identify dependencies
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus ” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -SubscriptionId  “<subscription-id>”
```

**Ожидаемые выходные данные**

![Вывод текста после получения зависимостей](./media/move-region-powershell/missing-dependencies.png)

В этом примере две зависимости определяются как отсутствующие:

- Исходная группа ресурсов: Псдеморм
- Сетевая карта на виртуальной машине: PSDemoVM62

## <a name="add-dependencies-to-collection"></a>Добавить зависимости в коллекцию


Используя полученные идентификаторы ресурсов, найдите имена недостающих ресурсов и добавьте их в коллекцию перемещения.

### <a name="add-the-nic"></a>Добавление сетевой карты

Получите имя и тип сетевого адаптера и добавьте его в коллекцию.

```azurepowershell-interactive
# Get the NIC name and resource type
Get-AzResource -ResourceId “/subscriptions/ <subscription-id> /resourcegroups/psdemorm/providers/microsoft.network/networkinterfaces/psdemovm62”

```

**Ожидаемые выходные данные**

![Вывод текста после получения сетевого адаптера](./media/move-region-powershell/output-retrieve-nic.png)

Теперь добавьте сетевую карту в коллекцию перемещения. В этом примере для целевого сетевого адаптера выдается то же имя. Обеспечьте соответствие параметров и регистра.

```azurepowershell-interactive
# Add the NIC to the collection. 
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -SourceId “/subscriptions/<subscription-id>/resourceGroups/PSDemoRM/providers/Microsoft.Network/networkInterfaces/psdemovm62” -Name “psdemovm62” -ResourceSettingResourceType “Microsoft.Network/networkInterfaces” -ResourceSettingTargetResourceName “psdemovm62”
```

**Ожидаемые выходные данные**

![Выходной текст после добавления сетевого адаптера в коллекцию](./media/move-region-powershell/add-nic.png)

## <a name="add-the-source-resource-group"></a>Добавление исходной группы ресурсов

```azurepowershell-interactive
# Get the resource group name and resource type
Get-AzResource -ResourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm”

# Add the resource group to the collection. 
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -SourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm” -Name “psdemorm” -ResourceSettingResourceType “resourcegroups” -ResourceSettingTargetResourceName “psdemorm”
```

**Ожидаемые выходные данные**

![Выходной текст после добавления группы ресурсов в коллекцию](./media/move-region-powershell/add-source-resource-group.png)

## <a name="recheck-dependencies"></a>Повторная проверка зависимостей

Проверьте наличие недостающих зависимостей.

```azurepowershell-interactive
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus ” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -SubscriptionId  “<subscription-id>”
```

**Выходные данные**

У нас есть дополнительные недостающие зависимости.

![Вывод текста после проверки зависимостей](./media/move-region-powershell/recheck-dependencies.png)

## <a name="retrieve-additional-dependencies"></a>Получение дополнительных зависимостей

```azurepowershell-interactive
# Identify dependencies
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus ” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -SubscriptionId  “<subscription-id>”
```

**Ожидаемые выходные данные**


![Вывод текста после получения дополнительных зависимостей](./media/move-region-powershell/additional-missing-dependencies.png)

В этом примере три дополнительные зависимости определяются как отсутствующие:

- Общедоступный IP-адрес: псдемовм-IP
- Виртуальная сеть Azure: псдеморм-vnet
- Группа безопасности сети (NSG): псдемовм-NSG

## <a name="add-additional-dependencies"></a>Добавить дополнительные зависимости

1. [Следуйте инструкциям](#add-dependencies-to-collection) по добавлению этих ресурсов в коллекцию перемещения.
2. После добавления ресурсов повторите проверку, пока не будут добавлены все зависимости.


## <a name="prepare-and-move-the-source-resource-group"></a>Подготовка и перемещение исходной группы ресурсов

В исходном регионе вы готовите ресурсы перед их перемещением. Процесс подготовки зависит от перемещаемых ресурсов. Например, для подготовки ресурсов без отслеживания состояния можно подготовить и экспортировать шаблон Azure Resource Manager (ARM). Для ресурсов с отслеживанием состояния репликация может запускаться. 

Прежде чем можно будет подготовить исходные ресурсы, необходимо подготовить и переместить исходную группу ресурсов.

### <a name="prepare"></a>Подготовка

```azurepowershell-interactive
# Prepare the source resource group
Invoke-AzResourceMoverPrepare -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -MoveResource “PSDemoRM”
```

**Ожидаемые выходные данные**

![Выходной текст после подготовки исходной группы ресурсов](./media/move-region-powershell/prepare-source-resource-group.png)

### <a name="initiate-move"></a>Начать перемещение

```azurepowershell-interactive
# Initiate move
Invoke-AzResourceMoverInitiateMove -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -MoveResource “PSDemoRM”

**Expected output**

![Output text after initiating move of the source resource group](./media/move-region-powershell/initiate-move-resource-group.png)


```azurepowershell-interactive
# Commit move
Invoke-AzResourceMoverCommit -SubscriptionId “<subscription-id” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “PS-centralus-
westcentralus-demoRM” -MoveResource “PSDemoRM”
```

**Ожидаемые выходные данные**

![Выходной текст после фиксации исходной группы ресурсов](./media/move-region-powershell/commit-move-resource-group.png)


## <a name="prepare-resources"></a>Подготовка ресурсов

После перемещения исходной группы ресурсов в целевой регион извлеките список ресурсов из коллекции перемещения и подготовьте их к перемещению.

```azurepowershell-interactive
# Retrieve resources in the collection
Get-AzResourceMoverMoveResource  -SubscriptionId “ <subscription-id> “ -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ”   | Where-Object {  $_.MoveStatusMoveState -eq “PreparePending” } | Select Name
```
**Ожидаемые выходные данные**

![Вывод текста после получения ресурсов в коллекции](./media/move-region-powershell/collection-resources.png)

Теперь Подготовьте ресурсы.

```azurepowershell-interactive
# Prepare the resources, using the resource name
Invoke-AzResourceMoverPrepare -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
Кроме того, можно подготовить и переместить ресурсы с помощью идентификатора ресурса, а не имени:

```azurepowershell-interactive
# Prepare the resources using the resource ID
Invoke-AzResourceMoverPrepare -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus  -MoveResourceInputType MoveResourceSourceId  -MoveResource $('/subscriptions/<subscription-id>/resourceGroups/PSDemoRM/providers/Microsoft.Network/networkSecurityGroups/PSDemoVM-nsg')
```

**Ожидаемые выходные данные**

![Вывод текста после подготовки ресурсов в коллекции](./media/move-region-powershell/collection-prepare.png)

## <a name="initiate-resource-move"></a>Запустить перемещение ресурсов

После подготовки ресурсов запустите перемещение.

```azurepowershell-interactive
# Initiate the move 
Invoke-AzResourceMoverInitiateMove -SubscriptionId <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**Ожидаемый результат** 
 ![ Вывод текста после инициации перемещения ресурсов](./media/move-region-powershell/initiate-resource-move.png)

## <a name="discard-or-commit-the-move"></a>Отменить или зафиксировать перемещение

После первоначального перемещения можно решить, следует ли зафиксировать перемещение или отменить его. 

- **Отменить**. Вы можете отменить перемещение, если тестируете, и вы не хотите переносить исходный ресурс. При отмене перемещения ресурс возвращается в состояние *Ожидание инициированного перемещения*. При необходимости можно снова запустить перемещение.
- **Фиксация**: фиксация завершает перемещение в целевой регион. После фиксации исходный ресурс будет находиться в состоянии *Ожидание удаления источника*, и вы можете решить, хотите ли вы удалить его.

### <a name="discard"></a>Игнорировать

Чтобы отменить перемещение, сделайте следующее:

```azurepowershell-interactive
# Discard the move 
Invoke-AzResourceMoverDiscard -SubscriptionId  <subscription-id> `-ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**Ожидаемый результат** 
 ![ Вывод текста после отмены перемещения](./media/move-region-powershell/discard-move.png)


### <a name="commit"></a>Commit

Фиксация перемещения:

```azurepowershell-interactive
# Commit the move 
Invoke-AzResourceMoverCommit -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**Ожидаемые выходные данные**

![Вывод текста после фиксации перемещения](./media/move-region-powershell/commit-move.png)

## <a name="delete-source-resources"></a>Удаление исходных ресурсов

После фиксации перемещения и проверки правильности работы ресурсов в целевом регионе можно удалить исходные ресурсы в [портал Azure](../azure-resource-manager/management/manage-resources-portal.md#delete-resources), [с помощью PowerShell](../azure-resource-manager/management/manage-resources-powershell.md#delete-resources)или [Azure CLI](../azure-resource-manager/management/manage-resources-cli.md#delete-resources).

## <a name="next-steps"></a>Дальнейшие действия

[Узнайте, как](remove-move-resources.md) удалить ресурсы из коллекции перемещения.