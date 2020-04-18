---
title: Масштабирование среды - Azure Time Series Insights Документы Майкрософт
description: Узнайте, как масштабировать среду Исследования времени Azure с помощью портала Azure.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/17/2020
ms.custom: seodec18
ms.openlocfilehash: 9604f0c6eeb9d0ac4b7d07f1ab1ba03285fd60fe
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640067"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Как масштабировать среду Azure Time Series Insights

В этой статье описывается, как изменить емкость среды Time Series Insights с помощью [портала Azure.](https://portal.azure.com) Емкость — множитель, применяемый к скорости входящих данных, емкости хранилища и затратам, связанным с выбранным номером SKU.

Портал Azure можно использовать для увеличения или уменьшения емкости в пределах ценовой категории SKU.

Однако изменение ценовой категории SKU запрещено. Например, среду с ценовой категорией SKU S1 нельзя преобразовать в среду S2 или наоборот.

## <a name="ga-limits"></a>Ограничения ГА

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>Изменение емкости среды

1. Найдите и выберите среду Time Series Insights на портале Azure.

1. В меню для среды Time Series Insights выберите **конфигурацию хранения.**

   [![Настройка емкости Time Series Insights](media/scale-your-environment/scale-your-environment-configure.png)](media/scale-your-environment/scale-your-environment-configure.png#lightbox)

1. Настройте ползунок **емкости** соответственно требованиям скорости приема и емкости хранилища. Обратите внимание, что **скорость приема**, **емкость хранилища** и **оценочная стоимость** обновляются динамически, чтобы показать эффект изменения.

   [![Налажить среду с помощью ползунка емкости](media/scale-your-environment/scale-your-environment-slider.png)](media/scale-your-environment/scale-your-environment-slider.png#lightbox)

   Кроме того, можно ввести число множителя емкости в текстовом поле справа от ползунка.

1. Выберите **Сохранить**, чтобы масштабировать среду. Индикатор хода выполнения будет отображаться, пока изменения не зафиксируются.

1. Убедитесь, что новая емкость [достаточна для предотвращения регулирования.](time-series-insights-diagnose-and-solve-problems.md)

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения см. в статье, посвященной [обзору периода удержания данных в службе "Аналитика временных рядов"](time-series-insights-concepts-retention.md).

- Узнайте о [настройке хранения данных в Azure Time Series Insights.](time-series-insights-how-to-configure-retention.md)

- Узнайте о [планировании среды.](time-series-insights-environment-planning.md)
