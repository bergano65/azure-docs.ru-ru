---
title: Типы узлов Azure Service Fabric и масштабируемые наборы виртуальных машин | Документация Майкрософт
description: Узнайте, как типы узлов Azure Service Fabric связаны с масштабируемыми наборами виртуальных машин и как удаленно подключаться к экземплярам масштабируемых наборов или узлам кластеров.
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: pepogors
ms.openlocfilehash: cec134f9e71f86cd0ed17912f1a3c76adc9a4164
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72167324"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Типы узлов Azure Service Fabric и масштабируемые наборы виртуальных машин
[Масштабируемые наборы виртуальных машин](/azure/virtual-machine-scale-sets) являются вычислительными ресурсами Azure. Их можно использовать для развертывания коллекций виртуальных машин и управления ими в качестве набора. Каждый тип узла, который вы определяете в Azure Service Fabric, настраивает отдельный масштабируемый набор.  Среда выполнения Service Fabric, установленная на каждой виртуальной машине в масштабируемом наборе с помощью расширения виртуальной машины Microsoft. Azure. ServiceFabric. Все типы узлов можно масштабировать независимо друг от друга, изменять номер SKU операционной системы, работающей на узле кластера, открывать разные наборы портов и использовать различные метрики производительности.

На следующем рисунке показан кластер с двумя типами узлов, которые называются FrontEnd и BackEnd. Каждый тип узла имеет пять узлов.

![Кластер с двумя типами узлов][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Сопоставление экземпляров масштабируемых наборов виртуальных машин с узлами
Как показано на предыдущем рисунке, экземпляры масштабируемого набора начинаются с экземпляра 0, а затем увеличиваются на 1. Нумерация узлов отражается в именах. Например, узел BackEnd_0 является нулевым экземпляром масштабируемого набора BackEnd. Этот конкретный масштабируемый набор имеет пять экземпляров с именами BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 и BackEnd_4.

При увеличении масштаба масштабируемого набора создается экземпляр. Имя нового экземпляра масштабируемого набора, как правило, состоит из имени масштабируемого набора и номера следующего экземпляра. В нашем примере это BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Сопоставление балансировщиков нагрузки масштабируемых наборов с типами узлов и масштабируемыми наборами
При развертывании кластера на портале Azure или использовании примера шаблона Azure Resource Manager перечисляются все ресурсы в группе ресурсов. Отображаются балансировщики нагрузки для каждого масштабируемого набора или типа узла. Имя балансировщика нагрузки имеет следующий формат: **LB-&lt;имя типа узла&gt;** , например LB-sfcluster4doc-0, как показано на следующем рисунке:

![Ресурсы][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>Расширение виртуальной машины Service Fabric
Service Fabric расширение виртуальной машины используется для начальной загрузки Service Fabric на виртуальные машины Azure и настройки безопасности узла.

Ниже приведен фрагмент кода расширения виртуальной машины Service Fabric.

```json
"extensions": [
  {
    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
    "properties": {
      "type": "ServiceFabricLinuxNode",
      "autoUpgradeMinorVersion": true,
      "protectedSettings": {
        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
       },
       "publisher": "Microsoft.Azure.ServiceFabric",
       "settings": {
         "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
         "nodeTypeRef": "[variables('vmNodeType0Name')]",
         "durabilityLevel": "Silver",
         "enableParallelJobs": true,
         "nicPrefixOverride": "[variables('subnet0Prefix')]",
         "dataPath": "D:\\\\SvcFab",
         "certificate": {
           "commonNames": [
             "[parameters('certificateCommonName')]"
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
         }
       },
       "typeHandlerVersion": "1.1"
     }
   },
```

Ниже приведены описания свойств.

| **имя** | **Допустимые значения** | ** --- ** | **Рекомендация или краткое описание** |
| --- | --- | --- | --- |
| Имя | строка | --- | уникальное имя для расширения |
| type | "ServiceFabricLinuxNode" или "ServiceFabricWindowsNode" | --- | Идентифицирует Service Fabric ОС для начальной загрузки |
| autoUpgradeMinorVersion | Значение true или false | --- | Включить автоматическое обновление вспомогательных версий среды выполнения SF |
| publisher | Microsoft.Azure.ServiceFabric | --- | имя издателя расширения Service Fabric |
| clusterEndpont | строка | --- | URI: порт для конечной точки управления |
| nodeTypeRef | строка | --- | имя nodeType |
| durabilityLevel | bronze, silver, gold, platinum | --- | время, отведенное на приостановку работы неизменяемой инфраструктуры Azure |
| enableParallelJobs | Значение true или false | --- | Включите COMPUTE Параллелжобс, например Remove VM и перезагрузите виртуальную машину в том же масштабируемом наборе параллельно |
| nicPrefixOverride | строка | --- | Префикс подсети, например "10.0.0.0/24" |
| commonNames | string[] | --- | Распространенные имена установленных сертификатов кластера |
| x509StoreName | строка | --- | Имя магазина, в котором находится установленный сертификат кластера |
| typeHandlerVersion | 1,1 | --- | Версия расширения. 1,0. рекомендуется использовать классическую версию расширения для обновления до 1,1 |
| dataPath | строка | --- | Путь к диску, используемому для сохранения состояния Service Fabric системных служб и данных приложений. 

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения о возможности развертывания в любом месте и сравнении с кластерами под управлением Azure см. в статье [Создание кластеров Service Fabric в Windows Server или Linux](service-fabric-deploy-anywhere.md).
* Дополнительные сведения о безопасности кластеров см. в статье [Сценарии защиты кластера Service Fabric](service-fabric-cluster-security.md).
* [Удаленное подключение](service-fabric-cluster-remote-connect-to-azure-cluster-node.md) к определенному экземпляру масштабируемого набора.
* [Обновление значений диапазона портов RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) в кластере виртуальных машин после развертывания
* [Обновление имени пользователя и пароля администратора](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) для виртуальных машин кластера

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
