---
title: Лучшие практики сети Azure Service Fabric
description: Рекомендации по управлению подключением к сети с помощью Сервиса Azure Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: de2a74ad2d61de18d2150b72be3251e5b5583f2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551800"
---
# <a name="networking"></a>Сети

При создании кластеров Azure Service Fabric и управлении ими вы обеспечиваете сетевое подключение для узлов и приложений. Сетевые ресурсы включают в себя диапазоны IP-адресов, виртуальные сети, подсистемы балансировки нагрузки и группы безопасности сети. В этой статье вы узнаете рекомендации по работе с этими ресурсами.

Просмотрите [шаблоны сетей](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking) Azure Service Fabric, чтобы узнать, как создавать кластеры, которые используют следующие функции: Существующая виртуальная сеть или подсеть, Static public IP-адрес, внутренний балансер нагрузки или внутренний и внешний балансера груз.

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

Ускоренная сеть поддерживается для SkUS серии виртуальных машин Azure: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 и Ms/Mms. Ускоренная работа в сети успешно опробована с помощью номера SKU Standard_DS8_v3 23.01.2019 в кластере Service Fabric на платформе Windows и номера SKU Standard_DS12_v2 29.01.2019 в кластере Service Fabric на платформе Linux.

Чтобы включить ускоренную работу в сети в имеющемся кластере Service Fabric, следует [масштабировать кластер Service Fabric путем добавления масштабируемого набора виртуальных машин](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out). Затем вы сможете выполнить следующие действия:
1. Подготовить NodeType с поддержкой ускоренной работы в сети.
2. Перенести службы и их состояния в подготовленный NodeType с поддержкой ускоренной работы в сети.

Масштабирование инфраструктуры требуется для включения ускоренной работы в сети в имеющемся кластере. Включение ускоренной работы в сети приведет к простою, так как все виртуальные машины в группе доступности следует [остановить и освободить, прежде чем включить ускорение на любой имеющейся сетевой карте](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Сеть кластера

* Кластеры Service Fabric можно развернуть в имеющейся виртуальной сети, выполнив действия, описанные в статье [Схемы сетевых подключений Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

* Группы безопасности сети (NSG) рекомендуются для типов узлов, которые ограничивают входящий и исходящий трафик в своем кластере. Убедитесь, что в NSG открыты необходимые порты. Например: ![Правила NSG ткани обслуживания][NSGSetup]

* Основной тип узла, который содержит системные службы Service Fabric, не должен предоставляться через внешний балансировщик нагрузки и может предоставляться с помощью [внутреннего](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#internal-only-load-balancer).

* Используйте [статический общедоступный IP-адрес](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#static-public-ip-address-1) для кластера.

## <a name="application-networking"></a>Сеть для приложений

* Для запуска рабочих нагрузок контейнера Windows используйте [режим открытой сети](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode), чтобы упростить взаимодействие между службами.

* Используйте обратный прокси-сервер, например [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) или [обратный прокси-сервер Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy), чтобы предоставить распространенные порты для приложений, например 80 или 443.

* Для Windows Контейнеров, размещенных на компьютерах, работающих с воздухом, которые не могут вытащить базовые слои из облачного хранилища Azure, переопределить поведение иностранного слоя, используя [флаг --нераспределенно-артисируемые артефакты](https://docs.microsoft.com/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) в Docker daemon.

## <a name="next-steps"></a>Дальнейшие действия

* Создание кластера на основе виртуальных машин или компьютеров под управлением Windows Server: [Создание кластера Azure Service Fabric в локальной или облачной средах](service-fabric-cluster-creation-for-windows-server.md)
* Создание кластера на основе виртуальных машин или компьютеров под управлением Linux: [Создание кластера Linux](service-fabric-cluster-creation-via-portal.md).
* Узнайте о [вариантах поддержки Service Fabric](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
