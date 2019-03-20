---
title: Примеры экземпляров Сетки Azure Service Fabric | Документация Майкрософт
description: Дополнительные сведения о том, как найти примеры различных приложений Сетки Service Fabric.
services: service-fabric-mesh
keywords: ''
author: v-vasuke
ms.author: v-vasuke
ms.date: 12/03/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chakdan
ms.openlocfilehash: db8c68bf5f9aeb8069044c1344be9f69e498b1b7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780888"
---
# <a name="find-service-fabric-mesh-samples"></a>Примеры экземпляров Сетки Service Fabric

В этой таблице перечислены доступные примеры приложений Сетки Service Fabric. Исходный код в этих примерах показывает, как достичь определенного сценария с помощью модели ресурсов Service Fabric.

Дополнительные сведения о развертывании шаблонов непосредственно в Azure см. в разделе [Страница примера шаблона GitHub](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/README.md).


|Пример шаблона|Описание сценария|Исходный код|Средства для разработчиков|
|------------|--------------------|----------|----------------------|
| [Приложение Hello World](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/helloworld) | Статическая веб-страница размещена в контейнере. Для Linux используется nginx, для Windows — IIS | [Исходный код](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) | Требования отсутствуют |
| [Приложение Счетчик для томов файлов Azure](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter) | Сохранение состояния с помощью подключения тома службы файлов Azure в контейнере. <br><br> **Примечание.** Для этого шаблона требуется, чтобы общий ресурс файлов Azure уже был подготовлен. Инструкции см. в статье [Создание общей папки в службе файлов Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share). | [Исходный код](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Инструментарий Сетки Visual Studio |
| [TodoListApp](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/todolist) | Создайте приложение с внешним и внутренним сервисом, использующим разрешение на основе DNS. Используется в качестве руководства [здесь](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-tutorial-create-dotnetcore) | [Исходный код](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/todolistapp) | Инструментарий Сетки Visual Studio |
| [Приложение Визуальные объекты](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/visualobjects) | Масштабирование и обновление микрослужб в приложении. | [Исходный код](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) |  Инструментарий Сетки Visual Studio |
| [Приложение для голосования](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/voting) | Создание приложения с внешним и внутренним сервисом, использующим разрешение на основе DNS | [Исходный код](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) | Инструментарий Сетки Visual Studio для версии Windows, VS Code/dotnet cli можно использовать для версии Linux |
| [Приложение Счетчик тома Service Fabric](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter)| Сохранение состояния путем монтирования тома на основе Service Fabric Reliable Disk внутри контейнера.| [Исходный код](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Инструментарий Сетки Visual Studio |
