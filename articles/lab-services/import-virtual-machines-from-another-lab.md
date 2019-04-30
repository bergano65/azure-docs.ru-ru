---
title: Импорт виртуальных машин из другой лабораторию в Azure DevTest Labs | Документация Майкрософт
description: Узнайте, как импортировать виртуальные машины из другой лаборатории в текущей лаборатории.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: ca6ed58cfabb5027830828812c4820c1b586875c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61322882"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Импорт виртуальных машин из другой лабораторию в Azure DevTest Labs
В этой статье сведения о том, как импортировать виртуальные машины из другой лаборатории в лаборатории.

## <a name="scenarios"></a>Сценарии
Ниже приведены некоторые сценарии, где необходимо импортировать виртуальные машины из одной лаборатории в другой лаборатории.

- Сотрудник группы перемещается в другую группу на предприятии и хочет воспользоваться рабочего стола разработчика для новой группы DevTest Labs.
- Группе достиг [квоту уровня подписки](../azure-subscription-service-limits.md) и хочет, чтобы разделить команды в несколько подписок
- Компания переходит Express Route (или некоторые другие новые топологией сети) и членам команды нужно перемещать виртуальные машины для использования этой новой инфраструктуры

## <a name="solution-and-constraints"></a>Решение и ограничения
Эта функция позволяет импортировать виртуальные машины в одной лаборатории (источник) в другой лабораторной (назначение). Можно присвоить новое имя для целевой виртуальной Машины в процессе. Процесс импорта включает в себя все зависимости, такие как диски, расписания, параметры сети и т. д.

Процесс занять некоторое время и зависит от следующих факторов:

- Количество и размеры создаваемых дисков, подключенных на исходный компьютер (так как это операция копирования и не операция перемещения)
- Расстояние в место назначения (например, восточная часть США регион для Юго-Восточная Азия).

По завершении процесса исходной виртуальной машины остается завершение работы и новые, котором запущены в целевой лабораторной среде.

Существует два ключевых ограничений, которые необходимо учитывать при планировании для импорта виртуальных машин из одной лаборатории в другую лабораторию.

- Импорт виртуальной машины подписки и в разных регионах поддерживаются, но подписки должны быть связаны с тем же клиентом Azure Active Directory.
- Виртуальные машины не должен быть в состоянии запрашиваемой в лаборатории источника.
- Вы являетесь владельцем виртуальной машины в лаборатории источника и владелец лаборатории в лаборатории назначения.
- В настоящее время эта функция поддерживается только через Powershell и REST API.

## <a name="use-powershell"></a>Использование PowerShell
Загрузите файл ImportVirtualMachines.ps1 с [GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines). Скрипт можно использовать для импорта одной виртуальной Машины или всех виртуальных машин в лаборатории источника в целевой лабораторной среде.

### <a name="use-powershell-to-import-a-single-vm"></a>Для импорта одной виртуальной Машины с помощью PowerShell
Этот сценарий powershell для выполнения требуется определение исходной виртуальной Машины и назначения лаборатории и при необходимости указав новое имя для использования на целевом компьютере:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>Чтобы импортировать все виртуальные машины в лаборатории источника с помощью PowerShell
Если исходная виртуальная машина не указан, сценарий автоматически импортирует все виртуальные машины в DevTest Labs.  Пример.

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>Импорт виртуальной Машины с помощью HTTP REST
Вызов REST прост. Вы предоставляете достаточно информации для идентификации ресурсов, источника и назначения. Помните, что операция выполняется в лаборатории на целевой ресурс.

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь со следующими статьями:

- [Управление базовыми политиками лаборатории в Azure DevTest Labs](devtest-lab-get-started-with-lab-policies.md)
- [Часто задаваемые вопросы](devtest-lab-faq.md)
