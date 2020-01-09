---
title: Квоты, номера SKU и доступность регионов в службе Azure Kubernetes (AKS)
description: Сведения о квотах по умолчанию, размерах SKU виртуальных машин с ограниченным доступом к узлу и доступности региона службы Azure Kubernetes (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: mlearned
ms.openlocfilehash: a8b561780e2e81499d211252648aeef86561cb9b
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2020
ms.locfileid: "75658514"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Квоты, ограничения размера виртуальной машины и доступность региона в службе Kubernetes Azure (AKS)

Все службы Azure устанавливают ограничения по умолчанию и квоты для ресурсов и компонентов. Некоторые номера SKU виртуальной машины также ограничены для использования.

В этой статье описаны ограничения ресурсов по умолчанию для ресурсов Azure Kubernetes Service (AKS) и доступность AKS в регионах Azure.

## <a name="service-quotas-and-limits"></a>Квоты и ограничения службы

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Подготовленная инфраструктура

Все другие ограничения сети, вычислительных ресурсов и хранилища применяются к подготовленной инфраструктуре. Соответствующие ограничения см. в статье [Подписка Azure и ограничения службы](../azure-resource-manager/management/azure-subscription-service-limits.md).

> [!IMPORTANT]
> При обновлении кластера AKS дополнительные ресурсы временно потребляются. Эти ресурсы включают доступные IP-адреса в подсети виртуальной сети или квоту виртуальной машины виртуальных ЦП. При использовании контейнеров Windows Server (в настоящее время в предварительной версии в AKS) единственным одобренным подходом к применению последних обновлений к узлам является выполнение операции обновления. Сбой процесса обновления кластера может означать, что у вас нет доступного пространства IP-адресов или квоты виртуальных ЦП для обработки этих временных ресурсов. Дополнительные сведения о процессе обновления узла Windows Server см. в разделе [Обновление пула узлов в AKS][nodepool-upgrade].

## <a name="restricted-vm-sizes"></a>Ограниченные размеры виртуальных машин

Каждый узел в кластере AKS содержит фиксированный объем ресурсов вычислений, таких как виртуальных ЦП и память. Если узел AKS содержит недостаточные ресурсы вычислений, то для правильной работы модулей Pod может произойти сбой. Чтобы обеспечить надежное планирование необходимых модулей *KUBE-System* и приложений, не используйте следующие номера SKU виртуальных машин в AKS:

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

Дополнительные сведения о типах ВМ и их ресурсах вычислений см. [в статье размеры виртуальных машин в Azure][vm-skus].

## <a name="region-availability"></a>Доступность по регионам

Последнюю версию списка, в котором можно развернуть и запустить кластеры, см. в статье о [доступности AKS Region][region-availability].

## <a name="next-steps"></a>Дальнейшие действия

Вы можете повысить лимит нескольких ограничений и квот. Если ресурс поддерживает увеличение, запросите увеличение через запрос на [поддержку Azure][azure-support] (для **типа проблемы**выберите **Квота**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
