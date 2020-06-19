---
title: Отрисовка структуры
description: Сведения о том, как выполнить отрисовку контура для выделенного фрагмента.
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 4f3889a0ba121cb9a3167c1f6ac95f0bed280539
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83759019"
---
# <a name="outline-rendering"></a>Отрисовка структуры

Выбранные объекты можно выделять визуально, добавляя к ним отрисовку контура с помощью [компонента переопределения иерархического состояния](../../overview/features/override-hierarchical-state.md). В этой главе объясняется, как с помощью клиентского API изменить глобальные параметры для отрисовки контура.

Параметры контура являются глобальными. Все объекты, использующие отрисовку контура, будут использовать одни и те же параметры, например невозможно задать отдельный цвет контура для каждого объекта.

## <a name="parameters-for-outlinesettings"></a>Параметры `OutlineSettings`

Класс `OutlineSettings` содержит параметры, относящиеся к глобальным свойствам контура. Он предоставляет следующие элементы.

| Параметр      | Тип    | Описание                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Color4Ub | Цвет, используемый для рисования контура. Часть, обозначающая альфа-канал, игнорируется.         |
| `PulseRateHz`    | FLOAT   | Скорость колебания контура в секунду.|
| `PulseIntensity` | FLOAT   | Интенсивность импульсов контура. Допускаются значения в диапазоне от 0,0 (нет пульсации) и 1,0 (полный диапазон пульсации). Интенсивность неявным образом задает минимальную прозрачность контура (`MinOpacity = 1.0 - PulseIntensity`). |

![Контуры](./media/outlines.png) Пример изменения параметров: цвет (`color`) желтый (слева) стал пурпурным (по центру), а пульсация (`pulseIntensity`) увеличена с 0 до 0,8 (справа).

## <a name="example"></a>Пример

В следующем коде показан пример настройки параметров контура через API:

```cs
void SetOutlineParameters(AzureSession session)
{
    OutlineSettings outlineSettings = session.Actions.OutlineSettings;
    outlineSettings.Color = new Color4Ub(255, 255, 0, 255);
    outlineSettings.PulseRateHz = 2.0f;
    outlineSettings.PulseIntensity = 0.5f;
}
```

```cpp
void SetOutlineParameters(ApiHandle<AzureSession> session)
{
    ApiHandle<OutlineSettings> outlineSettings = *session->Actions()->OutlineSettings();
    Color4Ub outlineColor;
    outlineColor.channels = { 255, 255, 0, 255 };
    outlineSettings->Color(outlineColor);
    outlineSettings->PulseRateHz(2.0f);
    outlineSettings->PulseIntensity(0.5f);
}
```

## <a name="performance"></a>Производительность

Отрисовка контура может оказать значительное влияние на производительность отрисовки. Это влияние зависит от соотношения пространства, занимаемого на экране выбранными и невыбранными объектами в определенном кадре.

## <a name="next-steps"></a>Дальнейшие действия

* [Компонент переопределения иерархического состояния](../../overview/features/override-hierarchical-state.md)
