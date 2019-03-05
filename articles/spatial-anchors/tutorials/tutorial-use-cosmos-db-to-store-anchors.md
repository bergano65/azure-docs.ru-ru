---
title: Руководство. Совместное использование привязок между сеансами и устройствами с помощью Пространственных привязок Azure и серверной части службы Azure Cosmos DB | Документация Майкрософт
description: Из этого руководства вы узнаете, как обеспечить совместное использование идентификаторов Пространственных привязок Azure между устройствами в Unity с помощью серверной части службы и базы данных Azure Cosmos DB.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: f0cd42fc37727099ed95a1c6fc2d427b7862412e
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753491"
---
# <a name="tutorial-sharing-across-sessions-and-devices-with-azure-spatial-anchors-and-an-azure-cosmos-db-back-end"></a>Руководство по Совместное использование привязок между сеансами и устройствами с помощью Пространственных привязок Azure и серверной части службы Azure Cosmos DB

Из этого руководства вы узнаете, как использовать [Пространственные привязки Azure](../overview.md), чтобы:

1. Создавать привязки в одном сеансе, а затем использовать их в другом сеансе на том же или на другом устройстве, например в другой день.
2. Создавать привязки, которые могут быть одновременно обнаружены несколькими устройствами в одном и том же расположении.

![Сохраняемость](./media/persistence.gif)

[Пространственные привязки Azure](../overview.md) — это кроссплатформенная служба разработчика, которая позволяет создавать среды смешанной реальности с применением объектов, не меняющих своего расположения на устройствах с течением времени. По завершении роботы с этим руководством у вас будет приложение, которое можно развернуть на двух или более устройствах. Пространственные привязки Azure, созданные одним экземпляром, могут передавать свои идентификаторы другим экземплярам с помощью Cosmos DB.

Вы узнаете, как:

> [!div class="checklist"]
> * развернуть веб-приложение ASP.NET Core в Azure, которое можно использовать для обмена привязками, храня их в Cosmos DB;
> * настроить сцену AzureSpatialAnchorsLocalSharedDemo в примере Unity из наших кратких руководств, чтобы воспользоваться преимуществами веб-приложения для совместного использования пространственных привязок;
> * развернуть и запустить привязку на одном или нескольких устройствах.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Хотя в этом руководстве рассматриваются Unity и Azure Cosmos DB, они представлены лишь в качестве примера того, как можно совместно использовать идентификаторы пространственных привязок Azure на других устройствах. Вы можете использовать другие языки и серверные технологии в тех же целях. Веб-приложение ASP.NET Core, используемое в этом руководстве, зависит от пакета SDK.NET Core 2.2. Оно отлично работает в обычных веб-приложениях Azure (для Windows), но в настоящее время не работает в веб-приложениях Azure для Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="create-a-database-account"></a>Создание учетной записи базы данных

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Запишите строку `Connection String`, так как мы используем ее на последующем шаге.

## <a name="deploy-your-sharing-anchors-service"></a>Развертывание службы совместного использования привязок

Откройте Visual Studio, а затем откройте проект в папке `Sharing\SharingServiceSample`.

### <a name="configure-the-service-so-that-it-uses-your-cosmos-db"></a>Настройка службы на использование вашего экземпляра службы Cosmos DB

В **обозревателе решений** откройте файл `SharingService\Startup.cs`.

Найдите строку `#define INMEMORY_DEMO` в верхней части файла и поставьте перед ней знак комментария. Сохраните файл.

В **обозревателе решений** откройте файл `SharingService\appsettings.json`.

Найдите свойство `StorageConnectionString` и введите для него значение `Connection String`, которое вы записали на [шаге создания учетной записи для базы данных](#create-a-database-account). Сохраните файл.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы обеспечили совместное использование идентификаторов привязок между устройствами с помощью Azure Cosmos DB. Чтобы узнать больше о библиотеке Пространственных привязок Azure, перейдите к руководству по созданию и поиску привязок.

> [!div class="nextstepaction"]
> [Создание и поиск привязок с помощью Пространственных привязок Azure](../create-locate-anchors-overview.md)
