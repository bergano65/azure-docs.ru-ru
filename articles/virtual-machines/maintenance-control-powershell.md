---
title: Управление обслуживанием для виртуальных машин Azure с помощью PowerShell
description: Узнайте, как управлять применением обслуживания к виртуальным машинам Azure с помощью управления обслуживанием и PowerShell.
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: e7a5f9ba865ab555bde3125f40ee8675709bef40
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932714"
---
# <a name="preview-control-updates-with-maintenance-control-and-azure-powershell"></a>Предварительный просмотр: Управление обновлениями с помощью управления обслуживанием и Azure PowerShell

Управление обновлениями платформы, которые не нуждаются в перезагрузке, с помощью управления обслуживанием. Azure часто обновляет свою инфраструктуру для повышения надежности, производительности, безопасности или запуска новых функций. Большинство обновлений прозрачны для пользователей. Некоторые конфиденциальные рабочие нагрузки, например игры, потоковая передача мультимедиа и финансовые транзакции, не допускают даже несколько секунд заморозки или отключения виртуальной машины для обслуживания. Управление обслуживанием дает возможность ожидать обновления платформы и применять их в течение интервала в течение 35 дней. 

Управление обслуживанием позволяет решить, когда следует применять обновления к изолированным виртуальным машинам.

С помощью управления обслуживанием можно:
- Пакетных обновлений в одном пакете обновления.
- Подождите 35 дней, чтобы применить обновления. 
- Автоматизируйте обновление платформы для окна обслуживания с помощью функций Azure.
- Конфигурации обслуживания работают в подписках и группах ресурсов. 

> [!IMPORTANT]
> Управление обслуживанием в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 

## <a name="limitations"></a>Ограничения

- Виртуальные машины должны находиться на [выделенном узле](./linux/dedicated-hosts.md)или быть созданы с использованием [ИЗОЛИРОВАННОГО размера виртуальной машины](./linux/isolation.md).
- Через 35 дней обновление будет автоматически применено, а ограничения доступности не будут соблюдаться.
- Пользователь должен иметь доступ **владельца ресурса** .


## <a name="enable-the-powershell-module"></a>Включение модуля PowerShell

Убедитесь, что `PowerShellGet` не устарела.

```azurepowershell-interactive
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```

Командлеты AZ. Maintenance PowerShell доступны в предварительной версии, поэтому необходимо установить модуль с параметром `AllowPrerelease` в Cloud Shell или локальной установке PowerShell.   

```azurepowershell-interactive
Install-Module -Name Az.Maintenance -AllowPrerelease
```

Если выполняется локальная установка, убедитесь, что вы откроете командную строку PowerShell с правами администратора.

Вам также может быть предложено подтвердить установку из *ненадежного репозитория*. Введите `Y` или выберите **Да для всех** , чтобы установить модуль.



## <a name="create-a-maintenance-configuration"></a>Создание конфигурации обслуживания

Создайте группу ресурсов в качестве контейнера для вашей конфигурации. В этом примере в *eastus*создается группа ресурсов с именем *мимаинтенанцерг* . Если у вас уже есть группа ресурсов, которую вы хотите использовать, можно пропустить эту часть и заменить имя группы ресурсов своим владельцем в остальных примерах.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

Используйте [New-азмаинтенанцеконфигуратион](https://docs.microsoft.com/powershell/module/az.maintenance/new-azmaintenanceconfiguration) для создания конфигурации обслуживания. В этом примере создается конфигурация обслуживания с именем *myConfig* , областью действия которой является узел. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

Использование `-MaintenanceScope host` гарантирует, что конфигурация обслуживания будет использоваться для управления обновлениями узла.

При попытке создать конфигурацию с тем же именем, но в другом расположении возникнет ошибка. Имена конфигураций должны быть уникальными для вашей подписки.

Вы можете запросить доступные конфигурации обслуживания с помощью команды [Get-азмаинтенанцеконфигуратион](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceconfiguration).

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="assign-the-configuration"></a>Назначение конфигурации

Используйте [New-азконфигуратионассигнмент](https://docs.microsoft.com/powershell/module/az.maintenance/new-azconfigurationassignment) , чтобы назначить конфигурацию изолированной виртуальной машине или выделенному узлу Azure.

### <a name="isolated-vm"></a>Изолированная виртуальная машина

Примените конфигурацию к виртуальной машине, используя идентификатор конфигурации. Укажите `-ResourceType VirtualMachines` и укажите имя виртуальной машины для `-ResourceName`и группу ресурсов виртуальной машины для `-ResourceGroupName`. 

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

### <a name="dedicate-host"></a>Выделить узел

Чтобы применить конфигурацию к выделенному узлу, необходимо также включить `-ResourceType hosts`, `-ResourceParentName` с именем группы узлов и `-ResourceParentType hostGroups`. 


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

## <a name="check-for-pending-updates"></a>Проверить наличие ожидающих обновлений

Чтобы узнать, есть ли ожидающие обновления, используйте [Get-азмаинтенанцеупдате](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceupdate) . Используйте `-subscription`, чтобы указать подписку Azure виртуальной машины, если она отличается от той, с которой вы выполнили вход. 

### <a name="isolated-vm"></a>Изолированная виртуальная машина

Проверьте наличие ожидающих обновлений для изолированной виртуальной машины. В этом примере выходные данные форматируются в виде таблицы для удобства чтения.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```

### <a name="dedicated-host"></a>Выделенный узел

Проверка наличия ожидающих обновлений для выделенного узла. В этом примере выходные данные форматируются в виде таблицы для удобства чтения. Замените значения для ресурсов собственными.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute | Format-Table
```

## <a name="apply-updates"></a>Установка обновлений

Используйте [New-азапплюпдате](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) для применения ожидающих обновлений.

### <a name="isolated-vm"></a>Изолированная виртуальная машина

Создайте запрос на применение обновлений к изолированной виртуальной машине.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

### <a name="dedicated-host"></a>Выделенный узел

Применить обновления к выделенному узлу.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute
```

## <a name="remove-a-maintenance-configuration"></a>Удаление конфигурации обслуживания

Удалите конфигурацию обслуживания с помощью [Remove-азмаинтенанцеконфигуратион](https://docs.microsoft.com/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) .

```azurecli-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения см. в разделе [обслуживание и обновления](maintenance-and-updates.md).
