---
title: Односторонняя отрисовка
description: Сведения об односторонней отрисовке и сценариях ее использования
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 97e0456e274adee7d678e373cfd92b5003f3d801
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83759104"
---
# <a name="single-sided-rendering"></a>Односторонняя отрисовка

Основная часть отрисовщиков использует [исключение задних поверхностей](https://en.wikipedia.org/wiki/Back-face_culling) для повышения производительности. Но в тех случаях, когда в сетках создается вырез [плоскостью сечения](cut-planes.md), пользователи увидят именно заднюю сторону треугольников. Если эти треугольники исключены, результат не выглядит убедительным.

Чтобы гарантированно избежать такой проблемы, можно применять *двустороннюю* отрисовку треугольников. Так как отказ от исключения задних поверхностей ухудшает производительность, служба "Удаленная отрисовка Azure" по умолчанию включает двустороннюю отрисовку только для тех сеток, которые пересекаются с плоскостью сечения.

Параметр *односторонней отрисовки* позволяет изменить это поведение.

> [!CAUTION]
> Односторонняя отрисовка в настоящее время считается экспериментальной функцией. Возможно, она будет позднее удалена. Не изменяйте значение по умолчанию, если это не требуется для решения критически важной проблемы в приложении.

## <a name="prerequisites"></a>Предварительные требования

Параметр односторонней отрисовки влияет только на те сетки, которые были [преобразованы](../../how-tos/conversion/configure-model-conversion.md) с указанием значения `SingleSided` для параметра `opaqueMaterialDefaultSidedness`. По умолчанию этот параметр имеет значение `DoubleSided`.

## <a name="single-sided-rendering-setting"></a>Параметр односторонней отрисовки

Поддерживаются три разных режима.

**Normal.** В этом режиме сетки всегда отрисовываются так, как были преобразованы. Это означает, что сетки, для которых параметр `opaqueMaterialDefaultSidedness` имел при преобразовании значение `SingleSided`, всегда будут отображаться с исключением задних поверхностей, даже при пересечении с плоскостью сечения.

**DynamicDoubleSiding.** В этом режиме двусторонняя отрисовка автоматически включается для сетки, когда та пересекается с плоскостью сечения. Этот режим используется по умолчанию.

**AlwaysDoubleSided.** Принудительно применяет двустороннюю отрисовку ко всем односторонним геометрическим схемам. Этот режим предоставляется в основном для того, чтобы сравнить производительность при односторонней и двусторонней отрисовке.

Изменить параметры односторонней отрисовки можно следующим образом:

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
    ApiHandle<SingleSidedSettings> settings = *session->Actions()->SingleSidedSettings();

    // Single-sided geometry is rendered as is
    settings->Mode(SingleSidedMode::Normal);

    // Single-sided geometry is always rendered double-sided
    settings->Mode(SingleSidedMode::AlwaysDoubleSided);
}
```

## <a name="next-steps"></a>Дальнейшие действия

* [Плоскости сечения](cut-planes.md)
* [Настройка преобразования модели](../../how-tos/conversion/configure-model-conversion.md)
