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
ms.date: 03/26/2020
ms.author: juliako
ms.openlocfilehash: aba987ba232a29ffc240f72039b1e24bb87a2ed4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80345905"
---
# <a name="create-a-job-input-from-a-local-file"></a>Создание входных данных задания из локального файла

При отправке заданий из Служб мультимедиа версии 3 необходимо указать расположение входного видео. Это видео может храниться как ресурс Служб мультимедиа. В этом случае необходимо создать входной ресурс на основе файла, который хранится локально в хранилище BLOB-объектов Azure. В этой статье показано, как создать входные данные задания из локального файла. Полный пример см. в этом [репозитории GitHub](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="prerequisites"></a>Предварительные требования 

* [Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md).
* Изучите, как [управлять активами](manage-asset-concept.md).

## <a name="net-sample"></a>Пример кода .NET

С помощью приведенного ниже кода можно создать входной ресурс и использовать его в качестве входных данных задания. Функция CreateInputAsset выполняет следующие задачи:

* создает ресурс;
* Получает доступный для записи [URL-адрес SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) [контейнера ресурса в хранилище](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container) .
* отправляет файл в контейнер в хранилище через URL-адрес SAS.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Следующий фрагмент кода создает выходной ресурс, если он еще не существует:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

Следующий фрагмент кода отправляет задание кодирования:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Коды ошибок задания

См. статью о [кодах ошибок](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Дальнейшие действия

[Создайте входные данные задания на основе URL-адреса HTTPS](job-input-from-http-how-to.md).
