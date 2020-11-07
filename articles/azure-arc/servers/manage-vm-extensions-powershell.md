---
title: Включение расширения виртуальной машины с помощью Azure PowerShell
description: В этой статье описывается, как развернуть расширения виртуальной машины на серверах с поддержкой дуги Azure, работающих в гибридных облачных средах, с помощью Azure PowerShell.
ms.date: 11/06/2020
ms.topic: conceptual
ms.openlocfilehash: 5ed9db23cd19814ff05c2f142f51cea869f2c2d4
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359074"
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
PS C:\> New-AzConnectedMachineExtension -Name OMSLinuxAgent -ResourceGroupName "myResourceGroup" -MachineName "myMachine" -Location "eastus" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -TypeHandlerVersion "1.10" -Settings $Setting -ProtectedSetting $protectedSetting -ExtensionType OmsAgentforLinux"
```

В следующем примере активируется расширение пользовательского скрипта на сервере с поддержкой ARC:

```powershell
PS C:\> $Setting = @{ "commandToExecute" = "powershell.exe -c Get-Process" }
PS C:\> New-AzConnectedMachineExtension -Name custom -ResourceGroupName myResourceGroup -MachineName myMachineName -Location eastus -Publisher "Microsoft.Compute" -TypeHandlerVersion 1.10 -Settings $Setting -ExtensionType CustomScriptExtension
```

### <a name="key-vault-vm-extension-preview"></a>Расширение виртуальной машины Key Vault (Предварительная версия)

> [!WARNING]
> Клиенты PowerShell часто добавляют в `\` `"` settings.js, что приведет к сбою akvvm_service с ошибкой: `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

В следующем примере показано включение расширения виртуальной машины Key Vault (Предварительная версия) на сервере с поддержкой ARC:

```powershell
# Build settings
    $settings = @{
      secretsManagementSettings = @{
       observedCertificates = @{
        "observedCert1"
       }
      certificateStoreLocation = "myMachineName" # For Linux use "/var/lib/waagent/Microsoft.Azure.KeyVault.Store/"
      certificateStore = "myCertificateStoreName"
      pollingIntervalInS = "pollingInterval"
      }
    authenticationLocationSettings = @{
     msiEndpoint = "http://localhost:40342/metadata/identity"
     }
    }

    $resourceGroup = "resourceGroupName"
    $machineName = "myMachineName"
    $location = "regionName"

    # Start the deployment
    New-AzConnectedMachineExtension -ResourceGroupName $resourceGRoup -Location $location -MachineName $machineName -Name "KeyVaultForWindows or KeyVaultforLinux" -Publisher "Microsoft.Azure.KeyVault" -ExtensionType "KeyVaultforWindows or KeyVaultforLinux" -Setting (ConvertTo-Json $settings)
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

## <a name="next-steps"></a>Дальнейшие шаги

- Расширения виртуальных машин можно развертывать, администрировать и удалять с помощью [Azure CLI](manage-vm-extensions-cli.md), из [портал Azure](manage-vm-extensions-portal.md)или [шаблонов Azure Resource Manager](manage-vm-extensions-template.md).

- Сведения об устранении неполадок можно найти в разделе [руководство по устранению неполадок РАСШИРЕНИЙ ВМ](troubleshoot-vm-extensions.md).
