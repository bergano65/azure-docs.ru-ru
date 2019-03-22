---
title: Квоты службы Azure Kubernetes (AKS) и ее доступность в регионах
description: Квоты по умолчанию и доступность в регионах для службы Azure Kubernetes (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: iainfou
ms.openlocfilehash: ef1ecf4419733e908445f9cf4fe47797d430433f
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337465"
---
# <a name="quotas-and-region-availability-for-azure-kubernetes-service-aks"></a>Квоты и доступность в регионах для службы Azure Kubernetes (AKS)

Все службы Azure включают несколько стандартных ограничений и квот для ресурсов и функций. В разделах ниже содержится подробная информация об ограничениях по умолчанию для нескольких ресурсов службы Azure Kubernetes (AKS), а также сведения о доступности службы AKS в регионах Azure.

## <a name="service-quotas-and-limits"></a>Квоты и ограничения службы

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Подготовленная инфраструктура

Все другие ограничения сети, вычислительных ресурсов и хранилища применяются к подготовленной инфраструктуре. Сведения о соответствующих ограничениях см. в статье о [подписке Azure и ограничениях службы](../azure-subscription-service-limits.md).

## <a name="region-availability"></a>Регионы доступности

Служба Azure Kubernetes (AKS) доступна в следующих регионах:

- Восточная часть Австралии
- Юго-Восточная часть Австралии
- Центральная Канада
- Восточная Канада
- Центральная Индия
- Центральный регион США
- Восточная Азия
- Восточная часть США
- Восток США 2
- Центральная Франция
- Восточная часть Японии
- Северная Европа
- Юго-Восточная Азия
- Южная Индия
- Южная часть Великобритании
- Западная часть Великобритании
- Западная Европа
- Запад США
- Западный регион США 2

## <a name="next-steps"></a>Дальнейшие действия

Вы можете повысить лимит нескольких ограничений и квот. Чтобы запросить увеличение квоты одного или нескольких ресурсов, которые поддерживают такую возможность, отправьте [запрос в службу поддержки Azure][azure-support] (в качестве **типа проблемы** укажите "Квота").

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
