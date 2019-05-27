---
author: kumudD
ms.service: load-balancer
ms.topic: include
ms.date: 11/09/2018
ms.author: kumud
ms.openlocfilehash: f0e575af51f952a80fe42102b033727713c75cf9
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66122231"
---
## <a name="configuration-scenario"></a>Скрипт конфигурации

В этом скрипте выполняется создание внутренней подсистемы балансировки нагрузки в виртуальной сети, как показано на следующем изображении:

![Сценарий внутреннего балансировщика нагрузки](./media/load-balancer-get-started-ilb-scenario-include/figure1.png)

Конфигурации для этого сценария выглядят следующим образом:

* Две виртуальные машины **DB1** и **DB2**.
* Конечные точки для внутренней подсистемы балансировки нагрузки.
* Внутренняя подсистема балансировки нагрузки.
