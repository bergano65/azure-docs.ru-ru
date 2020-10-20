---
title: Отрисовка структуры
description: Сведения о том, как выполнить отрисовку контура для выделенного фрагмента.
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 55151ab965b13a944fee5517a2ca8b4f9eb64bb0
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207246"
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

![Объект, отображаемый три раза с различными параметрами структуры ](./media/outlines.png) , влияет на изменение `color` параметра с желтого (левого) на пурпурный (по центру) и `pulseIntensity` от 0 до 0,8 (справа).

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
    ApiHandle<OutlineSettings> outlineSettings = session->Actions()->GetOutlineSettings();
    Color4Ub outlineColor;
    outlineColor.channels = { 255, 255, 0, 255 };
    outlineSettings->SetColor(outlineColor);
    outlineSettings->SetPulseRateHz(2.0f);
    outlineSettings->SetPulseIntensity(0.5f);
}
```

## <a name="performance"></a>Производительность

Отрисовка контура может оказать значительное влияние на производительность отрисовки. Это влияние зависит от соотношения пространства, занимаемого на экране выбранными и невыбранными объектами в определенном кадре.

## <a name="api-documentation"></a>Документирование API

* [C# Ремотеманажер. Аутлинесеттингс, свойство](/dotnet/api/microsoft.azure.remoterendering.remotemanager.outlinesettings)
* [C++ Ремотеманажер:: Аутлинесеттингс ()](/cpp/api/remote-rendering/remotemanager#outlinesettings)

## <a name="next-steps"></a>Дальнейшие действия

* [Компонент переопределения иерархического состояния](../../overview/features/override-hierarchical-state.md)