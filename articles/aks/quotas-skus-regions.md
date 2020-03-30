---
title: Квоты, СКУ и наличие в регионах сервиса Azure Kubernetes (AKS)
description: Узнайте о квотах по умолчанию, ограниченных размерах VM SKU и о наличии в регионах службы Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: 03e7396932f0813ef4bd00d644dcdaddfe229e6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252835"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Квоты, ограничения размера виртуальных машин и доступность региона в службе Azure Kubernetes (AKS)

Все службы Azure устанавливают ограничения по умолчанию и квоты для ресурсов и функций. Некоторые виртуальные машины (VM) SKUs также ограничены для использования.

В этой статье подробно описаны ограничения ресурсов по умолчанию для ресурсов службы Azure Kubernetes (AKS) и наличие AKS в регионах Azure.

## <a name="service-quotas-and-limits"></a>Квоты и ограничения службы

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Подготовленная инфраструктура

Все другие ограничения сети, вычислительных ресурсов и хранилища применяются к подготовленной инфраструктуре. Для соответствующих ограничений [см.](../azure-resource-manager/management/azure-subscription-service-limits.md)

> [!IMPORTANT]
> При обновлении кластера AKS временно расходуются дополнительные ресурсы. Эти ресурсы включают доступные IP-адреса в виртуальной сетевой подсети или квоту виртуальной машины vCPU. Если вы используете контейнеры Windows Server (в настоящее время в предварительном просмотре в AKS), единственным одобренным подходом к применению последних обновлений к узлам является выполнение операции обновления. Неудавшийся процесс обновления кластера может указывать на то, что у вас нет доступного пространства IP-адреса или квоты vCPU для обработки этих временных ресурсов. Для получения дополнительной информации о процессе обновления узлов Windows Server [см.][nodepool-upgrade]

## <a name="restricted-vm-sizes"></a>Ограниченные размеры VM

Каждый узлы в кластере AKS содержит фиксированное количество вычислительных ресурсов, таких как VCPU и память. Если узла AKS содержится недостаточно вычислительных ресурсов, стручки могут не работать правильно. Чтобы убедиться, что необходимые стручки *kube-системы* и ваши приложения могут быть надежно запланированы, **не используйте следующие VM SKUs в AKS:**

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

Для получения дополнительной информации о типах VM и их вычислительных [ресурсах см.][vm-skus]

## <a name="region-availability"></a>Доступность по регионам

Для последнего списка развертывания и запуска кластеров [можно][region-availability]см.

## <a name="next-steps"></a>Дальнейшие действия

Вы можете повысить лимит нескольких ограничений и квот. Если ресурс поддерживает увеличение, запросите увеличение через [запрос поддержки Azure][azure-support] (для **типа проблемы**выберите **квоту).**

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
