---
title: Управление назначениями с помощью PowerShell
description: Узнайте, как управлять назначениями схем с помощью официального модуля PowerShell для чертежей Azure, AZ. чертеж.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: beaa3f4c5ab272592e7fae5a95b40a9b586aaf65
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232885"
---
# <a name="how-to-manage-assignments-with-powershell"></a>Управление назначениями с помощью PowerShell

Назначение схемы можно управлять с помощью модуля **AZ. чертеж** Azure PowerShell. Модуль поддерживает выборку, создание, обновление и удаление назначений. Модуль также может получать сведения о существующих определениях схем. В этой статье описывается, как установить модуль и приступить к его использованию.

## <a name="add-the-azblueprint-module"></a>Добавьте модуль AZ. чертеж

Чтобы разрешить Azure PowerShell управлять назначениями схем, необходимо добавить модуль. Этот модуль можно использовать с локально установленной средой PowerShell, с [Azure Cloud Shell](https://shell.azure.com) либо с [образом Docker Azure PowerShell](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Основные требования

Для модуля "проекты Azure" требуется следующее программное обеспечение:

- Azure PowerShell 1.5.0 или более поздней версии. Если она еще не установлена, выполните [эти инструкции](/powershell/azure/install-az-ps).
- Модуль PowerShellGet 2.0.1 или более поздней версии. Если он еще не установлен или не обновлен, выполните [эти инструкции](/powershell/gallery/installing-psget).

### <a name="install-the-module"></a>Установка модуля

Модуль чертежей для PowerShell — **AZ. чертеж**.

1. Запустите следующие команды из командной строки PowerShell **с правами администратора**.

   ```azurepowershell-interactive
   # Install the Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > Если **AZ. Accounts** уже установлено, для принудительной установки может потребоваться `-AllowClobber` использовать.

1. Проверьте, что модуль был импортирован и имеет требуемую версию (0.1.0):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>Получение определений схем

Первый шаг для работы с назначением часто получает ссылку на определение схемы.
`Get-AzBlueprint` Командлет возвращает одно или несколько определений схем. Командлет может получать определения схем из группы управления с помощью `-ManagementGroupId {mgId}` или подписки с. `-SubscriptionId {subId}` Параметр **Name** получает определение схемы, но должен использоваться с **ManagementGroupId** или **SubscriptionId**. **Версию** можно использовать с **именем** , чтобы более явно определить, какое определение схемы возвращается. Вместо **Version**, параметр `-LatestPublished` извлекает последнюю опубликованную версию.

В следующем примере используется `Get-AzBlueprint` для получения всех версий определения схемы с именем "101-чертежи-Definition-Subscription" из конкретной подписки, представленной в виде `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

Пример выходных данных для определения схемы с несколькими версиями выглядит следующим образом:

```output
Name                 : 101-blueprints-definition-subscription
Id                   : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101
                       -blueprints-definition-subscription
DefinitionLocationId : {subId}
Versions             : {1.0, 1.1}
TimeCreated          : 2019-02-25
TargetScope          : Subscription
Parameters           : {storageAccount_storageAccountType, storageAccount_location,
                       allowedlocations_listOfAllowedLocations, [Usergrouporapplicationname]:Reader_RoleAssignmentName}
ResourceGroups       : ResourceGroup
```

[Параметры](../concepts/parameters.md#blueprint-parameters) схемы для определения схемы можно расширить, чтобы предоставить дополнительные сведения.

```azurepowershell-interactive
$blueprints.Parameters
```

```output
Key                                                    Value
---                                                    -----
storageAccount_storageAccountType                      Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
storageAccount_location                                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
allowedlocations_listOfAllowedLocations                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
[Usergrouporapplicationname]:Reader_RoleAssignmentName Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
```

## <a name="get-blueprint-assignments"></a>Получение назначений схем

Если назначение схемы уже существует, можно получить ссылку на него с помощью `Get-AzBlueprintAssignment` командлета. Командлет принимает **SubscriptionId** и **Name** как необязательные параметры. Если значение **SubscriptionId** не указано, используется текущий контекст подписки.

В следующем примере используется `Get-AzBlueprintAssignment` для получения одного назначения схемы с именем "назначение-блокировка-ресурс-группы" из конкретной подписки, представленной `{subId}`в виде:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

Пример выходных данных для назначения схемы выглядит следующим образом:

```output
Name              : Assignment-lock-resource-groups
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssignme
                    nts/Assignment-lock-resource-groups
Scope             : /subscriptions/{subId}
LastModified      : 2019-02-19
LockMode          : AllResourcesReadOnly
ProvisioningState : Succeeded
Parameters        :
ResourceGroups    : ResourceGroup
```

## <a name="create-blueprint-assignments"></a>Создание назначений схем

Если назначение схемы еще не существует, его можно создать с помощью `New-AzBlueprintAssignment` командлета. Этот командлет использует следующие параметры:

- **Имя** необходимости
  - Задает имя назначения схемы
  - Должно быть уникальным и не существовать в **SubscriptionId**
- Схема необходимости
  - Указывает определение схемы для назначения
  - Используйте `Get-AzBlueprint` для получения объекта Reference
- **Расположение** необходимости
  - Задает регион для назначаемого системой объекта развертывания управляемого удостоверения и подписки, который будет создан в
- **Подписка** используемых
  - Указывает подписку, в которой развернуто назначение
  - Если не указано, по умолчанию используется текущий контекст подписки
- **Блокировка** используемых
  - Определяет [блокировку ресурсов](../concepts/resource-locking.md) схемы для использования в развернутых ресурсах
  - Поддерживаемые параметры: _None_, _аллресаурцесреадонли_, _аллресаурцесдонотделете_
  - Если не указано, по умолчанию используется значение _None_
- **Системассигнедидентити** используемых
  - Выберите, чтобы создать управляемое системой удостоверение для назначения и развертывания ресурсов.
  - Значение по умолчанию для набора параметров "Identity"
  - Не может использоваться с **усерассигнедидентити**
- **Усерассигнедидентити** используемых
  - Указывает назначаемое пользователем управляемое удостоверение, которое будет использоваться для назначения и развертывания ресурсов.
  - Часть набора параметров "Identity"
  - Не может использоваться с **системассигнедидентити**
- **Параметр** используемых
  - [Хэш-таблица](/powershell/module/microsoft.powershell.core/about/about_hash_tables) пар "ключ-значение" для установки [динамических параметров](../concepts/parameters.md#dynamic-parameters) в назначении схемы
  - Значение по умолчанию для динамического параметра — это **DefaultValue** в определении
  - Если параметр не указан и не имеет **DefaultValue**, параметр не является обязательным.

    > [!NOTE]
    > **Параметр** не поддерживает секурестрингс.

- **Ресаурцеграуппараметер** используемых
  - [Хэш-таблица](/powershell/module/microsoft.powershell.core/about/about_hash_tables) артефактов группы ресурсов
  - Каждый заполнитель артефакта группы ресурсов будет иметь пары "ключ-значение" для динамической установки **имени** и (или) **расположения** в этом артефакте группы ресурсов.
  - Если параметр группы ресурсов не указан и не имеет **DefaultValue**, параметр группы ресурсов не является обязательным.

В следующем примере создается новое назначение версии "1,1" определения схемы "My-чертеж", полученного с помощью `Get-AzBlueprint`, которое устанавливает для управляемого удостоверения и расположения объекта назначения значение "westus2", блокирует ресурсы с помощью _аллресаурцесреадонли._ и задает хэш-таблицы для **параметра** и **ресаурцеграуппараметер** в конкретной подписке, представленной `{subId}`в виде:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadyOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

Пример выходных данных для создания назначения схемы выглядит следующим образом:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadyOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="update-blueprint-assignments"></a>Обновление назначений схем

Иногда необходимо обновить назначение схемы, которое уже было создано. `Set-AzBlueprintAssignment` Командлет обрабатывает это действие. Командлет принимает большинство тех же параметров, что `New-AzBlueprintAssignment` и командлет, что позволяет обновлять все, что было установлено в назначении. Исключениями являются _имя_, _проект_и идентификатор _подписки_. Обновляются только указанные значения.

Сведения о том, что происходит при обновлении назначения схемы, см. в разделе [правила обновления назначений](./update-existing-assignments.md#rules-for-updating-assignments).

- **Имя** необходимости
  - Задает имя обновляемого назначения схемы
  - Используется для нахождение назначения для обновления, а не для изменения назначения
- Схема необходимости
  - Задает определение схемы для назначения схемы
  - Используйте `Get-AzBlueprint` для получения объекта Reference
  - Используется для нахождение назначения для обновления, а не для изменения назначения
- **Расположение** используемых
  - Задает регион для назначаемого системой объекта развертывания управляемого удостоверения и подписки, который будет создан в
- **Подписка** используемых
  - Указывает подписку, в которой развернуто назначение
  - Если не указано, по умолчанию используется текущий контекст подписки
  - Используется для нахождение назначения для обновления, а не для изменения назначения
- **Блокировка** используемых
  - Определяет [блокировку ресурсов](../concepts/resource-locking.md) схемы для использования в развернутых ресурсах
  - Поддерживаемые параметры: _None_, _аллресаурцесреадонли_, _аллресаурцесдонотделете_
- **Системассигнедидентити** используемых
  - Выберите, чтобы создать управляемое системой удостоверение для назначения и развертывания ресурсов.
  - Значение по умолчанию для набора параметров "Identity"
  - Не может использоваться с **усерассигнедидентити**
- **Усерассигнедидентити** используемых
  - Указывает назначаемое пользователем управляемое удостоверение, которое будет использоваться для назначения и развертывания ресурсов.
  - Часть набора параметров "Identity"
  - Не может использоваться с **системассигнедидентити**
- **Параметр** используемых
  - [Хэш-таблица](/powershell/module/microsoft.powershell.core/about/about_hash_tables) пар "ключ-значение" для установки [динамических параметров](../concepts/parameters.md#dynamic-parameters) в назначении схемы
  - Значение по умолчанию для динамического параметра — это **DefaultValue** в определении
  - Если параметр не указан и не имеет **DefaultValue**, параметр не является обязательным.

    > [!NOTE]
    > **Параметр** не поддерживает секурестрингс.

- **Ресаурцеграуппараметер** используемых
  - [Хэш-таблица](/powershell/module/microsoft.powershell.core/about/about_hash_tables) артефактов группы ресурсов
  - Каждый заполнитель артефакта группы ресурсов будет иметь пары "ключ-значение" для динамической установки **имени** и (или) **расположения** в этом артефакте группы ресурсов.
  - Если параметр группы ресурсов не указан и не имеет **DefaultValue**, параметр группы ресурсов не является обязательным.

В следующем примере обновляется назначение версии "1,1" определения схемы "My-чертеж", полученное с `Get-AzBlueprint` помощью изменения режима блокировки:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

Пример выходных данных для создания назначения схемы выглядит следующим образом:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesDoNotDelete
ProvisioningState : Updating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="remove-blueprint-assignments"></a>Удалить назначения чертежей

При удалении `Remove-AzBlueprintAssignment` назначения схемы это действие обрабатывается командлетом. Командлет принимает либо **имя** , либо **InputObject** , чтобы указать назначение схемы для удаления. Идентификатор **SubscriptionId** является _обязательным_ и должен быть указан во всех случаях.

В следующем примере выполняется выборка существующего назначения схемы с `Get-AzBlueprintAssignment` помощью, а затем она удаляется из конкретной подписки `{subId}`, представленной следующим образом:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>Пример сквозного кода

Выполнив все шаги вместе, в следующем примере получается определение схемы, затем создается, обновляется и удаляется назначение схемы в конкретной подписке, представленной `{subId}`в виде:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

#region GetBlueprint
# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'
#endregion

#region CreateAssignment
# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadyOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
#endregion CreateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region UpdateAssignment
# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
#endregion UpdateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region RemoveAssignment
# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $bpAssignment -SubscriptionId '{subId}'
#endregion
```

## <a name="next-steps"></a>Следующие шаги

- Ознакомьтесь со сведениями о [жизненном цикле схем](../concepts/lifecycle.md).
- Узнайте, как использовать [статические и динамические параметры](../concepts/parameters.md).
- Научитесь настраивать [последовательность схемы](../concepts/sequencing-order.md).
- Узнайте, как применять [блокировку ресурсов схемы](../concepts/resource-locking.md).
- Устраняйте проблемы, возникающие во время назначения схемы, с помощью [общих инструкций по устранению неполадок](../troubleshoot/general.md).