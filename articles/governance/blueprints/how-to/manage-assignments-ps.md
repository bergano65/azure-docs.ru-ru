---
title: Как управлять назначением с помощью PowerShell
description: Сведения об управлении назначения схем с официальной чертежей модуль Azure PowerShell, Az.Blueprint.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 47026975a5142760f90a1a5712b532519f969fd7
ms.sourcegitcommit: 4133f375862fdbdec07b70de047d70c66ac29d50
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/15/2019
ms.locfileid: "57997642"
---
# <a name="how-to-manage-assignments-with-powershell"></a>Как управлять назначением с помощью PowerShell

Назначение плана можно управлять при помощи **Az.Blueprint** модуля Azure PowerShell. Модуль поддерживает получение, создание, обновление и удаление назначений. Модуль также можно извлечь подробности на существующих определений схем. В этой статье описывается, как установить модуль и их использовании.

## <a name="add-the-azblueprint-module"></a>Добавить модуль Az.Blueprint

Чтобы включить Azure PowerShell для управления назначения схем, необходимо добавить модуль. Этот модуль можно использовать с локально установленной средой PowerShell, с [Azure Cloud Shell](https://shell.azure.com) либо с [образом Docker Azure PowerShell](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Основные требования

Проекты Azure модуля требуется следующее программное обеспечение:

- Azure PowerShell версии 1.5.0 или более поздней версии. Если она еще не установлена, выполните [эти инструкции](/powershell/azure/install-az-ps).
- Модуль PowerShellGet 2.0.1 или более поздней версии. Если он еще не установлен или не обновлен, выполните [эти инструкции](/powershell/gallery/installing-psget).

### <a name="install-the-module"></a>Установка модуля

Модуль PowerShell чертежей **Az.Blueprint**.

1. Запустите следующие команды из командной строки PowerShell **с правами администратора**.

   ```azurepowershell-interactive
   # Install the Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > Если **Az.Accounts** — уже установлен, может потребоваться использовать `-AllowClobber` для принудительной установки.

1. Проверьте, что модуль был импортирован и имеет требуемую версию (0.1.0):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>Получение определений схемы

Первый шаг к работе с назначением часто является получение ссылки на определение схемы.
`Get-AzBlueprint` Командлет возвращает один или несколько определений схем. Командлет можно получить определения схем из группы управления с `-ManagementGroupId {mgId}` или подписки с помощью `-SubscriptionId {subId}`. **Имя** параметр получает определение схемы, но необходимо использовать с **ManagementGroupId** или **SubscriptionId**. **Версия** может использоваться с **имя** более явное определение какие схемы возвращается. Вместо **версии**, коммутатор `-LatestPublished` grabs наиболее недавно опубликованной версии.

В следующем примере используется `Get-AzBlueprint` для получения всех версий определение схемы с именем "101-чертежей definition-subscription" из определенной подписки, представленное в виде `{subId}`:

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

[Схемы, в которой параметры](../concepts/parameters.md#blueprint-parameters) с чертежом определение можно развернуть для предоставления дополнительных сведений.

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

## <a name="get-blueprint-assignments"></a>Получение назначения схем

Если назначение плана уже существует, можно получить ссылку на него с помощью `Get-AzBlueprintAssignment` командлета. Командлет принимает **SubscriptionId** и **имя** как необязательные параметры. Если **SubscriptionId** не указан, используется контекст текущей подписки.

В следующем примере используется `Get-AzBlueprintAssignment` получить назначение в рамках одной схемы, с именем «Назначение lock-resource-groups» из определенной подписки, представленное в виде `{subId}`:

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

## <a name="create-blueprint-assignments"></a>Создание назначения схем

Если назначение плана еще не создан, можно создать его с помощью `New-AzBlueprintAssignment` командлета. Этот командлет использует следующие параметры:

- **Имя** [обязательный параметр]
  - Указывает имя назначения схемы
  - Должно быть уникальным и не существовать в **SubscriptionId**
- **В рамках схемы** [обязательный параметр]
  - Указывает определение схемы для назначения
  - Используйте `Get-AzBlueprint` для получения объекта ссылки
- **Расположение** [обязательный параметр]
  - Задает область, назначенный системой управляемого удостоверения и подписки развертывания объекта должен быть создан в
- **Подписка** (необязательно)
  - Указывает подписку, которую развертывается назначения
  - Если не указано, то по умолчанию используется контекст текущей подписки
- **Блокировка** (необязательно)
  - Определяет [схемы, в которой блокировки ресурсов](../concepts/resource-locking.md) для развернутых ресурсов
  - Поддерживаемые параметры: _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
  - Если не указано, по умолчанию _None_
- **SystemAssignedIdentity** (необязательно)
  - Выберите для создания управляемого удостоверения для назначения, назначенное системой, так и для развертывания ресурсов
  - По умолчанию для набора параметра «identity»
  - Нельзя использовать с **UserAssignedIdentity**
- **UserAssignedIdentity** (необязательно)
  - Указывает, назначаемого пользователем управляемого удостоверения для используются для назначения и развертывания ресурсов
  - Параметр «identity» входит в группу
  - Нельзя использовать с **SystemAssignedIdentity**
- **Параметр** (необязательно)
  - Объект [хэш-таблицу](/powershell/module/microsoft.powershell.core/about/about_hash_tables) пар ключ значение для параметра [динамических параметров](../concepts/parameters.md#dynamic-parameters) на назначения схемы
  - По умолчанию для динамического параметра это **defaultValue** в определении
  - Если параметр не указан и не имеет **defaultValue**, параметр является обязательным

    > [!NOTE]
    > **Параметр** не поддерживает secureStrings.

- **ResourceGroupParameter** (необязательно)
  - Объект [хэш-таблицу](/powershell/module/microsoft.powershell.core/about/about_hash_tables) артефактов группы ресурсов
  - Каждый заполнитель артефакта группы ресурсов будут иметь пары ключ/значение для динамической установки **имя** и/или **расположение** на этот артефакт группы ресурсов
  - Если не указан параметр группы ресурсов и не имеет **defaultValue**, параметр группы ресурсов является обязательным

В следующем примере создается новое назначение версии «1.1"Определение схемы «Мой проект», с помощью `Get-AzBlueprint`, задает управляемого удостоверения и назначение расположение объекта на «westus2», блокирует ресурсы с  _AllResourcesReadOnly_и задает хэш-таблиц для обоих **параметр** и **ResourceGroupParameter** на определенной подписки, представленное в виде `{subId}`:

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

В примере выходных данных для создания назначения схемы выглядит следующим образом:

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

## <a name="update-blueprint-assignments"></a>Обновить назначения схем

Иногда бывает необходимо обновить назначение плана, который уже был создан. `Set-AzBlueprintAssignment` Командлет обрабатывает это действие. Командлет принимает большинство те же параметры, `New-AzBlueprintAssignment` командлета, позволяя все, что было задано для назначения обновляться. Исключение составляют _имя_, _Blueprint_, и _SubscriptionId_. Обновляются только указанными значениями.

Чтобы понять, что происходит при обновлении назначения схемы, см. в разделе [правила обновления назначений](./update-existing-assignments.md#rules-for-updating-assignments).

- **Имя** [обязательный параметр]
  - Указывает имя назначения схемы для обновления
  - Позволяет найти назначения для обновления, а не к изменить назначение
- **В рамках схемы** [обязательный параметр]
  - Указывает определение схемы для назначения схемы
  - Используйте `Get-AzBlueprint` для получения объекта ссылки
  - Позволяет найти назначения для обновления, а не к изменить назначение
- **Расположение** (необязательно)
  - Задает область, назначенный системой управляемого удостоверения и подписки развертывания объекта должен быть создан в
- **Подписка** (необязательно)
  - Указывает подписку, которую развертывается назначения
  - Если не указано, то по умолчанию используется контекст текущей подписки
  - Позволяет найти назначения для обновления, а не к изменить назначение
- **Блокировка** (необязательно)
  - Определяет [схемы, в которой блокировки ресурсов](../concepts/resource-locking.md) для развернутых ресурсов
  - Поддерживаемые параметры: _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
- **SystemAssignedIdentity** (необязательно)
  - Выберите для создания управляемого удостоверения для назначения, назначенное системой, так и для развертывания ресурсов
  - По умолчанию для набора параметра «identity»
  - Нельзя использовать с **UserAssignedIdentity**
- **UserAssignedIdentity** (необязательно)
  - Указывает, назначаемого пользователем управляемого удостоверения для используются для назначения и развертывания ресурсов
  - Параметр «identity» входит в группу
  - Нельзя использовать с **SystemAssignedIdentity**
- **Параметр** (необязательно)
  - Объект [хэш-таблицу](/powershell/module/microsoft.powershell.core/about/about_hash_tables) пар ключ значение для параметра [динамических параметров](../concepts/parameters.md#dynamic-parameters) на назначения схемы
  - По умолчанию для динамического параметра это **defaultValue** в определении
  - Если параметр не указан и не имеет **defaultValue**, параметр является обязательным

    > [!NOTE]
    > **Параметр** не поддерживает secureStrings.

- **ResourceGroupParameter** (необязательно)
  - Объект [хэш-таблицу](/powershell/module/microsoft.powershell.core/about/about_hash_tables) артефактов группы ресурсов
  - Каждый заполнитель артефакта группы ресурсов будут иметь пары ключ/значение для динамической установки **имя** и/или **расположение** на этот артефакт группы ресурсов
  - Если не указан параметр группы ресурсов и не имеет **defaultValue**, параметр группы ресурсов является обязательным

В следующем примере обновляется назначение версии «1.1"Определение схемы «Мой проект», с помощью `Get-AzBlueprint` , изменив режим блокировки:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

В примере выходных данных для создания назначения схемы выглядит следующим образом:

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

## <a name="remove-blueprint-assignments"></a>Удаление назначения схем

Когда придет время для назначения схемы удалены, `Remove-AzBlueprintAssignment` командлет обрабатывает это действие. Командлет принимает либо **имя** или **InputObject** чтобы указать, какие схемы, в которой назначения для удаления. **SubscriptionId** — _требуется_ и должно быть указано во всех случаях.

Следующий пример извлекает существующего назначения схемы с `Get-AzBlueprintAssignment` и затем удаляет его из определенной подписки, представленное в виде `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>Пример кода End-to-end

Объединяя все шаги, следующий пример возвращает определение схемы, затем создает, обновляет и удаляет назначение плана в определенной подписки, представленное в виде `{subId}`:

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

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь с [жизненным циклом схемы](../concepts/lifecycle.md).
- Узнайте, как использовать [статические и динамические параметры](../concepts/parameters.md).
- Научитесь настраивать [последовательность схемы](../concepts/sequencing-order.md).
- Узнайте, как применять [блокировку ресурсов схемы](../concepts/resource-locking.md).
- Устраняйте проблемы, возникающие во время назначения схемы, с помощью [общих инструкций по устранению неполадок](../troubleshoot/general.md).