---
title: Отладка отрисовки
description: Общие сведения об отладке отрисовки на стороне сервера
author: jumeder
ms.author: jumeder
ms.date: 06/15/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 9ee6945cec4c2441334e665947568743aaf29226
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2020
ms.locfileid: "89013594"
---
# <a name="debug-rendering"></a>Отладка отрисовки

API отладки отрисовки предоставляет ряд глобальных параметров для изменения отрисовки на стороне сервера с помощью различных эффектов отладки.

## <a name="available-debug-rendering-effects"></a>Доступные эффекты отладки отрисовки

|Параметр                          | Действие                               |
|---------------------------------|:-------------------------------------|
|Счетчик кадров                    | Отображает наложенный текст в левом верхнем углу кадра. Текст содержит текущий идентификатор кадра на стороне сервера, который постоянно увеличивается по мере отрисовки. |
|Число многоугольников                    | Отображает наложенный текст в левом верхнем углу кадра. Текст показывает число отображаемых в настоящий момент многоугольников. Это же значение можно получить с помощью [запросов данных производительности на стороне сервера](performance-queries.md).| 
|Каркасный режим                        | Если этот эффект включен, все геометрические объекты, загруженные на сервер, будут отрисованы в каркасном режиме. В этом режиме выполняется растрирование только граней многоугольников. |

Приведенный ниже код включает эти эффекты отладки.

```cs
void EnableDebugRenderingEffects(AzureSession session, bool highlight)
{
    DebugRenderingSettings settings = session.Actions.DebugRenderingSettings;

    // Enable frame counter text overlay on the server side rendering
    settings.RenderFrameCount = true;

    // Enable polygon count text overlay on the server side rendering
    settings.RenderPolygonCount = true;

    // Enable wireframe rendering of object geometry on the server
    settings.RenderWireframe = true;
}
```

```cpp
void EnableDebugRenderingEffects(ApiHandle<AzureSession> session, bool highlight)
{
    ApiHandle<DebugRenderingSettings> settings = session->Actions()->GetDebugRenderingSettings();

    // Enable frame counter text overlay on the server side rendering
    settings->SetRenderFrameCount(true);

    // Enable polygon count text overlay on the server side rendering
    settings->SetRenderPolygonCount(true);

    // Enable wireframe rendering of object geometry on the server
    settings->SetRenderWireframe(true);
}
```

![Отладка отрисовки](./media/debug-rendering.png)

> [!NOTE]
> Все эффекты отладки отрисовки — это глобальные параметры, влияющие на весь кадр.

## <a name="use-cases"></a>Варианты использования

API отладки отрисовки предназначен для выполнения простых задач отладки, таких как проверка правильности работы подключения службы. Параметры отрисовки текста непосредственно влияют на нисходящие видеокадры. Включив их, можно проверить правильность получения и декодирования видео новых кадров.

Однако предоставленные эффекты не дают возможности подробно изучить работоспособность службы. Для этого рекомендуется использовать [запросы данных производительности на стороне сервера](performance-queries.md).

## <a name="performance-considerations"></a>Вопросы производительности

* Включение наложения текста практически не отражается на производительности.
* Включение каркасного режима приводит к нетривиальным затратам на производительность, которые зависят от конкретной сцены. В сложных сценах этот режим может привести к снижению частоты кадров ниже целевого значения в 60 Гц.

## <a name="next-steps"></a>Дальнейшие действия

* [Режимы отрисовки](../../concepts/rendering-modes.md)
* [Запросы данных производительности на стороне сервера](performance-queries.md)
