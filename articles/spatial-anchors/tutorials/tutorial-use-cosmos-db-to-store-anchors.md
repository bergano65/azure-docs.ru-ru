---
title: Учебник. Совместное использование привязок между сеансами и устройствами с помощью Пространственных привязок Azure и серверной части службы Azure Cosmos DB | Документация Майкрософт
description: Из этого учебника вы узнаете, как обеспечить совместное использование идентификаторов Пространственных привязок Azure между устройствами Android или iOS в Unity с помощью серверной службы и Azure Cosmos DB.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 0e7011b9778221869940b137a2b87239f2d8db9b
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286403"
---
# <a name="tutorial-sharing-across-sessions-and-devices-with-azure-spatial-anchors-and-an-azure-cosmos-db-back-end"></a>Руководство. Совместное использование привязок между сеансами и устройствами с помощью Пространственных привязок Azure и серверной части службы Azure Cosmos DB

Из этого учебника вы узнаете, как использовать [Пространственные привязки Azure](../overview.md), чтобы:

- Создавать привязки во время одного сеанса, а затем обнаруживать их во время другого сеанса на том же или на другом устройстве. Например, второй сеанс может быть в другой день.
- Создавать привязки, которые могут быть одновременно обнаружены несколькими устройствами в одном и том же расположении.

![GIF, иллюстрирующий сохраняемость объектов](./media/persistence.gif)

[Пространственные привязки Azure](../overview.md) — это кроссплатформенная служба разработчика, с помощью которой можно создавать среды смешанной реальности с применением объектов, не меняющих своего расположения на устройствах с течением времени. По завершении роботы с этим руководством у вас будет приложение, которое можно развернуть на двух или более устройствах. Пространственные привязки, созданные одним экземпляром, могут передавать свои идентификаторы другим экземплярам с помощью Azure Cosmos DB.

Вы узнаете, как:

> [!div class="checklist"]
> * развернуть веб-приложение ASP.NET Core в Azure, которое можно использовать для обмена привязками, храня их в Azure Cosmos DB;
> * настроить сцену AzureSpatialAnchorsLocalSharedDemo в примере Unity из кратких руководств Azure, чтобы воспользоваться преимуществами веб-приложения для совместного использования пространственных привязок;
> * развернуть и запустить приложение на одном или нескольких устройствах.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Хотя в этом учебнике рассматриваются Unity и Azure Cosmos DB, они представлены лишь в качестве примера того, как можно совместно использовать идентификаторы Пространственных привязок на разных устройствах. Вы можете использовать другие языки и серверные технологии в тех же целях. Для веб-приложения ASP.NET Core, используемого в этом учебнике, требуется пакет SDK .NET Core 2.2. Он отлично работает в веб-приложениях для Windows, но в настоящее время не работает в веб-приложениях для Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="create-a-database-account"></a>Создание учетной записи базы данных

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Скопируйте `Connection String`, так как она потребуется вам.

## <a name="open-the-sample-project-in-unity"></a>Открытие примера проекта в Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-the-sharing-anchors-service"></a>Развертывание службы совместного использования привязок

Откройте Visual Studio, а затем откройте проект в папке `Sharing\SharingServiceSample`.

### <a name="configure-the-service-to-use-your-azure-cosmos-db-database"></a>Настройка службы для использования базы данных Azure Cosmos DB

Откройте `SharingService\Startup.cs` в **обозревателе решений**.

Найдите строку `#define INMEMORY_DEMO` в верхней части файла и закомментируйте ее. Сохраните файл.

Откройте `SharingService\appsettings.json` в **обозревателе решений**.

Найдите свойство `StorageConnectionString` и введите для него значение, совпадающее со значением `Connection String`, которое вы скопировали на [шаге создания учетной записи для базы данных](#create-a-database-account). Сохраните файл.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы обеспечили совместное использование идентификаторов привязок между устройствами с помощью Azure Cosmos DB. Чтобы узнать больше о библиотеке Пространственных привязок Azure, перейдите к руководству по созданию и поиску привязок.

> [!div class="nextstepaction"]
> [Создание и поиск привязок с помощью Пространственных привязок Azure](../create-locate-anchors-overview.md)
