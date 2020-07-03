---
title: Отрисовка структуры
description: Объясняется, как выполнить отрисовку структуры выбора.
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 8b52dbe8cd12e51c42677ce37acbd57ad551ec50
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680834"
---
# <a name="outline-rendering"></a>Отрисовка структуры

Выбранные объекты могут быть выделены визуально путем добавления отрисовки структуры с помощью [компонента переопределения иерархического состояния](../../overview/features/override-hierarchical-state.md). В этой главе объясняется, как изменяются глобальные параметры отрисовки структуры с помощью API клиента.

Свойства структуры являются глобальными настройками. Все объекты, использующие отрисовку структуры, будут использовать один и тот же параметр. цвет контура для каждого объекта использовать невозможно.

## <a name="parameters-for-outlinesettings"></a>Параметры для`OutlineSettings`

Класс `OutlineSettings` содержит параметры, относящиеся к глобальным свойствам структуры. Он предоставляет следующие члены:

| Параметр      | Тип    | Описание                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Color4Ub | Цвет, используемый для рисования контура. Альфа-часть игнорируется.         |
| `PulseRateHz`    | FLOAT   | Скорость, с которой осЦиллатес структура в секунду|
| `PulseIntensity` | FLOAT   | Интенсивность импульсного воздействия. Должно быть в диапазоне 0,0 для пулсинг и 1,0 для полного пулсинг. Интенсивность неявно устанавливает минимальную прозрачность структуры как `MinOpacity = 1.0 - PulseIntensity`. |

![Описывает](./media/outlines.png) результат изменения `color` параметра с желтого (левого) на пурпурный (по центру) и `pulseIntensity` от 0 до 0,8 (справа).

## <a name="example"></a>Пример

В следующем коде показан пример установки параметров структуры через API:

``` cs
void SetOutlineParameters(AzureSession session)
{
    OutlineSettings outlineSettings = session.Actions.OutlineSettings;
    outlineSettings.Color = new Color4Ub(255, 255, 0, 255);
    outlineSettings.PulseRateHz = 2.0f;
    outlineSettings.PulseIntensity = 0.5f;
}
```

## <a name="performance"></a>Тестирование

Отрисовка структуры может оказать значительное влияние на производительность отрисовки. Это влияние зависит от пространственного отношения пространства на экране между выбранными и невыбранными объектами для данного кадра.

## <a name="next-steps"></a>Дальнейшие шаги

* [Компонент переопределения иерархического состояния](../../overview/features/override-hierarchical-state.md)
