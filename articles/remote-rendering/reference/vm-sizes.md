---
title: Размеры виртуальной машины
description: Описание отдельных размеров виртуальных машин, которые можно выделить
author: florianborn71
ms.author: flborn
ms.date: 05/28/2020
ms.topic: reference
ms.openlocfilehash: e8e439a055b71ed291573965c561ee31610e3ed4
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121618"
---
# <a name="vm-sizes"></a>Размеры виртуальной машины

Служба отрисовки может выполнять действия на двух разных типах компьютеров в Azure, называемых `Standard` и `Premium` .

## <a name="polygon-limits"></a>Пределы многоугольников

Для размера виртуальной машины существует жесткое ограничение в **20 000 000 многоугольников** `Standard` . Такого ограничения на размер не существует `Premium` .

Когда модуль подготовки отчетов на стандартной виртуальной машине достигает этого ограничения, он переключает отрисовку в фон шахматной доски:

![Шахматная доска](media/checkerboard.png)

## <a name="allocate-the-vm"></a>Выделение виртуальной машины

Требуемый тип виртуальной машины должен быть указан во время инициализации сеанса отрисовки. Его нельзя изменить в работающем сеансе. В следующих примерах кода показано место, где необходимо указать размер виртуальной машины:

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

В [примерах сценариев PowerShell](../samples/powershell-example-scripts.md)размер виртуальной машины должен быть указан в `arrconfig.json` файле:

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

Существует два способа определить количество многоугольников модели или сцены, влияющих на бюджетное ограничение `standard` размера виртуальной машины.
* На стороне преобразования модели Извлеките [выходной JSON файл преобразования](../how-tos/conversion/get-information.md)и проверьте `numFaces` запись в [разделе *инпутстатистикс* ](../how-tos/conversion/get-information.md#the-inputstatistics-section) .
* Если приложение работает с динамическим содержимым, количество визуализированных многоугольников можно запросить динамически во время выполнения. Используйте [запрос оценки производительности](../overview/features/performance-queries.md#performance-assessment-queries) и проверьте наличие `polygonsRendered` элемента в `FrameStatistics` структуре. `polygonsRendered`Поле будет иметь значение, `bad` когда модуль подготовки отчетов достигнет предельного значения многоугольника. Фон шахматной доски всегда прокладывается с некоторой задержкой, чтобы обеспечить возможность выполнения действий пользователя после этого асинхронного запроса. Действие пользователя может сделать экземпляр недоступным для скрытия или удаления экземпляров модели.

## <a name="pricing"></a>Цены

Подробные сведения о ценах на каждый тип виртуальной машины см. на странице [цен на удаленную визуализацию](https://azure.microsoft.com/pricing/details/remote-rendering) .

## <a name="next-steps"></a>Дальнейшие действия
* [Примеры скриптов PowerShell](../samples/powershell-example-scripts.md)
* [Преобразование модели](../how-tos/conversion/model-conversion.md)

