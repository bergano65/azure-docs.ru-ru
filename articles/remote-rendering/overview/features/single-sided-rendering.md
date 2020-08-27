---
title: Односторонняя отрисовка
description: Сведения об односторонней отрисовке и сценариях ее использования
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: aff636adff48a8882c152eab398a96a8d28f84e0
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88892751"
---
# <a name="no-loc-textsingle-sided-rendering"></a>:::no-loc text="Single-sided"::: отчета

Основная часть отрисовщиков использует [исключение задних поверхностей](https://en.wikipedia.org/wiki/Back-face_culling) для повышения производительности. Но в тех случаях, когда в сетках создается вырез [плоскостью сечения](cut-planes.md), пользователи увидят именно заднюю сторону треугольников. Если эти треугольники исключены, результат не выглядит убедительным.

Чтобы гарантированно избежать такой проблемы, можно применять *двустороннюю* отрисовку треугольников. Так как отказ от исключения задних поверхностей ухудшает производительность, служба "Удаленная отрисовка Azure" по умолчанию включает двустороннюю отрисовку только для тех сеток, которые пересекаются с плоскостью сечения.

Параметр * :::no-loc text="single-sided"::: рендеринга* позволяет настроить это поведение.

> [!CAUTION]
> :::no-loc text="single-sided":::Параметр подготовки к просмотру является экспериментальной функцией. Возможно, она будет позднее удалена. Не изменяйте значение по умолчанию, если это не требуется для решения критически важной проблемы в приложении.

## <a name="prerequisites"></a>Предварительные требования

:::no-loc text="single-sided":::Параметр отрисовки влияет только на сетки, [преобразованные](../../how-tos/conversion/configure-model-conversion.md) с `opaqueMaterialDefaultSidedness` параметром в значение `SingleSided` . По умолчанию этот параметр имеет значение `DoubleSided`.

## <a name="no-loc-textsingle-sided-rendering-setting"></a>:::no-loc text="Single-sided"::: параметр подготовки к просмотру

Поддерживаются три разных режима.

**Normal.** В этом режиме сетки всегда отрисовываются так, как были преобразованы. Это означает, что сетки, для которых параметр `opaqueMaterialDefaultSidedness` имел при преобразовании значение `SingleSided`, всегда будут отображаться с исключением задних поверхностей, даже при пересечении с плоскостью сечения.

**DynamicDoubleSiding.** В этом режиме двусторонняя отрисовка автоматически включается для сетки, когда та пересекается с плоскостью сечения. Этот режим используется по умолчанию.

**AlwaysDoubleSided.** Принудительно применяет двустороннюю отрисовку ко всем односторонним геометрическим схемам. Этот режим в основном предоставляется, поэтому можно легко сравнивать влияние :::no-loc text="single-sided"::: и производительность :::no-loc text="double-sided"::: визуализации.

Изменение :::no-loc text="single-sided"::: параметров подготовки к просмотру можно выполнить следующим образом.

```cs
void ChangeSingleSidedRendering(AzureSession session)
{
    SingleSidedSettings settings = session.Actions.SingleSidedSettings;

    // Single-sided geometry is rendered as is
    settings.Mode = SingleSidedMode.Normal;

    // Single-sided geometry is always rendered double-sided
    settings.Mode = SingleSidedMode.AlwaysDoubleSided;
}
```

```cpp
void ChangeSingleSidedRendering(ApiHandle<AzureSession> session)
{
    ApiHandle<SingleSidedSettings> settings = session->Actions()->GetSingleSidedSettings();

    // Single-sided geometry is rendered as is
    settings->SetMode(SingleSidedMode::Normal);

    // Single-sided geometry is always rendered double-sided
    settings->SetMode(SingleSidedMode::AlwaysDoubleSided);
}
```

## <a name="next-steps"></a>Дальнейшие действия

* [Плоскости сечения](cut-planes.md)
* [Настройка преобразования модели](../../how-tos/conversion/configure-model-conversion.md)
