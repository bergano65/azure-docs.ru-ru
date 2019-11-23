---
title: включение файла
description: включение файла
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 06b54c3038e8b4f5879a93b696920534c2199008
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74414639"
---
This article provides you with an overview of the availability features of Azure virtual machines (VMs).

## <a name="high-availability"></a>Высокий уровень доступности

Workloads are typically spread across different virtual machines to gain high throughput, performance, and to create redundancy in case a VM is impacted due to an update or other event. 

There are few options that Azure provides to achieve High Availability. First let’s talk about basic constructs. 

### <a name="availability-zones"></a>Зоны доступности

[Availability zones](../articles/availability-zones/az-overview.md) expand the level of control you have to maintain the availability of the applications and data on your VMs. An Availability Zone is a physically separate zone, within an Azure region. В каждом поддерживаемом регионе Azure есть три зоны доступности. 

У каждой зоны доступности есть отдельный источник питания, сеть и система охлаждения. Вы можете защитить приложения и данные от потери в центре обработки данных, настроив свои решения для использования реплицированных виртуальных машин в зонах доступности. В случае неполадок с одной зоной реплицированные приложения и данные сразу же станут доступными в другой зоне. 

![Зоны доступности](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

См. дополнительные сведения о развертывании виртуальных машин [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) или [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) в зонах доступности.


### <a name="fault-domains"></a>Домены сбоя

Домен сбоя — это логическая группа базового оборудования, которое совместно использует общий источник питания и сетевой коммутатор, аналогично стойке в локальном центре обработки данных. 

### <a name="update-domains"></a>Домены обновления

Домен обновления — это логическая группа базового оборудования, которое может одновременно обслуживаться или перезагружаться. 

Такой подход гарантирует, что по крайней мере один экземпляр приложения будет работать во время периодического обслуживания платформы Azure. The order of update domains being rebooted may not proceed sequentially during maintenance, but only one update domain is rebooted at a time.


## <a name="virtual-machines-scale-sets"></a>Наборы масштабирования виртуальных машин. 

Azure virtual machine scale sets let you create and manage a group of load balanced VMs. Число экземпляров виртуальных машин может автоматически увеличиваться или уменьшаться в зависимости от спроса или по определенному расписанию. Scale sets provide high availability to your applications, and allow you to centrally manage, configure, and update many VMs. We recommended that two or more VMs are created within a scale set to provide for a highly available application and to meet the [99.95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). There is no cost for the scale set itself, you only pay for each VM instance that you create. Если отдельная виртуальная машина использует [SSD Azure (цен. категория "Премиум")](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd), соглашение об уровне обслуживания Azure применяется для внеплановых мероприятий обслуживания. Virtual machines in a scale set can be deployed across multiple update domains and fault domains to maximize availability and resilience to outages due to data center outages, and planned or unplanned maintenance events. Virtual machines in a scale set can also be deployed into a single Availability zone, or regionally. Availability zone deployment options may differ based on the orchestration mode.

### <a name="preview-orchestration-mode-preview"></a>Preview: Orchestration mode Preview
Virtual machines scale sets allow you to specify orchestration mode.  With the virtual machine scale set orchestration mode (preview), you can now choose whether the scale set should orchestrate virtual machines which are created explicitly outside of a scale set configuration model, or virtual machine instances created implicitly based on the configuration model. Choose the orchestration mode that VM orchestration model allows you group explicitly defined Virtual Machines together in a region or in an availability zone. Virtual machines deployed in an Availability Zone provides zonal isolation to VMs are they are bound to the availability zone boundary and are not subjected to any failures that may occur in other availability zone in the region. 

|   | “orchestrationMode”: “VM” (VirtualMachine)| “orchestrationMode”: “ScaleSetVM” (VirtualMachineScaleSetVM) |
|----|----|----|
| VM configuration model| Нет подходящих вариантов. VirtualMachineProfile is undefined in the scale set model. | Обязательный параметр. VirtualMachineProfile is populated in the scale set model. |
| Adding new VM to Scale Set| VMs are explicitly added to the scale set when the VM is created. | VMs are implicitly created and added to the scale set based on the VM configuration model, instance count, and AutoScaling rules. |
| зоны доступности;| Supports regional deployment or VMs in one Availability Zone| Supports regional deployment or multiple Availability Zones; Can define the zone balancing strategy |
| Домены сбоя| Can define fault domains count. 2 or 3 based on regional support and 5 for Availability zone. The assigned VM fault domain will persist with VM lifecycle, including deallocate and restart. | Can define 1, 2, or 3 fault domains for non-zonal deployments, and 5 for Availability zone deployments. The assigned VM fault domain does not persist with VM lifecycle, virtual machines are assigned a fault domain at time of allocation. |
| Домены обновления| Недоступно Update domains are automatically mapped to fault domains| Недоступно Update domains are automatically mapped to fault domains |

**Fault domains and update domains**

Virtual machine scale sets simplify designing for high availability by aligning fault domains and update domains. You will only have to define fault domains count for the scale set. The number of fault domains available to the scale sets may vary by region. See [Number of Fault Domains per region](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#number-of-fault-domains-per-region).


## <a name="availability-sets"></a>Группы доступности
Группа доступности — это логическое объединение виртуальных машин в центре обработки данных, которое позволяет Azure определить структуру вашего приложения, чтобы обеспечить избыточность и доступность. Для обеспечения высокой доступности приложения и достижения показателя [99,95 % уровня обслуживания (SLA) Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) мы рекомендуем создать в группе доступности не менее двух виртуальных машин. Группа доступности представляется бесплатно, оплачивается только каждый созданный экземпляр виртуальной машины. Если отдельная виртуальная машина использует [SSD Azure (цен. категория "Премиум")](../articles/virtual-machines/windows/disks-types.md#premium-ssd), соглашение об уровне обслуживания Azure применяется для внеплановых мероприятий обслуживания.

In an availability set, VMs are automatically distributed across these fault domains. Данный подход ограничивает влияние потенциальных сбоев физического оборудования, сети или электропитания.

При использовании управляемой группы доступности виртуальные машины, использующие [управляемые диски Azure](../articles/virtual-machines/windows/faq-for-disks.md), согласовываются с доменами сбоя управляемого диска. Это гарантирует, что все управляемые диски, подключенные к виртуальной машине, относятся к одному домену сбоя управляемого диска. 

В управляемой группе доступности можно создать только виртуальные машины с управляемыми дисками. Количество доменов сбоя управляемых дисков зависит от региона: два или три на один регион. См. дополнительные сведения о доменах сбоя управляемых дисков для [виртуальных машин Linux](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) и [виртуальных машин Windows](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set).

![Управляемая группа доступности](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


VMs within an availability set are also automatically distributed across update domains. 

![Группы доступности](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>Дальнейшие действия
Теперь вы можете использовать функции доступности и избыточности для создания среды Azure. Рекомендации см. в статье [Контрольный список для обеспечения доступности](/azure/architecture/checklist/resiliency-per-service).

