---
title: Поиск примеров сетки Azure Service Fabric
description: Ниже приведен индекс доступных Service Fabric примеров приложений сетки. Исходный код в этих примерах показывает, как достичь определенного сценария с помощью модели ресурсов Service Fabric.
ms.date: 12/03/2018
ms.topic: conceptual
ms.openlocfilehash: 64d9e4e112f26f83eedd02b034c8191c6f677618
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261267"
---
# <a name="find-service-fabric-mesh-samples"></a>Примеры экземпляров Сетки Service Fabric

В этой таблице перечислены доступные примеры приложений Сетки Service Fabric. Исходный код в этих примерах показывает, как достичь определенного сценария с помощью модели ресурсов Service Fabric.

Дополнительные сведения о развертывании шаблонов непосредственно в Azure см. на [странице Пример шаблона на сайте GitHub.](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/README.md)

|Пример шаблона|Описание сценария|Исходный код|Средства для разработчиков|
|------------|--------------------|----------|----------------------|
| [Приложение Hello World](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/helloworld) | Статическая веб-страница размещена в контейнере. Для Linux используется nginx, для Windows — IIS | [Исходный код](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) | Требования отсутствуют |
| [Приложение Счетчик для томов файлов Azure](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.md) | Сохранение состояния с помощью подключения тома службы файлов Azure в контейнере. <br><br> **Примечание.** Для этого шаблона требуется, чтобы общий ресурс файлов Azure уже был подготовлен [инструкциями](../storage/files/storage-how-to-create-file-share.md) | [Исходный код](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Инструментарий Сетки Visual Studio |
| [TodoListApp](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/todolist) | Создайте приложение с внешним и внутренним сервисом, использующим разрешение на основе DNS. Используется в качестве руководства [здесь](./service-fabric-mesh-tutorial-create-dotnetcore.md) | [Исходный код](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/todolistapp) | Инструментарий Сетки Visual Studio |
| [Приложение Визуальные объекты](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/visualobjects) | Масштабирование и обновление микрослужб в приложении. | [Исходный код](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) |  Инструментарий Сетки Visual Studio |
| [Приложение для голосования](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/voting) | Создание приложения с внешним и внутренним сервисом, использующим разрешение на основе DNS | [Исходный код](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) | Средства сетки Visual Studio для версии Windows, VS Code и DotNet CLI можно использовать для версии Linux. |
| [Приложение Счетчик тома Service Fabric](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.sfreliablevolume.md)| Сохранение состояния путем монтирования тома на основе Service Fabric Reliable Disk внутри контейнера.| [Исходный код](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Инструментарий Сетки Visual Studio |
