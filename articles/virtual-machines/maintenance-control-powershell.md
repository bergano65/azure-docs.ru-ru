---
title: Управление обслуживанием виртуальных машин Azure с использованием PowerShell
description: Узнайте, как управлять, когда техническое обслуживание применяется к вашим VMs-вам Azure, используя управление обслуживанием и PowerShell.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: dc47afe9cb6eca1b10f8caca7b85087023c5eadf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060136"
---
# <a name="preview-control-updates-with-maintenance-control-and-azure-powershell"></a>Предварительный просмотр: Управление обновлениями с помощью управления обслуживанием и Azure PowerShell

Управление обновлениями платформы, которые не требуют перезагрузки, с помощью управления обслуживанием. Azure часто обновляет свою инфраструктуру для повышения надежности, производительности, безопасности или запуска новых функций. Большинство обновлений являются прозрачными для пользователей. Некоторые чувствительные рабочие нагрузки, такие как игры, потоковое воспроизведение мультимедиа и финансовые операции, не могут допустить даже нескольких секунд замораживания или отключения VM для обслуживания. Управление обслуживанием дает вам возможность ждать обновления платформы и применять их в течение 35-дневного прокатного окна. 

Управление обслуживанием позволяет решать, когда следует применять обновления для изолированных ВМ.

С помощью управления обслуживанием, вы можете:
- Обновления пакета в один пакет обновлений.
- Подождите до 35 дней, чтобы применить обновления. 
- Автоматируйте обновления платформы для окна обслуживания с помощью функций Azure.
- Конфигурации обслуживания работают между подписками и группами ресурсов. 

> [!IMPORTANT]
> Управление обслуживанием в настоящее время находится в общедоступном предварительном просмотре.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 

## <a name="limitations"></a>Ограничения

- VMs должны быть на [выделенном хосте](./linux/dedicated-hosts.md)или быть созданы с использованием [изолированного размера VM.](./linux/isolation.md)
- Через 35 дней обновление будет автоматически применено.
- Пользователь должен иметь доступ **к ресурсу.**


## <a name="enable-the-powershell-module"></a>Включить модуль PowerShell

Убедитесь, что `PowerShellGet` в актуальном состоянии.

```azurepowershell-interactive
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```

Смлеты Az.Maintenance PowerShell находятся в предварительном просмотре, поэтому необходимо установить модуль с `AllowPrerelease` параметром в Облачной оболочке или локальной установке PowerShell.   

```azurepowershell-interactive
Install-Module -Name Az.Maintenance -AllowPrerelease
```

Если вы устанавливаете локально, убедитесь, что вы открываете запрос PowerShell в качестве администратора.

Вас также могут попросить подтвердить, что вы хотите установить из *ненадежного репозитория.* Для `Y` установки модуля введите или выберите **«Да для всех».**



## <a name="create-a-maintenance-configuration"></a>Создание конфигурации обслуживания

Создайте группу ресурсов в качестве контейнера для конфигурации. В этом примере в *Eastus*создается ресурсная группа под названием *myMaintenanceRG.* Если у вас уже есть группа ресурсов, которую вы хотите использовать, вы можете пропустить эту часть и заменить имя группы ресурсов на свой собственный в остальных примерах.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

Используйте [New-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/new-azmaintenanceconfiguration) для создания конфигурации обслуживания. Этот пример создает конфигурацию обслуживания под названием *myConfig,* прицелваемую для узла. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

Использование `-MaintenanceScope host` гарантирует, что конфигурация обслуживания используется для управления обновлениями для узла.

Если вы попытаетесь создать конфигурацию с тем же именем, но в другом месте, вы получите ошибку. Имена конфигураций должны быть уникальными для вашей подписки.

Вы можете запросить доступные конфигурации обслуживания с помощью [Get-AzMaintenanceConfiguration.](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceconfiguration)

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="assign-the-configuration"></a>Назначить конфигурацию

