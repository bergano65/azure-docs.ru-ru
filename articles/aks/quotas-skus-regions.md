---
title: Квоты, SKU и доступность по регионам в службе Azure Kubernetes (AKS)
description: Сведения о квотах по умолчанию, размеров SKU виртуальной Машины ограниченных узлов и доступность в регионах Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: iainfou
ms.openlocfilehash: abeb9ef6e467b62cf7332e01e1b77c710b9ba4f4
ms.sourcegitcommit: 524625dd12e0537173616a991802075e2dc9da12
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2019
ms.locfileid: "64413036"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Квоты, ограничения на размер виртуальной машины и доступность по регионам в службе Azure Kubernetes (AKS)

Все службы Azure включают несколько стандартных ограничений и квот для ресурсов и функций. Размер узла, определенной виртуальной машине (VM) номера SKU: выберите использование ограничено.

В этой статье подробно ограничениях ресурсов по умолчанию для ресурсов службы Azure Kubernetes (AKS), а также о доступности службы AKS в регионах Azure.

## <a name="service-quotas-and-limits"></a>Квоты и ограничения службы

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Подготовленная инфраструктура

Все другие ограничения сети, вычислительных ресурсов и хранилища применяются к подготовленной инфраструктуре. Сведения о соответствующих ограничениях см. в статье о [подписке Azure и ограничениях службы](../azure-subscription-service-limits.md).

## <a name="restricted-vm-sizes"></a>Ограниченных размеров виртуальных Машин

Каждый узел в кластере AKS содержит фиксированный объем вычислительных ресурсов, такие как виртуальный процессор и память. Если узлом AKS содержит недостаточно вычислительные ресурсы, модули могут не выполняться надлежащим образом. Чтобы убедиться, что необходимая *kube-system* модулей и приложений можно надежно запланировать, следующие номера SKU виртуальной Машины не может использоваться в AKS:

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

Дополнительные сведения о типах и их вычислительные ресурсы, см. в разделе [размеры виртуальных машин в Azure][vm-skus].

## <a name="region-availability"></a>Регионы доступности

Обновленный список where можно развернуть и запускайте кластеры, см. в разделе [доступности области AKS][region-availability].

## <a name="next-steps"></a>Дальнейшие действия

Вы можете повысить лимит нескольких ограничений и квот. Чтобы запросить увеличение квоты одного или нескольких ресурсов, которые поддерживают такую возможность, отправьте [запрос в службу поддержки Azure][azure-support] (в качестве **типа проблемы** укажите "Квота").

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
