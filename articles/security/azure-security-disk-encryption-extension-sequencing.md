---
title: Azure и шифрование дисков Azure масштабируемых наборов виртуальных машин виртуализации расширения см.
description: В этой статье приведены инструкции по включению шифрования дисков Microsoft Azure для виртуальных машин IaaS под управлением Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/21/2019
ms.openlocfilehash: e98e501806971f3cf1bec29960ad15ef9c0024fc
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498151"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>Шифрование дисков Azure используйте масштабирования виртуальных машин задайте виртуализации расширения см.

Расширения, такие как шифрование дисков Azure могут добавляться для масштабирования виртуальных машин Azure в указанном порядке. Чтобы сделать это, используйте [расширения виртуализации](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md). 

В общем случае шифрования должен применяться на диске:

- После расширения или пользовательские сценарии, которые подготавливают диски или тома.
- Прежде чем расширения или пользовательские сценарии, обращающихся к или работы с данными на зашифрованных дисков или томов.

В любом случае `provisionAfterExtensions` свойство определяет, какие расширения должны добавляться по порядку.

## <a name="sample-azure-templates"></a>Пример шаблонов Azure

Если вы хотите добавить шифрование дисков Azure применяется после другого расширения, помещать `provisionAfterExtensions` свойство в блоке AzureDiskEncryption расширения. 

Ниже приведен пример использования «CustomScriptExtension», сценарий Powershell, который инициализирует и форматирует диск Windows, а затем «AzureDiskEncryption»:

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

Если вы хотите иметь шифрования дисков Azure применяется перед другого расширения, помещать `provisionAfterExtensions` свойство в блоке расширения для выполнения.

Ниже приведен пример использования «AzureDiskEncryption», за которым следует «VMDiagnosticsSettings», расширение, которое обеспечивает мониторинг и возможности диагностики на виртуальной Машине Azure под управлением Windows:


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

Дополнительные подробные шаблоны см. в разделе:
* Примените расширение шифрования дисков Azure после пользовательский сценарий, который форматирует диск (Linux): [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)
* Примените расширение шифрования дисков Azure после пользовательский сценарий Powershell, который инициализирует и форматирование диска (Windows): [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-windows-ADE-after-customscript.json)
* Примените расширение шифрования дисков Azure перед пользовательский сценарий Powershell, который инициализирует и форматирование диска (Windows): [deploy-extseq-windows-CustomScript-after-ADE.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-windows-CustomScript-after-ADE.json)

## <a name="next-steps"></a>Дальнейшие действия
- Дополнительные сведения о виртуализации расширений: [Последовательность подготовки расширения в масштабируемых наборах виртуальных машин](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md).
- Дополнительные сведения о `provisionAfterExtensions` свойство: [Справочник по шаблонам virtualMachineScaleSets/расширения Microsoft.Compute](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions).
