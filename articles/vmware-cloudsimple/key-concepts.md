---
title: Ключевые концепции администрирования решения Azure VMware от CloudSimple
titleSuffix: Azure VMware Solution by CloudSimple
description: Описывает ключевые концепции администрирования решений Azure VMware от CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 410215550f728d6b11b02b3ef108d429481e42a0
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869025"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-by-cloudsimple"></a>Ключевые концепции администрирования решений Azure VMware от CloudSimple

Администрирование решений Azure VMware от CloudSimple требует понимания следующих концепций:

* Сервис CloudSimple, который отображается как решения Azure VMware от CloudSimple - Сервис
* Узел CloudSimple, который отображается как решения Azure VMware от CloudSimple - Узел
* Частное облако CloudSimple
* Сеть услуг
* Виртуальная машина CloudSimple, которая отображается как решения Azure VMware от CloudSimple - Виртуальная машина

## <a name="cloudsimple-service"></a>Сервис CloudSimple

С помощью сервиса CloudSimple вы можете создавать и управлять всеми ресурсами, связанными с решениями VMware, с помощью CloudSimple с портала Azure. Создайте ресурс службы в каждом регионе, где вы собираетесь использовать службу.

Подробнее о [сервисе CloudSimple.](cloudsimple-service.md)

## <a name="cloudsimple-node"></a>Облачный простой узла

Узла CloudSimple представляет собой специальный, голый металл, гиперконвергентный вычислительный и складской хост, в который развертывается гипервизор VMware ESXi. Затем этот узла включается в платформы VMware vSphere, vCenter, vSAN и NSX. Также включены сетевые сервисы CloudSimple и услуги edge network. Каждый уза служит единицей вычислительной и емкости, которую можно предоставить для создания [частных облаков CloudSimple.](cloudsimple-private-cloud.md) Вы предоставляете или резервируете узлы в регионе, где доступна услуга CloudSimple.

Подробнее о [узлах CloudSimple](cloudsimple-node.md).

## <a name="cloudsimple-private-cloud"></a>Частное облако CloudSimple

Частное облако CloudSimple — это изолированная среда стеков VMware, управляемая сервером vCenter в домене управления. Стек VMware включает в себя хосты ESXi, vSphere, vCenter, vSAN и NSX. Стек работает на выделенных узлах (посвященных и изолированных голо-металлических аппаратных средств) и потребляется пользователями через родной VMware инструменты, которые включают vCenter и NSX Manager. Выделенные узлы развернуты в местах расположения Azure и управляются Azure. Каждое частное облако может быть сегментировано и защищено с помощью сетевых служб, таких как VPN и подсети и таблицы брандмауэров. Соединения с вашей предварительной средой и сетью Azure создаются с помощью защищенных, частных VPN и подключений Azure ExpressRoute.

Узнайте больше о [частном облаке CloudSimple](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Сеть услуг

Служба CloudSimple предоставляет сеть в регионе, где развернута служба CloudSimple. Сеть представляет собой единое адресное пространство TCP Layer 3 с включенной по умолчанию реуктором. Все частные облака и подсети, созданные в этом регионе, взаимодействуют друг с другом без дополнительной конфигурации. Вы создаете распределенные группы портов на vCenter с помощью VLANs. Вы можете использовать следующие сетевые функции для настройки и защиты ресурсов рабочей нагрузки в частном облаке:

* [Виртуальные локальные сети и подсети](cloudsimple-vlans-subnets.md)
* [Таблицы брандмауэра](cloudsimple-firewall-tables.md)
* [VPN-шлюзы](cloudsimple-vpn-gateways.md)
* [Общедоступный IP-адрес](cloudsimple-public-ip-address.md)
* [Подключение к сети Azure](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>Виртуальная машина CloudSimple

С помощью сервиса CloudSimple вы можете управлять виртуальными машинами VMware с портала Azure. Один или несколько кластеров или пулов ресурсов из среды vSphere можно отобразить на подписку, на которой создана служба.

См. также:

* [Виртуальные машины CloudSimple](cloudsimple-virtual-machines.md)
* [Отображение подписки Azure](https://docs.microsoft.com/azure/vmware-cloudsimple/azure-subscription-mapping/)
