---
title: Импорт виртуальных машин из другой лаборатории в Azure DevTest Labs | Документация Майкрософт
description: Сведения об импорте виртуальных машин из другой лаборатории в текущую в Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: spelluru
ms.openlocfilehash: cb4a3ec9be82957b4c0366ec232f1147c52d0251
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125905"
---
# <a name="import-vms-from-another-lab-in-azure-devtest-labs"></a>Импорт виртуальной машины из другой лаборатории в Azure DevTest Labs
Служба Azure DevTest Labs значительно улучшает управление виртуальными машинами для операций разработки и тестирования. Она позволяет перемещать виртуальную машину из одной лаборатории в другую в случае изменения потребностей команды или требований к инфраструктуре. Ниже приведены несколько распространенных сценариев импорта:

- Участник команды переходит в другую группу на предприятии и хочет перенести виртуальные машины разработки в лабораторию новой команды.
- Группа достигла предела квоты на уровне подписки и хочет разделить команды на несколько подписок.
- Компания планирует перейти на использование ExpressRoute (или другой новой топологии сети), и команда хочет переместить виртуальные машины для использования новой инфраструктуры.

## <a name="solution-and-constraints"></a>Решение и ограничения
Azure DevTest Labs позволяет владельцу лаборатории импортировать виртуальные машины из исходной лаборатории в лабораторию назначения. При необходимости в процессе импорта владелец лаборатории может предоставить новое имя для целевой виртуальной машины. Во время импорта переносятся все зависимости, например диски, расписания, параметры сети и т. д. Процесс занимает некоторое время. На него влияет количество и размер дисков, подключенных к исходному компьютеру, а также расстояние до места назначения (например, из региона "Восточная часть США" в регион "Юго-Восточная Азия"). После выполнения процесса импорта исходная виртуальная машина останется выключенной, а в лаборатории назначения будет запущена новая.

При планировании импорта виртуальных машин в другую лабораторию необходимо учитывать два основных ограничения:

- Импорт виртуальных машин между подписками и регионами поддерживается, однако подписки должны быть связаны с одним клиентом Azure Active Directory.
- Виртуальные машины в исходной лаборатории не должны находиться в запрашиваемом состоянии.

Кроме того, чтобы иметь возможность импортировать виртуальную машину из одной лаборатории в другую, вы должны быть владельцем виртуальной машины в исходной лаборатории и владельцем лаборатории назначения.

## <a name="steps-to-import-a-vm-from-another-lab"></a>Действия для импорта виртуальной машины из лаборатории
В настоящее время вы можете импортировать виртуальную машину из одной лаборатории в другую только с помощью Azure PowerShell и REST API.

### <a name="use-powershell"></a>Использование PowerShell
Загрузите файл сценария PowerShell ImportVirtualMachines.ps1 из [репозитория Git Azure DevTest Lab](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines) на локальный диск.

#### <a name="import-a-single-vm"></a>Импорт одной виртуальной машины
Выполните сценарий ImportVirtualMachines.ps1, чтобы импортировать одну виртуальную машину из исходной лаборатории в лабораторию назначения. Вы можете указать новое имя для копируемой виртуальной машины с помощью параметра DestinationVirtualMachineName.

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source VM>" `
                            -SourceDevTestLabName "<Name of the lab that contains the source VM>" `
                            -SourceVirtualMachineName "<Name of the machine. Optional. If not specified, all VMs are copied>" `
                            -DestinationSubscriptionId "<ID of the target/destination subscription>" `
                            -DestinationDevTestLabName "<Name of the lab to which the VM is copied>" `
                            -DestinationVirtualMachineName "<New name for the VM. Optional>"
```


#### <a name="importing-all-vms"></a>Импорт всех виртуальных машин
При запуске сценария ImportVirtualMachines.ps1, если вы не укажете виртуальную машину в исходной лаборатории, сценарий импортирует все виртуальные машины из исходной лаборатории в лабораторию назначения.

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source VM>" `
                            -SourceDevTestLabName "<Name of the lab that contains the source VM>" `
                            -DestinationSubscriptionId "<ID of the target/destination subscription>" `
                            -DestinationDevTestLabName "<Name of the lab to which the VMs are copied>"
```

### <a name="use-rest-api"></a>Использование REST API
Вызовите REST API для целевой лаборатории (лаборатории назначения) и передайте в нее в качестве параметров исходную лабораторию, подписку и сведения о виртуальной машине, как показано в следующем примере:

```json
POST https://management.azure.com/subscriptions/<ID of the target/destination subscription>/resourceGroups/<Name of the resource group that contains the destination lab>/providers/Microsoft.DevTestLab/labs/<Name of the lab to which the VMs are copied>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<ID of the subscription that contains the source VM>/resourcegroups/<Name of the resource group that contains the source lab>/providers/microsoft.devtestlab/labs/<Name of the lab that contains the source VM>/virtualmachines/MyVm",
   destinationVirtualMachineName: "MyVmNew"
}
```

## <a name="next-steps"></a>Дальнейшие действия

- Сведения об изменении размера виртуальной машины см. в [этой статье](devtest-lab-resize-vm.md).
- Сведения о повторном развертывании виртуальной машины см. в [этой статье](devtest-lab-redeploy-vm.md).
