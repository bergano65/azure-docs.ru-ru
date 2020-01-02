---
title: Создание входных данных задания служб мультимедиа Azure из локального файла | Документация Майкрософт
description: В этой статье показано, как создать входные данные задания служб мультимедиа Azure из локального файла.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/18/2019
ms.author: juliako
ms.openlocfilehash: a91305f1c5bfb50e0354dc0054d1a149182d921f
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74888450"
---
# <a name="create-a-job-input-from-a-local-file"></a>Создание входных данных задания из локального файла

При отправке заданий из Служб мультимедиа версии 3 необходимо указать расположение входного видео. Это видео может храниться как ресурс Служб мультимедиа. В этом случае необходимо создать входной ресурс на основе файла, который хранится локально в хранилище BLOB-объектов Azure. В этой статье показано, как создать входные данные задания из локального файла. Полный пример см. в этом [репозитории GitHub](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>Пример кода .NET

С помощью приведенного ниже кода можно создать входной ресурс и использовать его в качестве входных данных задания. Функция CreateInputAsset выполняет следующие задачи:

* создает ресурс;
* получает доступный для записи [URL-адрес SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) для [контейнера ресурса в хранилище](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container);
* отправляет файл в контейнер в хранилище с использованием URL-адреса SAS;

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Следующий фрагмент кода создает выходной ресурс, если он еще не существует:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAssetAsync)]

Следующий фрагмент кода отправляет задание кодирования:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Коды ошибок задания

См. статью о [кодах ошибок](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Дальнейшие действия

[Создание входных данных задания из URL-адреса HTTPS](job-input-from-http-how-to.md)
