---
title: Включение расширения виртуальной машины с помощью Azure PowerShell
description: В этой статье описывается, как развернуть расширения виртуальной машины на серверах с поддержкой дуги Azure, работающих в гибридных облачных средах, с помощью Azure PowerShell.
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: 631aa323fee8db712acc975336bdbf9436833240
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92462988"
---
# <a name="enable-azure-vm-extensions-using-azure-powershell"></a>Включение расширений виртуальной машины Azure с помощью Azure PowerShell

В этой статье показано, как развертывать и удалять расширения виртуальной машины Azure, поддерживаемые серверами с поддержкой ARC в Azure, на гибридный компьютер под управлением Linux или Windows с помощью Azure PowerShell.

## <a name="prerequisites"></a>Предварительные требования

- Компьютер с Azure PowerShell. Инструкции см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/).

Прежде чем использовать Azure PowerShell для управления расширениями виртуальной машины на гибридном сервере, управляемом серверами с поддержкой Arc, необходимо установить `Az.ConnectedMachine` модуль. Выполните следующую команду на сервере с поддержкой ARC:

`Install-Module -Name Az.ConnectedMachine`.

После завершения установки возвращается следующее сообщение:

`The installed extension `AZ. Коннектедмачине` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>Включить расширение

Чтобы включить расширение виртуальной машины на сервере с поддержкой Arc, используйте [New-азконнектедмачиникстенсион](/powershell/module/az.connectedmachine/new-azconnectedmachineextension) с `-Name` параметрами, `-ResourceGroupName` , `-MachineName` , `-Location` , `-Publisher` ,- `ExtensionType` и `-Settings` .

В следующем примере показано включение расширения виртуальной машины Log Analytics на сервере Linux с поддержкой ARC:

```powershell
PS C:\> $Setting = @{ "workspaceId" = "workspaceId" }
PS C:\> $protectedSetting = @{ "workspaceKey" = "workspaceKey" }
PS C:\> New-AzConnectedMachine -Name OMSLinuxAgent -ResourceGroupName "myResourceGroup" -MachineName "myMachine" -Location "eastus" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -TypeHandlerVersion "1.10" -Settings $Setting -ProtectedSetting $protectedSetting -ExtensionType OmsAgentforLinux"
```

В следующем примере активируется расширение пользовательского скрипта на сервере с поддержкой ARC:

```powershell
PS C:\> $Setting = @{ "commandToExecute" = "powershell.exe -c Get-Process" }
PS C:\> New-AzConnectedMachine -Name custom -ResourceGroupName myResourceGroup -MachineName myMachineName -Location eastus -Publisher "Microsoft.Compute" -TypeHandlerVersion 1.10 -Settings $Setting -ExtensionType CustomScriptExtension
```

## <a name="list-extensions-installed"></a>Список установленных расширений

Чтобы получить список расширений виртуальной машины на сервере с поддержкой Arc, используйте [Get-азконнектедмачиникстенсион](/powershell/module/az.connectedmachine/get-azconnectedmachineextension) с `-MachineName` `-ResourceGroupName` параметрами и.

Пример

```powershell
Get-AzConnectedMachineExtension -ResourceGroupName myResourceGroup -MachineName myMachineName

Name    Location  PropertiesType        ProvisioningState
----    --------  --------------        -----------------
custom  westus2   CustomScriptExtension Succeeded
```

## <a name="remove-an-installed-extension"></a>Удаление установленного расширения

Чтобы удалить установленное расширение виртуальной машины на сервере с поддержкой Arc, используйте [Remove-азконнектедмачиникстенсион](/powershell/module/az.connectedmachine/remove-azconnectedmachineextension) с `-Name` `-MachineName` `-ResourceGroupName` параметрами и.

Например, чтобы удалить расширение виртуальной машины Log Analytics для Linux, выполните следующую команду:

```powershell
Remove-AzConnectedMachineExtension -MachineName myMachineName -ResourceGroupName myResourceGroup -Name OmsAgentforLinux
```

## <a name="next-steps"></a>Дальнейшие действия

- Расширения виртуальных машин можно развертывать, администрировать и удалять с помощью [Azure CLI](manage-vm-extensions-cli.md), из [портал Azure](manage-vm-extensions-portal.md)или [шаблонов Azure Resource Manager](manage-vm-extensions-template.md).

- Сведения об устранении неполадок можно найти в разделе [руководство по устранению неполадок РАСШИРЕНИЙ ВМ](troubleshoot-vm-extensions.md).
