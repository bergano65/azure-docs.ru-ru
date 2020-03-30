---
title: Решение Azure VMware от CloudSimple - Перенос наемных нагрузок в частные облака
description: Описывает, как перенести виртуальные машины из собственного vCenter в CloudSimple Private Cloud vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 87b8a112a319519dbde977ee30136a884137212d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77020001"
---
# <a name="migrate-workload-vms-from-on-premises-vcenter-to-private-cloud-vcenter-environment"></a>Перенос рабочей нагрузки VMs из находинного vCenter в среду Private Cloud vCenter

Для переноса вдвистого из центра обработки данных в облачное частное облако доступно несколько вариантов.  Частное облако предоставляет нативный доступ к VMware vCenter, а инструменты, поддерживаемые VMware, могут быть использованы для миграции рабочей нагрузки. В этой статье описаны некоторые варианты миграции vCenter.

## <a name="prerequisites"></a>Предварительные требования

Миграция вс-м носов и данных из вашего центра обработки данных требует подключения сети от центра обработки данных к среде Private Cloud.  Используйте любой из следующих методов для установления подключения к сети:

* [VPN-соединение](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) от места к сайту между вашей предварительной средой и вашим частным облаком.
* Соединение ExpressRoute Global Reach между вашей натерритории ExpressRoute и схемой CloudSimple ExpressRoute.

Сетевой путь от среды вашего собственного vCenter до частного облака должен быть доступен для миграции ВМ с помощью vMotion.  Сеть vMotion на вашем внепредтеv центре должна иметь возможности для разгрома.  Убедитесь, что ваш брандмауэр позволяет весь трафик vMotion между вашим наемным vCenter и Private Cloud vCenter. (В частном облаке, скорость в сети vMotion настроена по умолчанию.)

## <a name="migrate-isos-and-templates"></a>Мигрировать IsOs и шаблоны

Для создания новых виртуальных машин в частном облаке используйте шаблоны ISOs и VM.  Чтобы загрузить ИПИ и шаблоны в vCenter private Cloud и сделать их доступными, используйте следующий метод.

1. Загрузите ISO в Private Cloud vCenter из uI vCenter.
2. [Публикация библиотеки контента](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A0F1C13-7336-45CE-B211-610D39A6E1F4.html) в vCenter Private Cloud:

    1. Публикация вашей библиотеки контента.
    2. Создайте новую библиотеку контента в VCenter Private Cloud.
    3. Подпишитесь на опубликованную библиотеку контента.
    4. Синхронизация библиотеки содержимого для доступа к подписанного содержимого.

## <a name="migrate-vms-using-powercli"></a>Мигрировать VMs с помощью PowerCLI

Для переноса вс-мнопов из центра vCenter в частный облачный vCenter воспользуйтесь VMware PowerCLI или утилитой по миграции рабочей нагрузки Cross vCenter, доступной в VMware Labs.  Следующий пример сценария показывает команды миграции PowerCLI.

```
$sourceVC = Connect-VIServer -Server <source-vCenter name> -User <source-vCenter user name> -Password <source-vCenter user password>
$targetVC = Connect-VIServer -Server <target-vCenter name> -User <target-vCenter user name> -Password <target-vCenter user password>
$vmhost = <name of ESXi host on destination>
$vm = Get-VM -Server $sourceVC <name of VM>
Move-VM -VM $vm -VMotionPriority High -Destination (Get-VMhost -Server $targetVC -Name $vmhost) -Datastore (Get-Datastore -Server $targetVC -Name <name of tgt vc datastore>)
```

> [!NOTE]
> Чтобы использовать имена сервера назначения vCenter и хостов ESXi, назначьте dNS-переслюк из собственного облака в частное облако.

## <a name="migrate-vms-using-nsx-layer-2-vpn"></a>Мигрировать VMs с помощью NSX Слой 2 VPN

Эта опция позволяет осуществлять живую миграцию рабочих нагрузок из среды VMware в частное облако в Azure.  Благодаря этой растянутой сети layer 2 подсеть из помещения будет доступна в частном облаке.  После миграции для вс-кнопок не требуется новое назначение IP-адреса.

[Перенос рабочих нагрузок с помощью растянутых сетей уровня 2](migration-layer-2-vpn.md) описывает, как использовать VPN слоя 2 для того, чтобы растянуть сеть слоя 2 из вашей предварительной среды в частное облако.

## <a name="migrate-vms-using-backup-and-disaster-recovery-tools"></a>Мигрировать ВМ с помощью средств резервного копирования и аварийного восстановления

Миграция вс-м вобучел в private Cloud может осуществляться с помощью инструментов резервного копирования/восстановления и средств аварийного восстановления.  Используйте приватное облако в качестве мишени для восстановления резервных данных, созданных с помощью стороннего инструмента.  Частное облако также может быть использовано в качестве мишени для аварийного восстановления с помощью VMware SRM или стороннего инструмента.

Для получения дополнительной информации с помощью этих инструментов, см.

* [Резервное копирование виртуальных компьютеров рабочей нагрузки на CloudSimple Private Cloud с помощью Veeam B&R](backup-workloads-veeam.md)
* [Настройка облачного частного облака Cloud как места аварийного восстановления для рабочих нагрузок VMware](disaster-recovery-zerto.md)
