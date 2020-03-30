---
title: Типы узлов и масштабируемые наборы виртуальных машин
description: Узнайте, как типы узлов Azure Service Fabric соотносятся с виртуальными наборами масштабов машин и как удаленно подключаться к экземпляру набора масштаба или кластеру.
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: pepogors
ms.custom: sfrev
ms.openlocfilehash: 37d4c27d3033545c523cefc2f317073af531f095
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78199722"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Типы узлов Azure Service Fabric и масштабируемые наборы виртуальных машин

[Масштабируемые наборы виртуальных машин](/azure/virtual-machine-scale-sets) являются вычислительными ресурсами Azure. Их можно использовать для развертывания коллекций виртуальных машин и управления ими в качестве набора. Каждый тип узла, который определяется в кластере Azure Service Fabric, настраивает ровно один набор масштабов: несколько типов узлов не могут быть подкреплены одним и тем же набором масштабов, а один тип узла не должен (в большинстве случаев) быть подкреплен несколькими наборами масштабов. Исключением является редкая ситуация [вертикального масштабирования типа узла,](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations) когда у вас временно `nodeTypeRef` есть два набора масштабов с тем же значением, в то время как реплики переносятся из исходного в обновленный набор масштабов.

Время выполнения Service Fabric устанавливается на каждую виртуальную машину в масштабе, установленном расширением виртуальной машины *Microsoft.Azure.ServiceFabric* Virtual Machine. Все типы узлов можно масштабировать независимо друг от друга, изменять номер SKU операционной системы, работающей на узле кластера, открывать разные наборы портов и использовать различные метрики производительности.

На следующем рисунке показан кластер, который имеет два типа узлов, *названных FrontEnd* и *BackEnd.* Каждый тип узла имеет пять узлов.

![Кластер с двумя типами узлов][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Сопоставление экземпляров масштабируемых наборов виртуальных машин с узлами

Как показано на предыдущем рисунке, экземпляры масштабируемого набора начинаются с экземпляра 0, а затем увеличиваются на 1. Нумерация узлов отражается в именах. Например, узел BackEnd_0 является нулевым экземпляром масштабируемого набора BackEnd. Этот конкретный масштабируемый набор имеет пять экземпляров с именами BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 и BackEnd_4.

При увеличении масштаба масштабируемого набора создается экземпляр. Имя нового экземпляра масштабируемого набора, как правило, состоит из имени масштабируемого набора и номера следующего экземпляра. В нашем примере это BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Сопоставление балансировщиков нагрузки масштабируемых наборов с типами узлов и масштабируемыми наборами

При развертывании кластера на портале Azure или использовании примера шаблона Azure Resource Manager перечисляются все ресурсы в группе ресурсов. Отображаются балансировщики нагрузки для каждого масштабируемого набора или типа узла. Имя балансировщика нагрузки имеет следующий формат: **LB-&lt;имя типа узла&gt;**, например LB-sfcluster4doc-0, как показано на следующем рисунке:

![Ресурсы][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>Сервис Ткань Виртуальное расширение машины

Сервис Ная виртуальная машина расширение используется для загрузки службы Ткань для Azure виртуальных машин, и настроить узел безопасности.

Ниже приводится фрагмент расширения сервисной ткани виртуальной машины:

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

Ниже приведены описания свойств:

| **Название** | **Разрешенные значения** | **Руководство или краткое описание** |
| --- | --- | --- | --- |
| name | строка | Уникальное название для расширения |
| type | "ServiceFabricLinuxNode" или "ServiceFabricWindowsNode" | Идентифицирует OS Service Ткань является загрузка для |
| autoUpgradeМинминверсия | true или false | Включить автоматическое обновление SF Runtime Малой версии |
| publisher | Microsoft.Azure.ServiceFabric | Название издателя расширения Service Fabric |
| кластерЭндпонт | строка | URI:PORT к конечная точка управления |
| nodeTypeRef | строка | Название nodeType |
| долговечностьУровень | бронза, серебро, золото, платина | Время позволило приостановить неизменяемую инфраструктуру Azure |
| включитьParallelJobs | true или false | Включить compute ParallelJobs, как удалить VM и перезагрузить VM в той же шкале, установленной параллельно |
| nicPrefixOverride | строка | Префикс subnet как "10.0.0.0/24" |
| общиеимена | string[] | Общие названия установленных кластерных сертификатов |
| x509StoreName | строка | Название магазина, где находится установленный кластерный сертификат |
| typeHandlerVersion | 1,1 | Версия расширения. 1.0 классическая версия расширения рекомендуется обновить до 1.1 |
| dataPath | строка | Путь к диску, используемому для сохранения состояния для служб ыобслуживания Fabric системных служб и данных приложений.

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о возможности развертывания в любом месте и сравнении с кластерами под управлением Azure см. в статье [Создание кластеров Service Fabric в Windows Server или Linux](service-fabric-deploy-anywhere.md).
* Узнайте о [безопасности кластеров.](service-fabric-cluster-security.md)
* [Удаленное подключение](service-fabric-cluster-remote-connect-to-azure-cluster-node.md) к определенному экземпляру масштабируемого набора.
* [Обновление значений диапазона портов RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) в кластере виртуальных машин после развертывания
* [Обновление имени пользователя и пароля администратора](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) для виртуальных машин кластера

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
