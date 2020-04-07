---
title: Как управлять назначениями с Помощью PowerShell
description: Узнайте, как управлять назначениями чертежей с помощью официального модуля Azure Blueprints PowerShell, Az.Blueprint.
ms.date: 09/30/2019
ms.topic: how-to
ms.openlocfilehash: 0868e5e207202511c1981a930870bfdc68a77a8f
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677429"
---
# <a name="how-to-manage-assignments-with-powershell"></a>Как управлять назначениями с Помощью PowerShell

Назначение чертежа можно управлять с помощью модуля **Az.Blueprint** Azure PowerShell. Модуль поддерживает извлечение, создание, обновление и удаление заданий. Модуль также может получить подробную информацию о существующих определениях чертежей. В этой статье рассказывается о том, как установить модуль и начать его использование.

## <a name="add-the-azblueprint-module"></a>Добавить модуль Az.Blueprint

Чтобы Azure PowerShell могла управлять назначениями чертежей, модуль должен быть добавлен. Этот модуль можно использовать с локально установленной средой PowerShell, с [Azure Cloud Shell](https://shell.azure.com) либо с [образом Docker Azure PowerShell](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Основные требования

Модуль Azure Blueprints требует следующего программного обеспечения:

- Azure PowerShell 1.5.0 или выше. Если она еще не установлена, выполните [эти инструкции](/powershell/azure/install-az-ps).
- Модуль PowerShellGet 2.0.1 или более поздней версии. Если он еще не установлен или не обновлен, выполните [эти инструкции](/powershell/scripting/gallery/installing-psget).

### <a name="install-the-module"></a>Установка модуля

Модуль Azure Blueprints для PowerShell является **Az.Blueprint**.

1. Запустите следующие команды из командной строки PowerShell **с правами администратора**.

   ```azurepowershell-interactive
   # Install the Azure Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > Если **Az.Accounts** уже установлен, может потребоваться использовать `-AllowClobber` для принудительного установки.

1. Проверка того, что модуль был импортирован и является правильной версией (0.2.6):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>Получить определения чертежей

Первым шагом к работе с назначением часто становится ссылка на определение чертежа.
Cmdlet `Get-AzBlueprint` получает одно или несколько определений чертежей. Cmdlet может получить определения чертежа `-ManagementGroupId {mgId}` от группы `-SubscriptionId {subId}`управления с или подпиской с . Параметр **имени** получает определение чертежа, но должен использоваться с **Помощью ManagementGroupId** или **SubscriptionId.** **Версия** может быть использована с **Name,** чтобы быть более четкой о том, какое определение чертежа возвращается. Вместо **версии**, `-LatestPublished` переключатель захватывает самые последние опубликованные версии.

Следующий пример `Get-AzBlueprint` использует, чтобы получить все версии определения чертежа под названием "101-чертеж-определение-подписка" из конкретной подписки, представленной как: `{subId}`

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

Вывод примера для определения чертежа с несколькими версиями выглядит следующим образом:

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

Параметры [чертежа](../concepts/parameters.md#blueprint-parameters) по определению чертежа могут быть расширены, с тем чтобы предоставить дополнительную информацию.

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

## <a name="get-blueprint-assignments"></a>Получить план овых заданий

Если назначение чертежа уже существует, вы можете `Get-AzBlueprintAssignment` получить ссылку на него с помощью cmdlet. Cmdlet принимает **SubscriptionId** и **Имя** в качестве дополнительных параметров. Если **SubscriptionId** не указан, используется текущий контекст подписки.

Следующий пример `Get-AzBlueprintAssignment` использует для получения одного назначения чертежа под названием "Назначение-блокировка-ресурс-группы" из конкретной подписки, представленной как: `{subId}`

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

Вывод примера для назначения чертежа выглядит следующим образом:

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

## <a name="create-blueprint-assignments"></a>Создание назначений чертежей

Если назначение чертежа еще не существует, его `New-AzBlueprintAssignment` можно создать с помощью cmdlet. В этом cmdlet используются следующие параметры:

- **Имя** (обязательно)
  - Определяет название назначения чертежа
  - Должен быть уникальным и еще не существует в **SubscriptionId**
- **План (обязательно)**
  - Определяет определение чертежа для присвоения
  - Использование `Get-AzBlueprint` для получения эталонного объекта
- **Расположение** (обязательно)
  - Определяет регион для создания объекта управляемого удостоверения и подписки, назначенного системой в
- **Подписка** (необязательно)
  - Определяет подписку, на которая развертывается назначение
  - Если не предусмотрено, по умолчанию в текущем контексте подписки
- **Блокировка** (необязательно)
  - Определение [блокировки ресурсов для](../concepts/resource-locking.md) использования ресурсов для развернутых ресурсов
  - Поддерживаемые варианты: _Нет,_ _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
  - Если не предусмотрено, _None_ по умолчанию
- **СистемаНазначенаИдентичность** (необязательно)
  - Выберите для создания системного управляемого удостоверения для назначения и развертывания ресурсов
  - По умолчанию для набора параметров "идентификация"
  - Нельзя использовать с **userAssignedIdentity**
- **UserAssignedIdentity** (необязательно)
  - Определение назначенного пользователем управляемого удостоверения для использования для назначения и развертывания ресурсов
  - Часть набора параметров «идентичности»
  - Нельзя использовать с **SystemAssignedIdentity**
- **Параметр** (необязательно)
  - [Таблица хэша](/powershell/module/microsoft.powershell.core/about/about_hash_tables) пар ключей/значений для [настройки динамических параметров](../concepts/parameters.md#dynamic-parameters) в назначении чертежа
  - По умолчанию для динамического параметра является **значение по умолчанию** в определении
  - Если параметр не предоставлен и не имеет **значения по умолчанию,** параметр не является обязательным

    > [!NOTE]
    > **Параметр** не поддерживает безопасные строки.

- **РесурснаягруппаПараметр** (необязательно)
  - [Хэш-таблица](/powershell/module/microsoft.powershell.core/about/about_hash_tables) артефактов группы ресурсов
  - Каждый заполнитель артефакта группы ресурсов имеет пары ключей/значений для динамического настройки **Имя** и **местоположение** на этом артефакте группы ресурсов
  - Если параметр группы ресурсов не предоставлен и не имеет **значения по умолчанию,** параметр группы ресурсов не является обязательным
- **AssignmentFile** (необязательно)
  - Путь к представлению файла JSON назначения плана
  - Этот параметр является частью набора параметров PowerShell, который включает только **имя,** **чертеж**и **SubscriptionId,** а также общие параметры.

### <a name="example-1-provide-parameters"></a>Пример 1: Предоставить параметры

Следующий пример создает новое назначение версии '1.1' определения чертежа `Get-AzBlueprint`'my-blueprint', извлеченного с помощью, устанавливает управляемое местоположение объекта идентификации и назначения на 'westus2', блокирует ресурсы `{subId}`с _AllResourcesReadOnly_, и устанавливает таблицы хэша для **параметра** и **ResourceGroupParameter** на конкретной подписке, представленной как:

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
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

Вывод примера для создания назначения чертежа выглядит следующим образом:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

### <a name="example-2-use-a-json-assignment-definition-file"></a>Пример 2: Используйте файл определения назначения JSON

Следующий пример создает почти то же назначение, что [и пример 1](#example-1-provide-parameters).
Вместо передачи параметров в cmdlet пример показывает использование файла определения назначения JSON и параметра **AssignmentFile.** Кроме того, **свойство исключенных принципов** настроено как часть **блокировок.** Для **исключенных принципов** нет параметра PowerShell, и свойство можно настроить только путем настройки его через файл определения назначения JSON.

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "westus2",
  "properties": {
    "description": "Assignment of the 101-blueprint-definition-subscription",
    "blueprintId": "/subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101-blueprints-definition-subscription",
    "locks": {
      "mode": "AllResourcesReadOnly",
      "excludedPrincipals": [
          "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
          "38833b56-194d-420b-90ce-cff578296714"
      ]
    },
    "parameters": {
      "storageAccount_storageAccountType": {
        "value": "Standard_GRS"
      }
    },
    "resourceGroups": {
      "ResourceGroup": {
        "name": "storage_rg",
        "location": "westus2"
      }
    }
  }
}
```

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -SubscriptionId '{subId}' `
    -AssignmentFile '.\assignment.json'
```

На примере файла определения определения назначения JSON для управляемого итогового удостоверения, назначенного пользователем, см. тело запроса в [примере: Назначение с назначенным пользователем управляемым удостоверением](/rest/api/blueprints/assignments/createorupdate#examples) для REST API.

## <a name="update-blueprint-assignments"></a>Обновление назначений чертежей

Иногда необходимо обновить уже созданное назначение чертежа. Cmdlet `Set-AzBlueprintAssignment` обрабатывает это действие. Cmdlet принимает большинство из тех же `New-AzBlueprintAssignment` параметров, что cmdlet делает, что позволяет все, что было установлено на назначение будет обновляться. Исключение составляют _название,_ _чертеж_и _SubscriptionId_. Обновляются только приведенные значения.

Чтобы понять, что происходит при [rules for updating assignments](./update-existing-assignments.md#rules-for-updating-assignments)обновлении назначения чертежа, см.

- **Имя** (обязательно)
  - Упогоняет название назначения чертежа для обновления
  - Используется для определения местодания для обновления, а не для изменения назначения
- **План (обязательно)**
  - Определяет определение чертежа назначения чертежа
  - Использование `Get-AzBlueprint` для получения эталонного объекта
  - Используется для определения местодания для обновления, а не для изменения назначения
- **Расположение** (необязательно)
  - Определяет регион для создания объекта управляемого удостоверения и подписки, назначенного системой в
- **Подписка** (необязательно)
  - Определяет подписку, на которая развертывается назначение
  - Если не предусмотрено, по умолчанию в текущем контексте подписки
  - Используется для определения местодания для обновления, а не для изменения назначения
- **Блокировка** (необязательно)
  - Определение [блокировки ресурсов для](../concepts/resource-locking.md) использования ресурсов для развернутых ресурсов
  - Поддерживаемые варианты: _Нет,_ _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
- **СистемаНазначенаИдентичность** (необязательно)
  - Выберите для создания системного управляемого удостоверения для назначения и развертывания ресурсов
  - По умолчанию для набора параметров "идентификация"
  - Нельзя использовать с **userAssignedIdentity**
- **UserAssignedIdentity** (необязательно)
  - Определение назначенного пользователем управляемого удостоверения для использования для назначения и развертывания ресурсов
  - Часть набора параметров «идентичности»
  - Нельзя использовать с **SystemAssignedIdentity**
- **Параметр** (необязательно)
  - [Таблица хэша](/powershell/module/microsoft.powershell.core/about/about_hash_tables) пар ключей/значений для [настройки динамических параметров](../concepts/parameters.md#dynamic-parameters) в назначении чертежа
  - По умолчанию для динамического параметра является **значение по умолчанию** в определении
  - Если параметр не предоставлен и не имеет **значения по умолчанию,** параметр не является обязательным

    > [!NOTE]
    > **Параметр** не поддерживает безопасные строки.

- **РесурснаягруппаПараметр** (необязательно)
  - [Хэш-таблица](/powershell/module/microsoft.powershell.core/about/about_hash_tables) артефактов группы ресурсов
  - Каждый заполнитель артефакта группы ресурсов имеет пары ключей/значений для динамического настройки **Имя** и **местоположение** на этом артефакте группы ресурсов
  - Если параметр группы ресурсов не предоставлен и не имеет **значения по умолчанию,** параметр группы ресурсов не является обязательным

Следующий пример обновляет назначение версии '1.1' определения чертежа 'my-blueprint', извлеченного путем `Get-AzBlueprint` изменения режима блокировки:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

Вывод примера для создания назначения чертежа выглядит следующим образом:

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

## <a name="remove-blueprint-assignments"></a>Удаление назначений чертежей

Когда пришло время для назначения чертежа, `Remove-AzBlueprintAssignment` чтобы быть удалены, cmdlet обрабатывает это действие. Cmdlet принимает **имя** или **InputObject,** чтобы указать, какое назначение чертежа удалить. **SubscriptionId** _является обязательным_ и должны быть предоставлены во всех случаях.

Следующий пример приводит существующее `Get-AzBlueprintAssignment` назначение чертежа, а затем удаляет `{subId}`его из конкретной подписки, представленной как:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>Пример сквозного кода

Объединяя все шаги вместе, следующий пример получает определение чертежа, затем создает, обновляет `{subId}`и удаляет назначение чертежа в конкретной подписке, представленной как:

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
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
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

- Узнайте о [жизненном цикле чертежей.](../concepts/lifecycle.md)
- Узнайте, как использовать [статические и динамические параметры](../concepts/parameters.md).
- Научитесь настраивать [последовательность схемы](../concepts/sequencing-order.md).
- Узнайте, как применять [блокировку ресурсов схемы](../concepts/resource-locking.md).
- Устраняйте проблемы, возникающие во время назначения схемы, с помощью [общих инструкций по устранению неполадок](../troubleshoot/general.md).