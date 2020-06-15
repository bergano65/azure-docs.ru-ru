---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 5e0cff7bde6e80a776d694820ca7b69dafa7c0d9
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648817"
---
Закрытый доступ сайту обеспечивает доступность приложения только из частной сети, например из виртуальной сети Azure.

* Закрытый доступ сайту предоставляется в планах [Премиум](../articles/azure-functions/functions-premium-plan.md), [Потребление](../articles/azure-functions/functions-scale.md#consumption-plan) и [Служба приложений](../articles/azure-functions/functions-scale.md#app-service-plan) при настройке конечных точек службы.
    * Конечные точки службы можно настроить отдельно для каждого приложения, выбрав **Функции платформы** > **Сети** > **Настройка ограничений доступа** > **Добавить правило**. Теперь в качестве типа правила можно выбрать виртуальные сети.
    * Дополнительные сведения см. в статье [Конечные точки служб для виртуальной сети](../articles/virtual-network/virtual-network-service-endpoints-overview.md).
    * Помните, что при использовании с конечными точками службы функция по-прежнему имеет полный исходящий доступ к Интернету, даже если настроена интеграция с виртуальной сетью.
* Закрытый доступ сайту также можно использовать в Среде службы приложений, для которой настроена внутренняя подсистема балансировки нагрузки (ILB). Дополнительные сведения см. в статье [Создание и использование внутренней подсистемы балансировки нагрузки с использованием среды службы приложений](../articles/app-service/environment/create-ilb-ase.md).

Узнайте, как настроить закрытый доступ сайту, в разделе [Руководство по установке закрытого доступа к сайту для Функций Azure](../articles/azure-functions/functions-create-private-site-access.md).