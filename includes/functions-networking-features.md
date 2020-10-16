---
ms.openlocfilehash: 8d5c3fff2646536ffe5c593e13ccdab23397d44b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "84906734"
---


| Компонент |[План потребления](../articles/azure-functions/functions-scale.md#consumption-plan)|[План категории "Премиум"](../articles/azure-functions/functions-scale.md#premium-plan)|[Выделенный план](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Ограничения на входящие IP-адреса и частный доступ к сайту](../articles/azure-functions/functions-networking-options.md#inbound-ip-restrictions)|✅Да|✅Да|✅Да|✅Да|✅Да|
|[Интеграция с виртуальной сетью](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌Нет|✅Да (Региональный)|✅Да (Региональный и шлюз)|✅Да| ✅Да|
|[Триггеры виртуальной сети (без HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌Нет| ✅Да |✅Да|✅Да|✅Да|
|[Гибридные подключения](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (только для Windows)|❌Нет|✅Да|✅Да|✅Да|✅Да|
|[Ограничения исходящих IP-адресов](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌Нет| ✅Да|✅Да|✅Да|✅Да|