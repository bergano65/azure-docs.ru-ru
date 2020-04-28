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
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 3ebeb3c601dd3f734265d49d60728056561928be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/27/2020
ms.locfileid: "61127819"
---
# <a name="upload-files-to-a-media-services-account-in-the-azure-portal"></a>Отправка файлов в учетную запись служб мультимедиа на портале Azure 

> [!div class="op_single_selector"]
> * [Портал](media-services-portal-upload-files.md)
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> 

> [!NOTE]
> В Cлужбы мультимедиа версии 2 больше не добавляются новые компоненты или функциональные возможности. <br/>Ознакомьтесь с последней версией [служб мультимедиа v3](https://docs.microsoft.com/azure/media-services/latest/). См. также [руководство по миграции из v2 в версии 3](../latest/migrate-from-v2-to-v3.md) .

В службах мультимедиа Azure цифровые файлы отправляются в ресурс. Ресурс может содержать видео, аудио, изображения, коллекции эскизов, текстовые каналы и файлы скрытых субтитров (и метаданные этих файлов). После отправки этих файлов содержимое сохраняется в безопасном расположении в облаке для дальнейшей обработки и потоковой передачи.

В службах мультимедиа установлен максимальный размер обрабатываемых файлов. Дополнительные сведения об ограничениях на размер файлов см. в статье [Квоты и ограничения](media-services-quotas-and-limitations.md).

Для работы с этим учебником требуется учетная запись Azure. Дополнительные сведения см. в статье [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="upload-files"></a>Отправка файлов
1. Выберите учетную запись служб мультимедиа Azure на [портале Azure](https://portal.azure.com/).
2. Выберите **Параметры** > **ресурсы**. Затем нажмите кнопку **Отправить**.
   
    ![Отправка файлов](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    Появится окно **загрузки видеоресурса** .
   
   > [!NOTE]
   > В службах мультимедиа нет ограничения на размер файла при отправке видео.
 
3. На компьютере перейдите к видео, которое нужно отправить. Выберите видео и нажмите кнопку **ОК**.  
   
    Начнется отправка. Ход отправки отображается под именем файла.  

Когда отправка завершится, в области **Ресурсы-контейнеры** появится новый ресурс. 

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Предоставление отзыва
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте, как [закодировать отправленные ресурсы](media-services-portal-encode.md).

* Вы также можете использовать службу "Функции Azure", чтобы активировать задание кодирования, когда файл поступит в настроенный контейнер. Дополнительные сведения см. в примере в статье [Media Services: Integrating Azure Media Services with Azure Functions and Logic Apps](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/) (Службы мультимедиа: интеграция служб мультимедиа Azure со службой "Функции Azure" и Azure Logic Apps).


