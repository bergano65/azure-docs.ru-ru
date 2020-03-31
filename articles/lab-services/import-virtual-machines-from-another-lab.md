---
title: Импорт виртуальных машин из другой лаборатории в Azure DevTest Labs
description: В этой статье описывается, как импортировать виртуальные машины из другой лаборатории в текущую лабораторию в Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 299d5c8758a13edded63b99abb2f12ddf9fa14be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759522"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Импорт виртуальных машин из другой лаборатории в Azure DevTest Labs
В этой статье содержится информация о том, как импортировать виртуальные машины из другой лаборатории в вашу лабораторию.

## <a name="scenarios"></a>Сценарии
Вот несколько сценариев, где необходимо импортировать ввосажки из одной лаборатории в другую лабораторию:

- Человек в команде переходит в другую группу внутри предприятия и хочет взять рабочий стол разработчика в DevTest Labs новой команды.
- Группа попала в [квоту уровня подписки](../azure-resource-manager/management/azure-subscription-service-limits.md) и хочет разделить команды на несколько подписок
- Компания переходит к Express Route (или некоторые другие новые топологии сетей), и команда хочет переместить виртуальные машины, чтобы использовать эту новую инфраструктуру

## <a name="solution-and-constraints"></a>Решение и ограничения
Эта функция позволяет импортировать ввоз в одну лабораторию (источник) в другую лабораторию (направление). Вы можете дополнительно дать новое название для назначения VM в этом процессе. Процесс импорта включает в себя все зависимости, такие как диски, графики, настройки сети и так далее.

Процесс занимает некоторое время и влияет на следующие факторы:

- Количество/размер дисков, которые прикрепляются к исходной машине (так как это операция копирования, а не операция перемещения)
- Расстояние до места назначения (например, регион Восточной части США до юго-восточной Азии).

Как только процесс завершен, источник Virtual Machine остается выключенным, а новый работает в лаборатории назначения.

Есть два ключевых ограничения, о которым следует знать при планировании импорта ввоза ввоза в одну лабораторию в другую лабораторию:

- Импорт виртуальной машины в разных областях и в разных регионах поддерживается, но подписки должны быть связаны с тем же арендатором Active Directory Azure.
- Виртуальные машины не должны находиться в состоянии претензий в исходной лаборатории.
- Вы являетесь владельцем VM в исходной лаборатории и владельцем лаборатории в лаборатории назначения.
- В настоящее время эта функция поддерживается только через Powershell и REST API.

## <a name="use-powershell"></a>Использование PowerShell
Скачать файл ImportVirtualMachines.ps1 с [GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines). Скрипт можно использовать для импорта одного VM или всех VM в исходной лаборатории в лабораторию назначения.

### <a name="use-powershell-to-import-a-single-vm"></a>Используйте PowerShell для импорта одного VM
Выполнение этого скрипта powershell требует идентификации источника VM и лаборатории назначения, а также дополнительного предложения нового имени для использования для машины назначения:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>Используйте PowerShell для импорта всех VMs в исходной лаборатории
Если «Виртуальная машина источника» не указана, скрипт автоматически импортирует все виртуальные машины в DevTest Labs.  Пример:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>Используйте HTTP REST для импорта VM
Вызов REST прост. Вы даете достаточно информации, чтобы определить источник и ресурсы назначения. Помните, что операция происходит на ресурсе лаборатории назначения.

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>Дальнейшие действия
См. следующие статьи:

- [Управление базовыми политиками лаборатории в Azure DevTest Labs](devtest-lab-get-started-with-lab-policies.md)
- [Часто задаваемые вопросы](devtest-lab-faq.md)
