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
ms.openlocfilehash: 3040369e655ab91f56f271313dc4d2613f02be06
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92015861"
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
Другие методы см. в [документации по службе хранилища Azure](../../storage/blobs/index.yml) для работы с BLOB-объектами в [.NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md), [Java](../../storage/blobs/storage-quickstart-blobs-java.md), [Python](../../storage/blobs/storage-quickstart-blobs-python.md)и [JavaScript (Node.js)](../../storage/blobs/storage-quickstart-blobs-nodejs.md).

## <a name="next-steps"></a>Дальнейшие действия

> [Общие сведения о службах мультимедиа](media-services-overview.md)