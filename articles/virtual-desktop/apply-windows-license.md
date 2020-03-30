---
title: Применить лицензию Windows для виртуальных компьютеров-
description: Описывает, как применять лицензию Windows для виртуальных настольных компьютеров Windows.
services: virtual-desktop
author: ChristianMontoya
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/14/2019
ms.author: chrimo
ms.openlocfilehash: 2543dd12e8a75a038a1fc04371b8c562ef696e25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254239"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>Применить лицензию Windows для виртуальных компьютеров- испотечных хостов

Клиенты, которые имеют надлежащую лицензию на запуск рабочих нагрузок Windows Virtual Desktop, имеют право применить лицензию Windows к виртуальным компьютерам- изапустить их без оплаты другой лицензии. Для получения дополнительной информации, см [Windows Виртуальный настольный цены](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="ways-to-use-your-windows-virtual-desktop-license"></a>Способы использования лицензии Windows Virtual Desktop
Лицензирование Windows Virtual Desktop позволяет применять лицензию к любой виртуальной машине Windows или Windows Server, которая зарегистрирована в качестве хоста в пуле хостов и получает пользовательские соединения. Эта лицензия не распространяется на виртуальные машины, которые работают как серверы обмена файлами, контроллеры доменов и так далее.

Существует несколько способов использования лицензии Windows Virtual Desktop:
- Вы можете создать пул хоста и его виртуальные машины, хост сеанса, используя [предложение Azure Marketplace.](./create-host-pools-azure-marketplace.md) Виртуальные машины, созданные таким образом, автоматически применяют лицензию.
- Вы можете создать пул хоста и его виртуальные машины, хост сеанса, используя [шаблон GitHub Azure Resource Manager.](./create-host-pools-arm-template.md) Виртуальные машины, созданные таким образом, автоматически применяют лицензию.
- Вы можете применить лицензию к существующей виртуальной машине хоста сеанса. Для этого сначала следуйте инструкциям в [создании пула хоста с PowerShell](./create-host-pools-powershell.md) для создания пула хостов и связанных с ними ВМ, а затем вернитесь к этой статье, чтобы узнать, как применить лицензию.

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>Примените лицензию Windows к хосте сеанса VM
Убедитесь, что у вас [установлена и настроена последняя версия Azure PowerShell](/powershell/azure/overview). Выполнить следующий Смдлет PowerShell, чтобы применить лицензию Windows:

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>Проверка вашего хостатора сеанса VM использует лицензионное пособие
После развертывания VM запустите этот cmdlet ot, проверить тип лицензии:
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

Ведущий сеанса VM с приложенной лицензией Windows покажет вам что-то вроде этого:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

VMs без прикладной лицензии Windows покажет вам что-то вроде этого:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

Запустите следующий cmdlet, чтобы увидеть список всех vMs-миноносов, в которых есть лицензия Windows, примененная в подписке Azure:

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```
