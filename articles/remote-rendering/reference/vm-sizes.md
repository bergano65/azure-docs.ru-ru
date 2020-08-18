---
title: Размеры сервера
description: Описание отдельных размеров сервера, которые могут быть выделены
author: florianborn71
ms.author: flborn
ms.date: 05/28/2020
ms.topic: reference
ms.openlocfilehash: b9479c2ab5b63440a03bd74d2503930108a49091
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88511191"
---
# <a name="server-sizes"></a>Размеры сервера

Удаленная визуализация Azure доступна в двух конфигурациях сервера: `Standard` и `Premium` .

## <a name="polygon-limits"></a>Пределы многоугольников

Для удаленной отрисовки с помощью `Standard` size Server предусмотрен максимальный размер сцены в 20 000 000 многоугольников. Удаленная Визуализация с `Premium` размером не обеспечивает жесткое ограничение, но производительность может снизиться, если содержимое превышает возможности отрисовки службы.

Когда модуль подготовки к на на сервере "Стандартный" достигает этого ограничения, он переключает отрисовку в фон шахматной доски:

![Шахматная доска](media/checkerboard.png)

## <a name="specify-the-server-size"></a>Укажите размер сервера

Требуемый тип конфигурации сервера должен быть указан во время инициализации сеанса отрисовки. Его нельзя изменить в работающем сеансе. В следующих примерах кода показано место, где должен быть указан размер сервера:

```cs
async void CreateRenderingSession(AzureFrontend frontend)
{
    RenderingSessionCreationParams sessionCreationParams = new RenderingSessionCreationParams();
    sessionCreationParams.Size = RenderingSessionVmSize.Standard; // or  RenderingSessionVmSize.Premium

    AzureSession session = await frontend.CreateNewRenderingSessionAsync(sessionCreationParams).AsTask();
}
```

```cpp
void CreateRenderingSession(ApiHandle<AzureFrontend> frontend)
{
    RenderingSessionCreationParams sessionCreationParams;
    sessionCreationParams.Size = RenderingSessionVmSize::Standard; // or  RenderingSessionVmSize::Premium

    if (auto createSessionAsync = frontend->CreateNewRenderingSessionAsync(sessionCreationParams))
    {
        // ...
    }
}
```

В [примерах сценариев PowerShell](../samples/powershell-example-scripts.md)требуемый размер сервера должен быть указан в `arrconfig.json` файле:

```json
{
  "accountSettings": {
    ...
  },
  "renderingSessionSettings": {
    "vmSize": "<standard or premium>",
    ...
  },
```

### <a name="how-the-renderer-evaluates-the-number-of-polygons"></a>Как модуль подготовки отчетов вычисляет количество многоугольников

Количество многоугольников, которые учитываются при проверке ограничений, — число многоугольников, которые фактически передаются в модуль подготовки отчетов. Эта геометрия обычно является суммой всех экземпляров моделей, но существуют и исключения. Следующая геометрия **не включена**:
* Загруженные экземпляры модели, которые полностью выходят за пределы представления фрустум.
* Модели или части модели, которые переключаются на невидимые, с использованием [компонента иерархического переопределения состояния](../overview/features/override-hierarchical-state.md).

Соответственно, можно написать приложение, предназначенное для `standard` размера, который загружает несколько моделей с числом многоугольников, близко к пределу для каждой отдельной модели. Когда приложение показывает только одну модель за раз, она не срабатывает.

### <a name="how-to-determine-the-number-of-polygons"></a>Определение количества многоугольников

Существует два способа определить количество многоугольников модели или сцены, влияющих на бюджетное ограничение `standard` размера конфигурации:
* На стороне преобразования модели Извлеките [выходной JSON файл преобразования](../how-tos/conversion/get-information.md)и проверьте `numFaces` запись в [разделе *инпутстатистикс* ](../how-tos/conversion/get-information.md#the-inputstatistics-section) .
* Если приложение работает с динамическим содержимым, количество визуализированных многоугольников можно запросить динамически во время выполнения. Используйте [запрос оценки производительности](../overview/features/performance-queries.md#performance-assessment-queries) и проверьте наличие `polygonsRendered` элемента в `FrameStatistics` структуре. `polygonsRendered`Поле будет иметь значение, `bad` когда модуль подготовки отчетов достигнет предельного значения многоугольника. Фон шахматной доски всегда прокладывается с некоторой задержкой, чтобы обеспечить возможность выполнения действий пользователя после этого асинхронного запроса. Действие пользователя может сделать экземпляр недоступным для скрытия или удаления экземпляров модели.

## <a name="pricing"></a>Цены

Подробное разбиение цен для каждого типа конфигурации см. на странице [цен на удаленную визуализацию](https://azure.microsoft.com/pricing/details/remote-rendering) .

## <a name="next-steps"></a>Дальнейшие действия
* [Примеры скриптов PowerShell](../samples/powershell-example-scripts.md)
* [Преобразование модели](../how-tos/conversion/model-conversion.md)

