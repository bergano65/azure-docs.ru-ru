---
title: Пример сценария Azure PowerShell для шифрования виртуальной машины Windows.
description: Пример сценария Azure PowerShell для шифрования виртуальной машины Windows.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ms.openlocfilehash: 25184de89fc3bb504d26b411be04da7ba9bba10d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86509426"
---
# <a name="encrypt-a-windows-virtual-machine-with-azure-powershell"></a>Шифрование виртуальной машины Windows с помощью Azure PowerShell

Этот сценарий создает безопасное Azure Key Vault, ключи шифрования, субъект-службу Azure Active Directory и виртуальную машину Windows. Затем эта виртуальная машина шифруется с помощью ключа шифрования из Key Vault и учетных данных субъекта-службы.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/virtual-machine/encrypt-vm/encrypt-windows-vm.ps1 "Encrypt VM disks")]

## <a name="clean-up-deployment"></a>Очистка развертывания

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Чтобы создать развертывание, скрипт использует следующие команды. Для каждого элемента в таблице приведены ссылки на документацию по команде.

| Get-Help | Примечания |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) | Создает Azure Key Vault для хранения защищенных данных, таких как ключи шифрования. |
| [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) | Создает ключ шифрования в Key Vault. |
| [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal) | Создает субъект-службу Azure Active Directory для безопасной аутентификации и контроля доступа к ключам шифрования. |
| [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | Задает разрешения для Key Vault, предоставляя субъекту-службе доступ к ключам шифрования. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Создает виртуальную машину и подключает ее к сетевой карте, виртуальной сети, подсети и группе безопасности сети. Эта команда также открывает порт 80 и задает учетные данные администратора. |
| [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault) | Получает необходимые сведения о Key Vault. |
| [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) | Включает шифрование на виртуальной машине с помощью учетных данных субъекта-службы и ключа шифрования. |
| [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) | Отображает состояние шифрования виртуальной машины. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов и все ресурсы, содержащиеся в ней. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев PowerShell для виртуальных машин представлены в [документации по виртуальным машинам Azure под управлением Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
