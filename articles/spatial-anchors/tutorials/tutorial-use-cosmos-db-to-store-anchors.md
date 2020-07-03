---
title: Руководство по совместному использованию привязок с помощью Azure Cosmos DB
description: Из этого учебника вы узнаете, как обеспечить совместное использование идентификаторов Пространственных привязок Azure между устройствами Android или iOS в Unity с помощью серверной службы и Azure Cosmos DB.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 71b3027d86400d6921895f86e257ddff2961f91f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77615158"
---
# <a name="tutorial-sharing-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>Руководство по Совместное использование Пространственных привязок Azure между сеансами и устройствами с помощью серверной части службы Azure Cosmos DB

Это руководство является продолжением руководства по [совместному использованию Пространственных привязок Azure между сеансами и устройствами](../../../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md). Он поможет вам добавить еще несколько возможностей, чтобы использовать Azure Cosmos DB в качестве серверного хранилища при совместном использовании Пространственных привязок Azure для сеансов и устройств.

![GIF, иллюстрирующий сохраняемость объектов](./media/persistence.gif)

Хотя в этом учебнике рассматриваются Unity и Azure Cosmos DB, они представлены лишь в качестве примера того, как можно совместно использовать идентификаторы Пространственных привязок на разных устройствах. Вы можете использовать другие языки и серверные технологии в тех же целях. Для веб-приложения ASP.NET Core, используемого в этом учебнике, требуется пакет SDK .NET Core 2.2. Он отлично работает в веб-приложениях для Windows, но в настоящее время не работает в веб-приложениях для Linux.

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

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="unity-20193"></a>Unity 2019.3

Из-за критических изменений сейчас Unity 2019.3 не поддерживается. Используйте Unity 2019.1 или 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы обеспечили совместное использование идентификаторов привязок между устройствами с помощью Azure Cosmos DB. Чтобы узнать больше об использовании Пространственных привязок Azure в новом приложении Unity HoloLens, перейдите к следующему учебнику.

> [!div class="nextstepaction"]
> [Запуск нового приложения Unity HoloLens](./tutorial-new-unity-hololens-app.md)
