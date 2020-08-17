---
title: Руководство по совместному использованию привязок с помощью Azure Cosmos DB
description: Из этого учебника вы узнаете, как обеспечить совместное использование идентификаторов Пространственных привязок Azure между устройствами Android или iOS в Unity с помощью серверной службы и Azure Cosmos DB.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 07/31/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 8ff431c27dd53e82f9003b658c82ceb3efb5d320
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810026"
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
