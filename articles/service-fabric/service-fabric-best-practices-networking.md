---
title: Рекомендации по сетевой активности в Azure Service Fabric | Документация Майкрософт
description: Рекомендации по управлению сетевыми подключениями Service Fabric.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 86ad6fce34f323d94f7b9c318ba81f547360d4df
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56804970"
---
# <a name="networking"></a>Сеть

При создании кластеров Azure Service Fabric и управлении ими вы обеспечиваете сетевое подключение для узлов и приложений. Сетевые ресурсы включают в себя диапазоны IP-адресов, виртуальные сети, подсистемы балансировки нагрузки и группы безопасности сети. В этой статье вы узнаете рекомендации по работе с этими ресурсами.

Просмотрите [схемы сетевых подключений Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking), чтобы узнать, как создавать кластеры, использующие следующие функции: имеющаяся виртуальная сеть или подсеть, статический общедоступный IP-адрес, только внутренняя подсистема балансировки нагрузки или внутренняя и внешняя подсистема балансировки нагрузки.

## <a name="infrastructure-networking"></a>Сеть инфраструктуры
Увеличьте производительность виртуальной машины с помощью ускорения работы в сети, объявив свойство enableAcceleratedNetworking в шаблоне Resource Manager. В следующем фрагменте кода представлено свойство NetworkInterfaceConfigurations масштабируемого набора виртуальных машин, позволяющее ускорить работу в сети.

```json
"networkInterfaceConfigurations": [
  {
    "name": "[concat(variables('nicName'), '-0')]",
    "properties": {
      "enableAcceleratedNetworking": true,
      "ipConfigurations": [
        {
        <snip>
        }
      ],
      "primary": true
    }
  }
]
```
Кластер Service Fabric можно подготовить с ускоренной работой в сети в [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) и [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).

Ускоренная работа в сети поддерживается для следующих номеров SKU виртуальных машин Azure: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 и Ms/Mms. Ускоренная работа в сети успешно опробована с помощью номера SKU Standard_DS8_v3 23.01.2019 в кластере Service Fabric на платформе Windows и номера SKU Standard_DS12_v2 29.01.2019 в кластере Service Fabric на платформе Linux.

Чтобы включить ускоренную работу в сети в имеющемся кластере Service Fabric, следует [масштабировать кластер Service Fabric путем добавления масштабируемого набора виртуальных машин](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out). Затем вы сможете выполнить следующие действия:
1. Подготовить NodeType с поддержкой ускоренной работы в сети.
2. Перенести службы и их состояния в подготовленный NodeType с поддержкой ускоренной работы в сети.

Масштабирование инфраструктуры требуется для включения ускоренной работы в сети в имеющемся кластере. Включение ускоренной работы в сети приведет к простою, так как все виртуальные машины в группе доступности следует [остановить и освободить, прежде чем включить ускорение на любой имеющейся сетевой карте](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Сеть кластера

* Кластеры Service Fabric можно развернуть в имеющейся виртуальной сети, выполнив действия, описанные в статье [Схемы сетевых подключений Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

* Группы безопасности сети (NSG) рекомендуются для типов узлов, которые ограничивают входящий и исходящий трафик в своем кластере. Убедитесь, что в NSG открыты необходимые порты. Например:  ![Правила группы безопасности сети Service Fabric][NSGSetup]

* Основной тип узла, который содержит системные службы Service Fabric, не должен предоставляться через внешний балансировщик нагрузки и может предоставляться с помощью [внутреннего](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#internal-only-load-balancer).

* Используйте [статический общедоступный IP-адрес](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#static-public-ip-address-1) для кластера.

## <a name="application-networking"></a>Сеть для приложений

* Для запуска рабочих нагрузок контейнера Windows используйте [режим открытой сети](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode), чтобы упростить взаимодействие между службами.

* Используйте обратный прокси-сервер, например [Traefik](https://docs.traefik.io/configuration/backends/servicefabric/) или [обратный прокси-сервер Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy), чтобы предоставить распространенные порты для приложений, например 80 или 443.

## <a name="next-steps"></a>Дальнейшие действия

* Создание кластера на основе виртуальных машин или компьютеров под управлением Windows Server: [Создание изолированного кластера под управлением Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Создание кластера на основе виртуальных машин или компьютеров под управлением Linux: [Создание кластера Service Fabric в Azure с помощью портала Azure](service-fabric-cluster-creation-via-portal.md)
* [Сведения о вариантах поддержки Service Fabric](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
