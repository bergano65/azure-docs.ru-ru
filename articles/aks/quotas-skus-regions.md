---
title: Квоты, SKU и доступность по регионам в службе Azure Kubernetes (AKS)
description: Сведения о квотах по умолчанию, размеров SKU виртуальной Машины ограниченных узлов и доступность в регионах Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: iainfou
ms.openlocfilehash: 8d4ed8f791858747814972bcf16a9672a7f12610
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65901456"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Квоты, ограничения на размер виртуальной машины и доступность по регионам в службе Azure Kubernetes (AKS)

По умолчанию ограничения и квоты для ресурсов и функций, задайте всех служб Azure. Некоторые конфигурации виртуальной машины (VM) также ограничены для использования.

В этой статье подробно ограничениях ресурсов по умолчанию для ресурсов службы Azure Kubernetes (AKS) и доступность AKS в регионах Azure.

## <a name="service-quotas-and-limits"></a>Квоты и ограничения службы

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Подготовленная инфраструктура

Все другие ограничения сети, вычислительных ресурсов и хранилища применяются к подготовленной инфраструктуре. О соответствующих ограничениях см. в разделе [подписки Azure и ограничения службы](../azure-subscription-service-limits.md).

> [!IMPORTANT]
> При обновлении кластера AKS, временно потребляются дополнительные ресурсы. Эти ресурсы включают доступных IP-адресов в подсети виртуальной сети или квоту на виртуальные ЦП виртуальной машины. Если вы используете контейнеры Windows Server (в настоящее время в предварительной версии в AKS), только подтвержденных подход для применения обновлений к узлам — провести операцию обновления. Сбой обновление кластера может означать, что у вас нет доступных IP-адрес адрес места или виртуальный ЦП квоты для обработки этих временные ресурсы. Дополнительные сведения о процессе обновления узла Windows Server см. в разделе [обновить пуле узел в AKS][nodepool-upgrade].

## <a name="restricted-vm-sizes"></a>Ограниченных размеров виртуальных Машин

Каждый узел в кластере AKS содержит фиксированный объем вычислительных ресурсов, такие как виртуальный процессор и память. Если узлом AKS содержит недостаточно вычислительные ресурсы, модули могут не выполняться надлежащим образом. Чтобы убедиться, что необходимая *kube-system* модулей и приложений, надежно можно запланировать, в AKS не используются следующие номера SKU виртуальной Машины:

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

Дополнительные сведения о типах и их вычислительные ресурсы, см. в разделе [размеры виртуальных машин в Azure][vm-skus].

## <a name="region-availability"></a>Доступность в регионах

Обновленный список where можно развернуть и запускайте кластеры, см. в разделе [доступности области AKS][region-availability].

## <a name="next-steps"></a>Дальнейшие действия

Вы можете повысить лимит нескольких ограничений и квот. Если ресурс поддерживает увеличение, запросить увеличение через [запрос на поддержку Azure] [ azure-support] (для **тип проблемы**выберите **квоты** ).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
