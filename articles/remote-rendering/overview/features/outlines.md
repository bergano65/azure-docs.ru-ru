---
title: Визуализация контуров
description: Объясняет, как сделать визуализацию контура выделения
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 8b52dbe8cd12e51c42677ce37acbd57ad551ec50
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680834"
---
# <a name="outline-rendering"></a>Визуализация контуров

Выбранные объекты можно выделить визуально, добавив визуализацию контура через [компонент переопределения иерархического состояния.](../../overview/features/override-hierarchical-state.md) В этой главе объясняется, как изменяются глобальные параметры набросков с помощью API клиента.

Свойства контуров являются глобальным параметром. Все объекты, которые используют визуализацию контуров, будут использовать один и тот же параметр - невозможно использовать цвет контура на объекте.

## <a name="parameters-for-outlinesettings"></a>Параметры для`OutlineSettings`

Класс `OutlineSettings` содержит параметры, связанные с глобальными свойствами контуров. В нем разоблачают следующих членов:

| Параметр      | Тип    | Описание                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Color4Ub | Цвет, который используется для рисования контура. Альфа-часть игнорируется.         |
| `PulseRateHz`    | FLOAT   | Скорость, с которой контур колеблется в секунду|
| `PulseIntensity` | FLOAT   | Интенсивность импульсного эффекта контура. Должно быть между 0.0 для не пульсируя и 1.0 для полного пульсируя. Интенсивность неявно устанавливает минимальную непрозрачность `MinOpacity = 1.0 - PulseIntensity`контура как . |

![Очертания](./media/outlines.png) Эффект изменения `color` параметра с желтого (слева) на `pulseIntensity` пурпурный (в центре) и от 0 до 0,8 (справа).

## <a name="example"></a>Пример

В следующем коде показан пример для настройки параметров контура через API:

``` cs
void SetOutlineParameters(AzureSession session)
{
    OutlineSettings outlineSettings = session.Actions.OutlineSettings;
    outlineSettings.Color = new Color4Ub(255, 255, 0, 255);
    outlineSettings.PulseRateHz = 2.0f;
    outlineSettings.PulseIntensity = 0.5f;
}
```

## <a name="performance"></a>Производительность

Визуализация контуров может оказать значительное влияние на производительность рендеринга. Это воздействие зависит от пространственного отношения экрана и пространства между выбранными и невыбранными объектами для данного кадра.

## <a name="next-steps"></a>Дальнейшие действия

* [Компонент переопределения иерархического состояния](../../overview/features/override-hierarchical-state.md)
