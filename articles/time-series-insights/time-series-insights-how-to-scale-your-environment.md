---
title: Как масштабировать среду Azure Time Series Insights | Документация Майкрософт
description: В этом руководстве описано масштабирование среды Azure Time Series Insights. Используйте портал Azure для добавления или вычитания ресурсов в ценовой категории SKU.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/27/2019
ms.custom: seodec18
ms.openlocfilehash: 13fa2b892013cf4a3fb96220c901030c6b0aee0b
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129116"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Как масштабировать среду Azure Time Series Insights

В этой статье описывается, как изменять емкость среды Time Series Insights с помощью портала Azure. Емкость — множитель, применяемый к скорости входящих данных, емкости хранилища и затратам, связанным с выбранным номером SKU.

Портал Azure можно использовать для увеличения или уменьшения емкости в пределах ценовой категории SKU.

Однако изменение ценовой категории SKU запрещено. Например, среду с ценовой категорией SKU S1 нельзя преобразовать в среду S2 или наоборот.

## <a name="s1-sku-ingress-rates-and-capacities"></a>Скорость приема и емкость номера SKU S1

| Емкость номера SKU S1 | Скорость приема | Максимальная емкость хранилища
| --- | --- | --- |
| 1 | 1 ГБ (1 млн событий) | 30 ГБ в месяц (30 млн событий) |
| 10 | 10 ГБ (10 млн событий) | 300 ГБ в месяц (300 млн событий) |

## <a name="s2-sku-ingress-rates-and-capacities"></a>Скорость приема и емкость номера SKU S2

| Емкость номера SKU S2 | Скорость приема | Максимальная емкость хранилища
| --- | --- | --- |
| 1 | 10 ГБ (10 млн событий) | 300 ГБ в месяц (300 млн событий) |
| 10 | 100 ГБ (100 млн событий) | 3 ТБ в месяц (3 млрд событий) |

Емкость масштабируется линейно, поэтому номер SKU S1 с емкостью 2 поддерживает скорость приема 2 ГБ (2 млн событий) в неделю и 60 ГБ (60 млн событий) в месяц.

## <a name="change-the-capacity-of-your-environment"></a>Изменение емкости среды

1. Найдите и выберите среду Time Series Insights на портале Azure.

1. В меню среды службы "Аналитика временных рядов" выберите **Настроить**.

   [![Настройка. png](media/scale-your-environment/configure.png)](media/scale-your-environment/configure.png#lightbox)

1. Настройте ползунок **емкости** соответственно требованиям скорости приема и емкости хранилища. Обратите внимание, что **скорость приема**, **емкость хранилища** и **оценочная стоимость** обновляются динамически, чтобы показать эффект изменения.

   [![Группу](media/scale-your-environment/slider.png)](media/scale-your-environment/slider.png#lightbox)

   Кроме того, можно ввести число множителя емкости в текстовом поле справа от ползунка.

1. Выберите **Сохранить**, чтобы масштабировать среду. Индикатор хода выполнения будет отображаться, пока изменения не зафиксируются.

## <a name="next-steps"></a>Следующие шаги

- Убедитесь, что Новая емкость [достаточна для предотвращения регулирования](time-series-insights-diagnose-and-solve-problems.md).