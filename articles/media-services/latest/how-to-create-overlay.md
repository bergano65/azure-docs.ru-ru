---
title: Создание наложения с помощью Media Encoder Standard
description: Узнайте, как создать наложение с Media Encoder Standard.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 0d1a6d5626e081ff50f65b3a4396e223b33f920d
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2020
ms.locfileid: "87433637"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>Создание наложения с помощью Media Encoder Standard

Стандартный кодировщик служб мультимедиа позволяет наложить изображение на существующее видео. В настоящее время поддерживаются следующие форматы: png, jpg, gif и bmp.

## <a name="prerequisites"></a>Предварительные требования

* Собирайте сведения об учетной записи, необходимые для настройки *appsettings.jsдля* файла в примере. Если вы не знаете, как это сделать, см. раздел [Краткое руководство по регистрации приложения на платформе Microsoft Identity](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app). В *appsettings.jsдля* файла ожидались следующие значения.

    ```json
    {
    "AadClientId": "",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "",
    "AadTenantId": "",
    "AccountName": "",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "Region": "",
    "ResourceGroup": "",
    "SubscriptionId": ""
    }
    ```

Если вы еще не знакомы с преобразованиями, рекомендуется выполнить следующие действия.

* Чтение [кодирования видео и аудио с помощью служб мультимедиа](encoding-concept.md)
* Узнайте, [как кодировать с помощью пользовательского преобразования — .NET](customize-encoder-presets-how-to.md). Выполните действия, описанные в этой статье, чтобы настроить .NET для работы с преобразованиями, а затем вернитесь сюда, чтобы испытать пример предустановки наложения.
* См. [справочный документ преобразования](https://docs.microsoft.com/rest/api/media/transforms).

Когда вы знакомы с преобразованиями, Скачайте пример наложения.

## <a name="overlays-preset-sample"></a>Образец предустановки наложения

Скачайте [образец мультимедиа-Services-Overlay](https://github.com/Azure-Samples/media-services-overlays) , чтобы начать работу с наложением.

## <a name="next-steps"></a>Дальнейшие действия

* [Подрезать видео при кодировании с помощью служб мультимедиа — .NET](subclip-video-dotnet-howto.md)