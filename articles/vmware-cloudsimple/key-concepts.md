---
title: Основные понятия для администрирования решения Azure VMware с CloudSimple
description: Описывает основные понятия для администрирования решения Azure VMware с CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0d890553ee145ca6aafed5a34d158c6a34d9af36
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66358173"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solution-by-cloudsimple"></a>Основные понятия для администрирования решения Azure VMware с CloudSimple

Администрирование Azure VMware решения по CloudSimple необходимо понимать следующие концепции.

* Служба CloudSimple (отображается как решение VMware в Azure с CloudSimple - службы)
* Узел CloudSimple (отображается как решение VMware в Azure с CloudSimple - узел)
* CloudSimple частного облака
* Сетевой службы
* CloudSimple виртуальной машины (отображается как решение VMware в Azure с CloudSimple - виртуальной машины)

## <a name="cloudsimple-service"></a>Служба CloudSimple

Служба CloudSimple позволяет создавать и администрировать все ресурсы, связанные с решениями VMware, CloudSimple на портале Azure. Создайте ресурс службы в каждом регионе, где планируется использовать службу. 

Дополнительные сведения о [CloudSimple службы](cloudsimple-service.md)

## <a name="cloudsimple-node"></a>Узел CloudSimple

Узел CloudSimple — это выделенный невиртуализированный гиперконвергентный вычислительные ресурсы и хранилище узла, в которую развертывается гипервизор VMware ESXi. Этот узел, затем будет включено в VMware vSphere, vCenter, виртуальной сети хранения данных и NSX платформ. Также включены CloudSimple сетевые службы и сетевые службы edge. Каждый узел выступает в качестве единицы вычислений и емкость хранилища, вы можете приобрести для создания [CloudSimple частных облаков](cloudsimple-private-cloud.md). Вы приобретаете или зарезервировать узла в регионе, где доступна служба CloudSimple.


Дополнительные сведения о [CloudSimple узлов](cloudsimple-node.md)

## <a name="cloudsimple-private-cloud"></a>CloudSimple частного облака

CloudSimple частного облака — это изолированная среда stack VMware под управлением сервера vCenter в своем собственном домене управления. Стек VMware включает ESXi узлов vSphere, vCenter, виртуальной сети хранения данных и NSX.  Запуски стека на выделенных узлов (выделенный и изолированной физического оборудования) и используется пользователями с помощью собственного средства VMware, которые включают vCenter и NSX Manager. Выделенные узлы развертываются в расположениях Azure и управляются Azure. Каждого частного облака можно сегментировать и защищать с помощью сетевых служб, таких как виртуальные локальные сети, подсетей и брандмауэра таблиц.  Подключения к вашей локальной среде и сетью Azure создаются с помощью VPN, безопасность и конфиденциальность и подключения Azure ExpressRoute.

Дополнительные сведения о [CloudSimple частного облака](cloudsimple-private-cloud.md)

## <a name="service-networking"></a>Сетевые службы

Эта служба CloudSimple предоставляет сети на регион, где развернута служба CloudSimple. Сеть не одно пространство адресов TCP уровня 3 с маршрутизацией включен по умолчанию. Все частные облака и подсетей, созданные в данном регионе взаимодействовать друг с другом без дополнительной настройки. Создание групп портов, распределенных на vCenter, используя виртуальные локальные сети.  Следующие функции сети можно использовать для настройки и защиты ресурсов рабочей нагрузки в частным облаком.

* [Виртуальные локальные сети и подсети](cloudsimple-vlans-subnets.md)
* [Таблицы брандмауэра](cloudsimple-firewall-tables.md)
* [VPN-шлюзов](cloudsimple-vpn-gateways.md)
* [Общедоступный IP-адрес](cloudsimple-public-ip-address.md)
* [Подключение сети Azure](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>CloudSimple виртуальной машины

Служба CloudSimple позволяет управлять виртуальными машинами VMware с помощью портала Azure. Один или несколько кластеров, или пулов ресурсов в вашей среде vSphere могут сопоставляться с подписки, для которой создается служба.

См. также:

* [CloudSimple виртуальных машин](cloudsimple-virtual-machines.md)
* [Сопоставление подписки Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
