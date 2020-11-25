---
title: Руководство по совместному использованию привязок с помощью Azure Cosmos DB
description: Из этого учебника вы узнаете, как обеспечить совместное использование идентификаторов Пространственных привязок Azure между устройствами Android или iOS в Unity с помощью серверной службы и Azure Cosmos DB.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: ff888cd98cc79f3e2d508b01f092102eaa038c86
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2020
ms.locfileid: "95494766"
---
# <a name="tutorial-sharing-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>Руководство по Совместное использование Пространственных привязок Azure между сеансами и устройствами с помощью серверной части службы Azure Cosmos DB

Это руководство является продолжением руководства по [совместному использованию Пространственных привязок Azure между сеансами и устройствами](../../../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md). С его помощью вы добавите еще несколько возможностей, чтобы использовать Azure Cosmos DB в качестве серверного хранилища при совместном использовании Пространственных привязок Azure для сеансов и устройств.

![GIF, иллюстрирующий сохраняемость объектов](./media/persistence.gif)

Хотя в этом учебнике рассматриваются Unity и Azure Cosmos DB, они представлены лишь в качестве примера того, как можно совместно использовать идентификаторы Пространственных привязок на разных устройствах. Вы можете использовать другие языки и серверные технологии в тех же целях.

## <a name="create-a-database-account"></a>Создание учетной записи базы данных

Добавьте базу данных Azure Cosmos в созданную ранее группу ресурсов.

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Скопируйте `Connection String`, так как она потребуется вам.

## <a name="make-minor-changes-to-the-sharingservice-files"></a>Внесение незначительных изменений в файлы SharingService

Откройте `SharingService\Startup.cs` в **обозревателе решений**.

Найдите строку `#define INMEMORY_DEMO` в верхней части файла и закомментируйте ее. Сохраните файл.

Откройте `SharingService\appsettings.json` в **обозревателе решений**.

Найдите свойство `StorageConnectionString` и введите для него значение, совпадающее со значением `Connection String`, которое вы скопировали на [шаге создания учетной записи для базы данных](#create-a-database-account). Сохраните файл.

SharingService можно опубликовать еще раз и запустить пример приложения.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы обеспечили совместное использование идентификаторов привязок между устройствами с помощью Azure Cosmos DB. Чтобы узнать больше об использовании Пространственных привязок Azure в новом приложении Unity HoloLens, перейдите к следующему учебнику.

> [!div class="nextstepaction"]
> [Запуск нового приложения Unity HoloLens](./tutorial-new-unity-hololens-app.md)
