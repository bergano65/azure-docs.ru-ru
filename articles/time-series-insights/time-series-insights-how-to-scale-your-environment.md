---
title: Как масштабировать среду в Azure Time Series Insights | Документация Майкрософт
description: Узнайте, как масштабировать среду службы "аналитика временных рядов Azure" с помощью портал Azure.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: b17cdb2ec27676d5d20d6f12bad309368fe32aa3
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74006803"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Как масштабировать среду Azure Time Series Insights

В этой статье описывается, как изменить емкость среды "аналитика временных рядов" с помощью [портал Azure](https://portal.azure.com). Емкость — множитель, применяемый к скорости входящих данных, емкости хранилища и затратам, связанным с выбранным номером SKU.

Портал Azure можно использовать для увеличения или уменьшения емкости в пределах ценовой категории SKU.

Однако изменение ценовой категории SKU запрещено. Например, среду с ценовой категорией SKU S1 нельзя преобразовать в среду S2 или наоборот.

## <a name="ga-limits"></a>Ограничения в общедоступной версии

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>Изменение емкости среды

1. Найдите и выберите среду Time Series Insights на портале Azure.

1. В меню среды службы "Аналитика временных рядов" выберите **Настроить**.

   [![configure. png](media/scale-your-environment/configure.png)](media/scale-your-environment/configure.png#lightbox)

1. Настройте ползунок **емкости** соответственно требованиям скорости приема и емкости хранилища. Обратите внимание, что **скорость приема**, **емкость хранилища** и **оценочная стоимость** обновляются динамически, чтобы показать эффект изменения.

   [Ползунок ![](media/scale-your-environment/slider.png)](media/scale-your-environment/slider.png#lightbox)

   Кроме того, можно ввести число множителя емкости в текстовом поле справа от ползунка.

1. Выберите **Сохранить**, чтобы масштабировать среду. Индикатор хода выполнения будет отображаться, пока изменения не зафиксируются.

1. Убедитесь, что Новая емкость [достаточна для предотвращения регулирования](time-series-insights-diagnose-and-solve-problems.md).

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения см. в статье, посвященной [обзору периода удержания данных в службе "Аналитика временных рядов"](time-series-insights-concepts-retention.md).

- Узнайте, как [настроить хранение данных в службе "аналитика временных рядов Azure](time-series-insights-how-to-configure-retention.md)".

- Узнайте о [планировании среды](time-series-insights-environment-planning.md).