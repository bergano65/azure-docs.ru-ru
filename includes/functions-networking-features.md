---
ms.openlocfilehash: 82571d1a0e651f638dec29184f0ecdc88562b3ad
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578847"
---


| Функция |[План потребления](../articles/azure-functions/functions-scale.md#consumption-plan)|[План категории "Премиум"](../articles/azure-functions/functions-scale.md#premium-plan)|[План ценовой категории "Выделенный"](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Ограничения на входящие IP-адреса и частный доступ к сайту](../articles/azure-functions/functions-networking-options.md#inbound-access-restrictions)|✅Да|✅Да|✅Да|✅Да|✅Да|
|[Интеграция с виртуальной сетью](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌Нет|✅Да (Региональный)|✅Да (Региональный и шлюз)|✅Да| ✅Да|
|[Триггеры виртуальной сети (без HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌Нет| ✅Да |✅Да|✅Да|✅Да|
|[Гибридные подключения](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (только для Windows)|❌Нет|✅Да|✅Да|✅Да|✅Да|
|[Ограничения исходящих IP-адресов](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌Нет| ✅Да|✅Да|✅Да|✅Да|