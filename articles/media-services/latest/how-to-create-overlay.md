---
title: Создание наложения с помощью Media Encoder Standard
description: Узнайте, как создать наложение с Media Encoder Standard.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 85b1b6c56221deaa03057a7ccb658b4bf67124eb
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830586"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>Создание наложения с помощью Media Encoder Standard

Стандартный кодировщик служб мультимедиа позволяет наложить изображение на существующее видео. В настоящее время поддерживаются следующие форматы: png, jpg, gif и bmp.

## <a name="prerequisites"></a>Предварительные требования

* Собирайте сведения об учетной записи, необходимые для настройки *appsettings.jsдля* файла в примере. Если вы не знаете, как это сделать, см. раздел [Краткое руководство по регистрации приложения на платформе Microsoft Identity](../../active-directory/develop/quickstart-register-app.md). В *appsettings.jsдля* файла ожидались следующие значения.

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
* См. [справочный документ преобразования](/rest/api/media/transforms).

Когда вы знакомы с преобразованиями, Скачайте пример наложения.

## <a name="overlays-preset-sample"></a>Образец предустановки наложения

Скачайте [образец мультимедиа-Services-Overlay](https://github.com/Azure-Samples/media-services-overlays) , чтобы начать работу с наложением.

## <a name="next-steps"></a>Дальнейшие действия

* [Подрезать видео при кодировании с помощью служб мультимедиа — .NET](subclip-video-dotnet-howto.md)