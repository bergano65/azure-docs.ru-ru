---
title: Основные понятия администрирования решений Azure VMware (AVS)
description: Описание основных принципов администрирования решений Azure VMware (AVS)
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c581ffbba7fbaefc8fbd0ffc7e92350bd69802b8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025237"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-avs"></a>Основные понятия администрирования решений Azure VMware (AVS)

Для администрирования решений Azure VMware (AVS) необходимо понимать следующие понятия.

* Служба AVS, которая отображается как решения Azure VMware (AVS) — служба
* Узел AVS, который отображается как решения Azure VMware (AVS) — узел
* Частное облако AVS
* Сетевые службы
* Виртуальная машина AVS, которая отображается как решения Azure VMware (AVS) — виртуальная машина

## <a name="avs-service"></a>Служба AVS

С помощью службы AVS можно создавать все ресурсы, связанные с решениями VMware (AVS), и управлять ими из портал Azure. Создайте ресурс службы в каждом регионе, где вы планируете использовать службу.

Дополнительные сведения о [службе AVS](cloudsimple-service.md).

## <a name="avs-node"></a>Узел AVS

Узел AVS — это выделенный, гиперконвергентномный и дисковый узел, на котором развернута низкоуровневая оболочка VMware ESXi. Затем этот узел внедряется в платформы VMware vSphere, vCenter, vSAN и НСКС. Также включены сетевые службы и пограничные сетевые службы AVS. Каждый узел выступает в качестве единицы вычислений и емкости хранилища, которые можно подготавливать для создания [частных облаков AVS](cloudsimple-private-cloud.md). Вы предоставляете или резервируете узлы в регионе, где доступна служба AVS.

Дополнительные сведения об [узлах AVS](cloudsimple-node.md).

## <a name="avs-private-cloud"></a>Частное облако AVS

Частное облако AVS — это изолированная среда VMware Stack, управляемая сервером vCenter в собственном домене управления. Стек VMware включает узлы ESXi, vSphere, vCenter, vSAN и НСКС. Стек работает на выделенных узлах (выделенном и изолированном оборудовании без операционной системы) и потребляется пользователями с помощью собственных средств VMware, включающих vCenter и НСКС Manager. Выделенные узлы развертываются в расположениях Azure и управляются с помощью Azure. Каждое частное облако AVS может быть сегментировано и защищено с помощью сетевых служб, таких как виртуальные ЛС и подсети и таблицы брандмауэра. Подключения к локальной среде и сети Azure создаются с помощью защищенных, частных VPN и подключений Azure ExpressRoute.

Дополнительные сведения о [частном облаке AVS](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Сетевые службы

Служба AVS предоставляет сеть для каждого региона, в котором развернута служба AVS. Сеть — это одно адресное пространство TCP уровня 3 с включенной маршрутизацией по умолчанию. Все частные облака и подсети AVS, созданные в этом регионе, взаимодействуют друг с другом без дополнительной настройки. Группы распределенных портов создаются в vCenter с помощью виртуальных ЛС. Для настройки и защиты ресурсов рабочей нагрузки в частном облаке AVS можно использовать следующие сетевые функции:

* [Виртуальные ЛС и подсети](cloudsimple-vlans-subnets.md)
* [Таблицы брандмауэра](cloudsimple-firewall-tables.md)
* [VPN-шлюзы](cloudsimple-vpn-gateways.md)
* [Общедоступный IP-адрес](cloudsimple-public-ip-address.md)
* [Сетевое подключение Azure](cloudsimple-azure-network-connection.md)

## <a name="avs-virtual-machine"></a>Виртуальная машина AVS

С помощью службы AVS можно управлять виртуальными машинами VMware из портал Azure. Один или несколько кластеров или пулов ресурсов из среды vSphere можно сопоставить с подпиской, в которой создана служба.

См. также:

* [Виртуальные машины AVS](cloudsimple-virtual-machines.md)
* [Сопоставление подписок Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