Используйте [New-AzConfigurationAssignment,](https://docs.microsoft.com/powershell/module/az.maintenance/new-azconfigurationassignment) чтобы назначить конфигурацию изолированной VM или Azure Dedicated Host.

### <a name="isolated-vm"></a>Изолированный VM

Примените конфигурацию к VM, используя идентификатор конфигурации. Укажите `-ResourceType VirtualMachines` и предоставим название `-ResourceName`VM для , и ресурсная группа VM для `-ResourceGroupName`. 

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

### <a name="dedicated-host"></a>Выделенный узел

Чтобы применить конфигурацию к выделенной хосте, необходимо также `-ResourceType hosts`включить, `-ResourceParentType hostGroups` `-ResourceParentName` с именем хоста и. 


```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

## <a name="check-for-pending-updates"></a>Проверка ожидающих обновления

Используйте [Get-AzMaintenanceUpdate,](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceupdate) чтобы узнать, есть ли ожидающие обновления. Используйте `-subscription` для указания подписки Azure VM, если она отличается от той, в которую вы вошли.

Если нет обновлений для отображаемого, эта команда ничего не вернет. В противном случае он вернет объект PSApplyUpdate:

```json
{
   "maintenanceScope": "Host",
   "impactType": "Freeze",
   "status": "Pending",
   "impactDurationInSec": 9,
   "notBefore": "2020-02-21T16:47:44.8728029Z",
   "properties": {
      "resourceId": "/subscriptions/39c6cced-4d6c-4dd5-af86-57499cd3f846/resourcegroups/Ignite2019/providers/Microsoft.Compute/virtualMachines/MCDemo3"
} 
```

### <a name="isolated-vm"></a>Изолированный VM

Проверьте ожидающие обновления для изолированного VM. В этом примере вывод отформатирован как таблица для читаемости.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```


### <a name="dedicated-host"></a>Выделенный узел

Проверка ожидающих обновления для выделенного узла. В этом примере вывод отформатирован как таблица для читаемости. Замените значения для ресурсов своими собственными.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute | Format-Table
```


## <a name="apply-updates"></a>Применение обновлений

Используйте [New-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) для подачи заявки на ожидающие обновления.

### <a name="isolated-vm"></a>Изолированный VM

Создайте запрос на применение обновлений к изолированной VM.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

При успехе эта команда `PSApplyUpdate` вернет объект. Атрибут «Имя» можно `Get-AzApplyUpdate` использовать для проверки состояния обновления. Смотрите [статус обновления проверьте](#check-update-status).

### <a name="dedicated-host"></a>Выделенный узел

Применяйте обновления к выделенной хосте.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute
```

## <a name="check-update-status"></a>Проверка состояния обновления
Используйте [Get-AzApplyUpdate,](https://docs.microsoft.com/powershell/module/az.maintenance/get-azapplyupdate) чтобы проверить состояние обновления. Команды, показанные ниже, показывают состояние последнего обновления, используя `default` для `-ApplyUpdateName` параметра. Вы можете заменить название обновления (возвращенное командой [New-AzApplyUpdate),](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) чтобы получить статус конкретного обновления.

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime будет время, когда обновление получило завершение, либо инициировано вами или платформой в случае, если окно самообслуживания не было использовано. Если никогда не было обновления, применяемого с помощью управления обслуживанием, оно покажет значение по умолчанию.

### <a name="isolated-vm"></a>Изолированный VM

Проверьте наличие обновлений в конкретной виртуальной машине.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName default
```

### <a name="dedicated-host"></a>Выделенный узел

Проверьте наличие обновлений для выделенного хоста.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName myUpdateName
```

## <a name="remove-a-maintenance-configuration"></a>Удалить конфигурацию обслуживания

Используйте [Remove-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) для удаления конфигурации обслуживания.

```azurepowershell-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>Дальнейшие действия
Чтобы узнать больше, смотрите [техническое обслуживание и обновления](maintenance-and-updates.md).
