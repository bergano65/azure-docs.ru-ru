---
title: Миграция из библиотеки процессоров из изменений в Azure Cosmos DB .NET V3 SDK
description: Узнайте, как перенести приложение с использования библиотеки процессоров изменений в Azure Cosmos DB SDK V3
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: maquaran
ms.openlocfilehash: 9570a8512e3437b12ecce2ef0c708a74a8806482
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588889"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>Миграция из библиотеки процессоров из изменений в Azure Cosmos DB .NET V3 SDK

В этой статье описаны необходимые шаги для переноса кода существующего приложения, используюшего [библиотеку процессоров изменения в](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet) функцию [подачи изменений](change-feed.md) в последней версии .NET SDK (также именуемый .NET V3 SDK).

## <a name="required-code-changes"></a>Необходимые изменения кода

В .NET V3 SDK есть несколько изменений, следующие ключевые шаги для переноса приложения:

1. Преобразуйте `DocumentCollectionInfo` `Container` экземпляры в ссылки на контролируемые и арендуемые контейнеры.
1. Настройки, которые `WithProcessorOptions` используются должны `WithLeaseConfiguration` `WithPollInterval` быть обновлены `WithStartTime` для использования `WithMaxItems` и для интервалов, для начала [времени,](how-to-configure-change-feed-start-time.md)и определить максимальное количество элементов.
1. Установите `processorName` `GetChangeFeedProcessorBuilder` на соответствие значения, `ChangeFeedProcessorOptions.LeasePrefix`настроенного `string.Empty` на, или использовать в противном случае.
1. Изменения больше не поставляются `IReadOnlyList<Document>`как, вместо `IReadOnlyCollection<T>` этого, это, где `T` тип, который необходимо определить, нет класса базового элемента больше.
1. Для обработки изменений больше не нужна реализация, вместо этого необходимо [определить делегата.](change-feed-processor.md#implementing-the-change-feed-processor) Делегат может быть статичной функцией или, если вам нужно поддерживать состояние в выполнении, можно создать свой собственный класс и пройти метод экземпляра в качестве делегата.

Например, если исходный код для создания процессора подачи изменений выглядит следующим образом:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorLibrary)]

Миграционный код будет выглядеть следующим:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorMigrated)]

И делегат, может быть статическим методом:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=Delegate)]

## <a name="state-and-lease-container"></a>Контейнер для государства и аренды

Подобно библиотеке процессоров с изменением, функция подачи изменений в .NET V3 SDK использует [контейнер аренды](change-feed-processor.md#components-of-the-change-feed-processor) для хранения состояния. Тем не менее, схемы разные.

Процессор подачи изменения SDK V3 обнаружит любое старое состояние библиотеки и автоматически переносит его в новую схему при первом выполнении мигрированного кода приложения. 

Вы можете безопасно остановить приложение, используя старый код, перенести код в новую версию, запустить перемотанный приложение, и любые изменения, которые произошли в то время как приложение было остановлено, будут подобраны и обработаны новой версией.

> [!NOTE]
> Миграция из приложений, использующих библиотеку, в .NET V3 SDK является односторонней, так как государство (аренда) будет перенесено на новую схему. Миграция не совместима с обратной.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Azure Cosmos DB](sql-api-sdk-dotnet.md)
* [Примеры использования в GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Дополнительные примеры на GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Дальнейшие действия

Вы можете продолжить знакомство с обработчиком канала изменений, перейдя к следующим статьям:

* [Обработчик канала изменений в Azure Cosmos DB](change-feed-processor.md)
* [Использование оценщика канала изменений](how-to-use-change-feed-estimator.md)
* [Время запуска обработчика канала изменений](how-to-configure-change-feed-start-time.md)
