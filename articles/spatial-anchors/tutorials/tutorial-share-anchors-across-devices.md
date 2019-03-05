---
title: Руководство по совместному использованию привязок между сеансами и устройствами с помощью Пространственных привязок Azure | Документация Майкрософт
description: В этом руководстве вы узнаете, как совместно использовать идентификаторы пространственных привязок Azure между устройствами в Unity с внутренней службой.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: df7f8aa5b49e3fe17be3b17a6e0f5d8861b26253
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753510"
---
# <a name="tutorial-sharing-across-sessions-and-devices-with-azure-spatial-anchors"></a>Руководство по совместному использованию привязок между сеансами и устройствами с помощью Пространственных привязок Azure

Из этого руководства вы узнаете, как использовать [Пространственные привязки Azure](../overview.md), чтобы:

1. Создавать привязки в одном сеансе, а затем использовать их в другом сеансе на том же или на другом устройстве. Например, в другой день.
2. Создавать привязки, которые могут быть одновременно обнаружены несколькими устройствами в одном и том же расположении.

![Сохраняемость](./media/persistence.gif)

"Пространственные привязки Azure" — это кроссплатформенная служба разработчика, которая позволяет создавать среды смешанной реальности с применением объектов, не меняющих своего расположения на устройствах с течением времени. По завершении роботы с этим руководством у вас будет приложение, которое можно развернуть на двух или более устройствах. Пространственные привязки Azure, созданные одним экземпляром, могут совместно использоваться другими экземплярами.

Вы узнаете, как:

> [!div class="checklist"]
> * Развернуть веб-приложение ASP.NET Core в Azure, которое можно использовать для обмена привязками, храня их в памяти в течение некоторого времени.
> * Настроить сцену AzureSpatialAnchorsLocalSharedDemo в примере Unity из наших кратких руководств, чтобы воспользоваться преимуществами веб-приложения для совместного использования пространственных привязок.
> * Развернуть и запустить привязку на одном или нескольких устройствах.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Хотя в этом руководстве рассматривается приложение Unity и веб-приложение ASP.NET Core, они представлены лишь в качестве примера того, как можно совместно использовать идентификаторы пространственных привязок Azure на других устройствах. Вы можете использовать другие языки и серверные технологии в тех же целях. Веб-приложение ASP.NET Core, используемое в этом руководстве, зависит от пакета SDK.NET Core 2.2. Оно отлично работает в обычных веб-приложениях Azure (для Windows), но в настоящее время не работает в веб-приложениях Azure для Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="deploy-your-sharing-anchors-service"></a>Развертывание службы совместного использования привязок

Откройте Visual Studio, а затем откройте проект в папке `Sharing\SharingServiceSample`.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы развернули веб-приложение ASP.NET Core в Azure, а затем настроили и развернули приложение Unity. Вы создали в приложении пространственные привязки и предоставили к ним доступ другим устройствам с помощью веб-приложения ASP.NET Core. Сведения о том, как улучшить веб-приложение ASP.NET Core, чтобы использовать Cosmos DB для хранения общих пространственных привязок, см. в следующем руководстве.

> [!div class="nextstepaction"]
> [Руководство по использованию Cosmos DB для хранения привязок](./tutorial-use-cosmos-db-to-store-anchors.md)
