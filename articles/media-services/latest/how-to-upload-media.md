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
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: inhenkel
ms.openlocfilehash: a046a3caba9a0909d873356bda8d1fa6cf1f9860
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719263"
---
# <a name="upload-media-for-streaming-or-encoding"></a>Отправка мультимедиа для потоковой передачи или кодирования.

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
