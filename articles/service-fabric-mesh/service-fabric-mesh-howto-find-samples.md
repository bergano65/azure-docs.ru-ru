---
title: Примеры экземпляров Сетки Azure Service Fabric | Документация Майкрософт
description: Ниже приведен индекс доступных Service Fabric примеров приложений сетки. Исходный код в этих примерах показывает, как достичь определенного сценария с помощью модели ресурсов Service Fabric.
services: service-fabric-mesh
keywords: ''
author: athinanthny
ms.author: atsenthi
ms.date: 12/03/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chackdan
ms.openlocfilehash: a300c06a6a18bf2e986b3736b78957daf3d7efd6
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718818"
---
# <a name="find-service-fabric-mesh-samples"></a>Примеры экземпляров Сетки Service Fabric

В этой таблице перечислены доступные примеры приложений Сетки Service Fabric. Исходный код в этих примерах показывает, как достичь определенного сценария с помощью модели ресурсов Service Fabric.

Дополнительные сведения о развертывании шаблонов непосредственно в Azure см. в разделе [Страница примера шаблона GitHub](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/README.md).

|Пример шаблона|Описание сценария|Исходный код|Инструменты разработчика|
|------------|--------------------|----------|----------------------|
| [Приложение Hello World](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/helloworld) | Статическая веб-страница размещена в контейнере. Для Linux используется nginx, для Windows — IIS | [Исходный код](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) | Требования отсутствуют |
| [Приложение Счетчик для томов файлов Azure](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.md) | Сохранение состояния с помощью подключения тома службы файлов Azure в контейнере. <br><br> **Примечание.** Для этого шаблона требуется, чтобы общий ресурс файлов Azure уже был подготовлен [инструкциями](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share) | [Исходный код](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Инструментарий Сетки Visual Studio |
| [TodoListApp](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/todolist) | Создайте приложение с внешним и внутренним сервисом, использующим разрешение на основе DNS. Используется в качестве руководства [здесь](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-tutorial-create-dotnetcore) | [Исходный код](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/todolistapp) | Инструментарий Сетки Visual Studio |
| [Приложение Визуальные объекты](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/visualobjects) | Масштабирование и обновление микрослужб в приложении. | [Исходный код](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) |  Инструментарий Сетки Visual Studio |
| [Приложение для голосования](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/voting) | Создание приложения с внешним и внутренним сервисом, использующим разрешение на основе DNS | [Исходный код](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) | Средства сетки Visual Studio для версии Windows, VS Code и DotNet CLI можно использовать для версии Linux. |
| [Приложение Счетчик тома Service Fabric](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.sfreliablevolume.md)| Сохранение состояния путем монтирования тома на основе Service Fabric Reliable Disk внутри контейнера.| [Исходный код](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Инструментарий Сетки Visual Studio |
