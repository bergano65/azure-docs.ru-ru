---
title: Создание входных данных задания Служб мультимедиа Azure из URL-адреса HTTPS | Документация Майкрософт
description: В этой статье показано, как создать входные данные задания из URL-адреса HTTP (HTTPS).
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 6919bf49e46a65a02d2444277c8005fbb6f4ca1f
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55991656"
---
# <a name="create-a-job-input-from-an-https-url"></a>Создание входных данных задания из URL-адреса HTTPS

При отправке заданий из Служб мультимедиа версии 3 необходимо указать расположение входного видео. Это можно сделать, указав URL-адрес HTTP (HTTPS) как входные данные задания (как показано в этом примере). Обратите внимание, что в настоящее время AMS версии 3 не поддерживает кодирование блочной передачи по URL-адресам HTTPS. Полный пример см. в этом [репозитории GitHub](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>Пример кода .NET

В следующем коде показано, как создать задание с помощью входных данных URL-адреса HTTPS.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>Дополнительная информация

[Создание входных данных задания из локального файла](job-input-from-local-file-how-to.md).
