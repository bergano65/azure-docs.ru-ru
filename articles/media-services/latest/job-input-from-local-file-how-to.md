---
title: Создание входных данных задания Служб мультимедиа Azure из локального файла | Документация Майкрософт
description: В этой статье показано, как создать входные данные задания из локального файла.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/25/2018
ms.author: juliako
ms.openlocfilehash: 66bd03b03289f568c019588f1b8ac1317ab9c076
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47222026"
---
# <a name="create-a-job-input-from-a-local-file"></a>Создание входных данных задания из локального файла

При отправке заданий из Служб мультимедиа версии 3 необходимо указать расположение входного видео. Это видео может храниться как ресурс Служб мультимедиа. В этом случае необходимо создать входной ресурс на основе файла, который хранится локально в хранилище BLOB-объектов Azure. В этой статье показано, как создать входные данные задания из локального файла. Полный пример см. в этом [репозитории GitHub](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>Пример кода .NET

С помощью приведенного ниже кода можно создать входной ресурс и использовать его в качестве входных данных задания. Функция CreateInputAsset выполняет следующие задачи:

* создает ресурс; 
* получает записываемый [URL-адрес SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) [контейнера ресурса в хранилище](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container);
* отправляет файл в контейнер в хранилище через URL-адрес SAS.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>Дополнительная информация

[Создание входных данных задания из URL-адреса HTTPS](job-input-from-http-how-to.md)
