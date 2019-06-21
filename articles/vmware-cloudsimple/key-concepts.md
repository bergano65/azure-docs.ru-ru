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
ms.openlocfilehash: 3eff61408cb190396987ace6dee21182cff4f25c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165185"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solution-by-cloudsimple"></a>Основные понятия для администрирования решения Azure VMware с CloudSimple

Администрирование Azure VMware решения по CloudSimple важно понимать следующие концепции:

* CloudSimple службу, которая отображается в виде решение VMware в Azure с CloudSimple - службы
* CloudSimple узла, который отображается в виде решение VMware в Azure с CloudSimple - узел
* CloudSimple частного облака
* Сетевые службы
* CloudSimple виртуальную машину, которая отображается в виде решение VMware в Azure с CloudSimple - виртуальной машины

## <a name="cloudsimple-service"></a>Служба CloudSimple

В службе CloudSimple можно создать и управлять всеми ресурсами, связанные с решениями VMware, CloudSimple на портале Azure. Создайте ресурс службы в каждом регионе, где планируется использовать службу.

Дополнительные сведения о [CloudSimple службы](cloudsimple-service.md).

## <a name="cloudsimple-node"></a>Узел CloudSimple

Узел CloudSimple — узла на вычислительные ресурсы и хранилище гиперконвергентный выделенный, без операционной системы, в которую развертывается гипервизор VMware ESXi. Этот узел, затем будет включено в VMware vSphere, vCenter, виртуальной сети хранения данных и NSX платформ. Также включены CloudSimple сетевые службы и сетевые службы edge. Каждый узел выступает в качестве единицы вычислений и емкость хранилища, вы можете подготовить для создания [CloudSimple частных облаков](cloudsimple-private-cloud.md). Подготовить или зарезервировать узла в регионе, где доступна служба CloudSimple.


Дополнительные сведения о [CloudSimple узлы](cloudsimple-node.md).

## <a name="cloudsimple-private-cloud"></a>CloudSimple частного облака

Частное облако CloudSimple — изолированная среда stack VMware под управлением сервера vCenter в своем собственном домене управления. Стек VMware включает ESXi узлов vSphere, vCenter, виртуальной сети хранения данных и NSX. Запуски стека на выделенных узлов (выделенный и изолированной физического оборудования) и используется пользователями с помощью собственного средства VMware, которые включают vCenter и NSX Manager. Выделенные узлы развертываются в расположениях Azure и управляются Azure. Каждого частного облака можно сегментировать и защищенный с помощью сетевых служб, таких как виртуальные локальные сети и подсети и таблицы брандмауэра. Подключения к вашей локальной среде и сетью Azure, создаваемые с помощью безопасной частный VPN и Azure ExpressRoute подключений.

Дополнительные сведения о [CloudSimple частного облака](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Сетевые службы

Эта служба CloudSimple предоставляет сети на регион, где развернута служба CloudSimple. Сеть не одно пространство адресов TCP уровня 3 с маршрутизацией включен по умолчанию. Все частные облака и подсетей, созданные в данном регионе взаимодействовать друг с другом без дополнительной настройки. Создание групп портов, распределенных на сервере vCenter, используя виртуальные локальные сети. Следующие функции сети можно использовать для настройки и защиты ресурсов рабочей нагрузки в вашем частном облаке:

* [Виртуальные ЛС и подсети](cloudsimple-vlans-subnets.md)
* [Таблицы брандмауэра](cloudsimple-firewall-tables.md)
* [VPN-шлюзов](cloudsimple-vpn-gateways.md)
* [Общедоступный IP-адрес](cloudsimple-public-ip-address.md)
* [Подключение сети Azure](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>CloudSimple виртуальной машины

В службе CloudSimple виртуальных машин VMware можно управлять на портале Azure. Один или несколько кластеров, или пулов ресурсов в вашей среде vSphere могут сопоставляться с подписки, для которой создается служба.

См. также:

* [CloudSimple виртуальных машин](cloudsimple-virtual-machines.md)
* [Сопоставление подписки Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
