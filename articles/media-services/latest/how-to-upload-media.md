---
title: Отправка мультимедиа
titleSuffix: Azure Media Services
description: Узнайте, как отправить мультимедиа для потоковой передачи или кодирования.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 0bdb2c36bc895c9229e4c04e9e0d76aa852bd139
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89297312"
---
# <a name="upload-media-for-streaming-or-encoding"></a>Отправка мультимедиа для потоковой передачи или кодирования.

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

В Службах мультимедиа цифровые файлы (мультимедиа) отправляются в контейнер BLOB-объектов, связанный с ресурсом. Сущность [Ресурс](/rest/api/media/operations/asset) может содержать видео, аудио, изображения, коллекции эскизов, текстовые каналы и файлы скрытых субтитров (а также метаданные этих файлов). После отправки этих файлов в контейнер ресурса содержимое сохраняется в безопасном расположении в облаке для дальнейшей обработки и потоковой передачи.

Прежде чем приступать к работе, необходимо определить следующее:

1. Локальный путь к файлу, который необходимо передать.
1. Идентификатор ресурса (контейнера).
1. Имя, которое будет использоваться для отправленного файла, включая расширение.
1. Имя используемой учетной записи хранения.
1. Ключ доступа для вашей учетной записи хранения.

## <a name="portal"></a>[Портал](#tab/portal/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-portal.md)]

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-cli.md)]

## <a name="rest"></a>[REST](#tab/rest/)

[Создав ресурс с помощью Postman или другого метода REST и получив URL-адрес SAS для ресурса](how-to-create-asset.md?tabs=rest), используйте API службы хранилища Azure или пакеты SDK (например, [REST API службы хранилища](../../storage/common/storage-rest-api-auth.md) или [пакет SDK для .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

---
<!-- add these to the tabs when available -->
Другие методы см. в [документации по службе хранилища Azure](https://docs.microsoft.com/azure/storage/blobs/) для работы с BLOB-объектами в [.NET](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet), [Java](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-java), [Python](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-python)и [JavaScript (Node.js)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-nodejs).

## <a name="next-steps"></a>Дальнейшие действия

> [Общие сведения о службах мультимедиа](media-services-overview.md)
