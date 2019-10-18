---
title: Последовательное шифрование дисков Azure и масштабируемых наборов виртуальных машин Azure
description: В этой статье приведены инструкции по включению шифрования дисков Microsoft Azure для виртуальных машин IaaS под управлением Linux.
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: article
ms.author: mbaldwin
ms.date: 10/10/2019
ms.openlocfilehash: 351c3603ddc069647577b5465ea9fa839810cbc1
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530892"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>Использование шифрования дисков Azure с виртуализацией расширения масштабируемого набора виртуальных машин

Расширения, такие как шифрование дисков Azure, можно добавить в масштабируемый набор виртуальных машин Azure в указанном порядке. Для этого используйте [виртуализацию расширения](virtual-machine-scale-sets-extension-sequencing.md). 

Как правило, шифрование должно применяться к диску.

- После расширений или пользовательских сценариев, подготавливающих диски или тома.
- Перед расширениями или пользовательскими скриптами, которые обращаются к данным на зашифрованных дисках или томах или используют их.

В любом случае свойство `provisionAfterExtensions` указывает, какое расширение следует добавить позже в последовательности.

## <a name="sample-azure-templates"></a>Примеры шаблонов Azure

Если вы хотите, чтобы шифрование дисков Azure применялось после другого расширения, укажите свойство `provisionAfterExtensions` в блоке расширения AzureDiskEncryption. 

Ниже приведен пример с использованием "CustomScriptExtension" — скрипт PowerShell, который инициализирует и форматирует диск Windows, за которым следует "AzureDiskEncryption":

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "CustomScriptExtension",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.9",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "fileUris": [
              "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/ade-vmss/FormatMBRDisk.ps1"
            ]
          },
          "protectedSettings": {
           "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File FormatMBRDisk.ps1"
          }
        }
      },
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "AzureDiskEncryption",
        "location": "[resourceGroup().location]",
        "properties": {
          "provisionAfterExtensions": [
            "CustomScriptExtension"
          ],
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
    ]
  }
}
```

Если вы хотите, чтобы шифрование дисков Azure применялось перед другим расширением, поставьте свойство `provisionAfterExtensions` в блок расширения, чтобы следовать ему.

Ниже приведен пример с использованием "AzureDiskEncryption", за которым следует "VMDiagnosticsSettings", расширение, которое предоставляет возможности мониторинга и диагностики на виртуальной машине Azure под управлением Windows:


```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "AzureDiskEncryption",
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "provisionAfterExtensions": [
            "AzureDiskEncryption"
          ],
        "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
    ]
  }
}
```

Более подробные сведения о шаблоне см. в следующих статьях:
* Примените расширение шифрования дисков Azure после пользовательского скрипта оболочки, который форматирует диск (Linux): [deploy-екстсек-Linux-ADE-After-CustomScript. JSON.](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)


## <a name="next-steps"></a>Дальнейшие действия
- Дополнительные сведения о виртуализации расширений: [Подготовка расширения последовательности в масштабируемых наборах виртуальных машин](virtual-machine-scale-sets-extension-sequencing.md).
- Дополнительные сведения о свойстве `provisionAfterExtensions`: [ссылка на шаблон Microsoft. COMPUTE virtualMachineScaleSets/Extensions](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions).
- [Шифрование дисков Azure для масштабируемых наборов виртуальных машин](disk-encryption-overview.md)
- [Шифрование масштабируемых наборов виртуальных машин с помощью Azure CLI](disk-encryption-cli.md)
- [Шифрование масштабируемых наборов виртуальных машин с помощью Azure PowerShell](disk-encryption-powershell.md)
- [Создание и Настройка хранилища ключей для шифрования дисков Azure](disk-encryption-key-vault.md)
