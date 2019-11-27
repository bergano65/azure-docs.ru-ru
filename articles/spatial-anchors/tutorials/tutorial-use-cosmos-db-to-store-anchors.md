---
title: Руководство. Совместное использование Пространственных привязок Azure между сеансами и устройствами с помощью серверной части службы Azure Cosmos DB | Документация Майкрософт
description: Из этого учебника вы узнаете, как обеспечить совместное использование идентификаторов Пространственных привязок Azure между устройствами Android или iOS в Unity с помощью серверной службы и Azure Cosmos DB.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 7ddbff563f79992f21aef5182177f4fb60c61dab
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882163"
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

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы обеспечили совместное использование идентификаторов привязок между устройствами с помощью Azure Cosmos DB. Чтобы узнать больше об использовании Пространственных привязок Azure в новом приложении Unity HoloLens, перейдите к следующему учебнику.

> [!div class="nextstepaction"]
> [Запуск нового приложения Unity HoloLens](./tutorial-new-unity-hololens-app.md)
