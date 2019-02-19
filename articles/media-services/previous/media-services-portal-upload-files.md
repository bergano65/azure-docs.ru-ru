---
title: Отправка файлов в учетную запись служб мультимедиа на портале Azure | Документация Майкрософт
description: В этом руководстве описываются действия по отправке файлов в учетную запись служб мультимедиа на портале Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 3ad3dcea-95be-4711-9aae-a455a32434f6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: 7730d15b0d3be9c0ffc7c7fbafe40fece713afbd
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55976798"
---
# <a name="upload-files-to-a-media-services-account-in-the-azure-portal-legacy"></a>Отправка файлов в учетную запись Служб мультимедиа на портале Azure (для прежних версий)

> [!div class="op_single_selector"]
> * [Портал](media-services-portal-upload-files.md)
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> 
> [!NOTE]
> Для работы с этим учебником требуется учетная запись Azure. Дополнительные сведения см. на странице с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 

В службах мультимедиа Azure цифровые файлы отправляются в ресурс. Ресурс может содержать видео, аудио, изображения, коллекции эскизов, текстовые каналы и файлы скрытых субтитров (и метаданные этих файлов). После отправки этих файлов содержимое сохраняется в безопасном расположении в облаке для дальнейшей обработки и потоковой передачи.

> [!NOTE]
> В службах мультимедиа установлен максимальный размер обрабатываемых файлов. Дополнительные сведения об ограничениях на размер файлов см. в статье [Квоты и ограничения](media-services-quotas-and-limitations.md).
>

## <a name="upload-files"></a>Отправка файлов
1. Выберите учетную запись служб мультимедиа Azure на [портале Azure](https://portal.azure.com/).
2. Установите флажок **Параметры** > **Ресурсы-контейнеры**. Затем нажмите кнопку **Отправить**.
   
    ![Отправка файлов](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    Появится окно **загрузки видеоресурса** .
   
   > [!NOTE]
   > В службах мультимедиа нет ограничения на размер файла при отправке видео.
 
3. На компьютере перейдите к видео, которое нужно отправить. Выберите видео и нажмите кнопку **ОК**.  
   
    Начнется отправка. Ход отправки отображается под именем файла.  

Когда отправка завершится, в области **Ресурсы-контейнеры** появится новый ресурс. 

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Дополнительная информация
* Узнайте, как [закодировать отправленные ресурсы](media-services-portal-encode.md).

* Вы также можете использовать службу "Функции Azure", чтобы активировать задание кодирования, когда файл поступит в настроенный контейнер. Дополнительные сведения см. в статье [Media Services: Integrating Azure Media Services with Azure Functions and Logic Apps](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/) (Cлужбы мультимедиа. Интеграция Служб мультимедиа Azure со Службой "Функции Azure" и Logic Apps).


