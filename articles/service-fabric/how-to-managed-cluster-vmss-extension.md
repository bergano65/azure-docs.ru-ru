---
title: Добавление расширения масштабируемого набора виртуальных машин в Service Fabric тип узла управляемого кластера (Предварительная версия)
description: Вот как можно добавить расширение масштабируемого набора виртуальных машин Service Fabric тип узла управляемого кластера.
ms.topic: article
ms.date: 09/28/2020
ms.openlocfilehash: 64df4b82795f382e176d66dc61470296447b9e29
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788084"
---
# <a name="add-a-virtual-machine-scale-set-extension-to-a-service-fabric-managed-cluster-node-type-preview"></a>Добавление расширения масштабируемого набора виртуальных машин в Service Fabric тип узла управляемого кластера (Предварительная версия)

Каждый тип узла в управляемом кластере Service Fabric поддерживается масштабируемым набором виртуальных машин. Это позволяет добавлять [расширения масштабируемых наборов виртуальных машин](../virtual-machines/extensions/overview.md) в типы управляемых узлов кластера Service Fabric.

Расширение масштабируемого набора виртуальных машин можно добавить в тип узла с помощью команды PowerShell [Add-азсервицефабрикманажеднодетипевмекстенсион](/powershell/module/az.servicefabric/add-azservicefabricmanagednodetypevmextension) .

Кроме того, можно создать расширение масштабируемого набора виртуальных машин на Service Fabric тип узла управляемого кластера в шаблоне Azure Resource Manager, например:

```json
{
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "apiVersion": "[variables('sfApiVersion')]",
    "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
    "dependsOn": [
        "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
    ],
    "location": "[resourceGroup().location]",
    "properties": {
        "isPrimary": true,
        "vmInstanceCount": 3,
        "dataDiskSizeGB": 100,
        "vmSize": "Standard_D2",
        "vmImagePublisher": "MicrosoftWindowsServer",
        "vmImageOffer": "WindowsServer",
        "vmImageSku": "2019-Datacenter",
        "vmImageVersion": "latest",
        "vmExtensions": [{
            "name": "ExtensionA",
            "properties": {
                "publisher": "ExtensionA.Publisher",
                "type": "KeyVaultForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                }
            }
        }]
    }
}
```

Дополнительные сведения о настройке типов управляемых узлов кластера Service Fabric см. в разделе [тип узла управляемого кластера](/azure/templates/microsoft.servicefabric/2020-01-01-preview/managedclusters/nodetypes).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об управляемых кластерах Service Fabric см. в статье:

> [!div class="nextstepaction"]
> [Распространенные вопросы об управляемых кластерах Service Fabric](./faq-managed-cluster.md)
