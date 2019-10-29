---
title: Как масштабировать среду Azure Time Series Insights | Документация Майкрософт
description: В этом руководстве описано масштабирование среды Azure Time Series Insights. Используйте портал Azure для добавления или вычитания ресурсов в ценовой категории SKU.
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
ms.openlocfilehash: 3f03f5ed75c720c9b0daf30d721ef4d2aee9749c
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991160"
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

   [Ползунок![](media/scale-your-environment/slider.png)](media/scale-your-environment/slider.png#lightbox)

   Кроме того, можно ввести число множителя емкости в текстовом поле справа от ползунка.

1. Выберите **Сохранить**, чтобы масштабировать среду. Индикатор хода выполнения будет отображаться, пока изменения не зафиксируются.

1. Убедитесь, что Новая емкость [достаточна для предотвращения регулирования](time-series-insights-diagnose-and-solve-problems.md).

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения см. в статье, посвященной [обзору периода удержания данных в службе "Аналитика временных рядов"](time-series-insights-concepts-retention.md).

- Узнайте, как [настроить хранение данных в службе "аналитика временных рядов Azure](time-series-insights-how-to-configure-retention.md)".

- Узнайте о [планировании среды](time-series-insights-environment-planning.md).