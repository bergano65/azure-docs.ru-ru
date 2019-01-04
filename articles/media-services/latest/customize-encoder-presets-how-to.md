---
title: Кодирование пользовательского преобразования с помощью Службы мультимедиа Azure версии 3 — Azure | Документация Майкрософт
description: В этом разделе показано, как использовать Службы мультимедиа Azure версии 3 для кодирования пользовательского преобразования.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 12/08/2018
ms.author: juliako
ms.openlocfilehash: c62d9132cdd7eb2ebcbecc3c417ad30d368a278a
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138710"
---
# <a name="how-to-encode-with-a-custom-transform"></a>Кодирование с помощью пользовательского преобразования

При кодировании с помощью Служб мультимедиа Azure можно быстро приступить к работе с одной из рекомендуемых встроенных предустановок на основе передовых отраслевых рекомендаций, как показано в руководстве по [потоковой передаче файлов](stream-files-tutorial-with-api.md). Кроме того, вы можете создать пользовательскую предустановку в соответствии с требованиями сценария или устройства. 

> [!Note]
> В Службах мультимедиа Azure версии 3 скорость кодировки указана в битах за секунду. Это отличается от предустановок Media Encoder Standard REST версии 2. Например, скорость в версии 2 указывается как 128, а в версии 3 она имела бы значение 128 000.

## <a name="download-the-sample"></a>Скачивание примера приложения

Клонируйте репозиторий GitHub, содержащий пример .NET Core, на компьютер с помощью следующей команды:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
Пример настраиваемой предустановки находится в папке [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/).

## <a name="create-a-transform-with-a-custom-preset"></a>Создание преобразования с помощью настраиваемой предустановки 

При создании [преобразования](https://docs.microsoft.com/rest/api/media/transforms) необходимо указать, что требуется создать в качестве выходных данных. Обязательный параметр — это объект **TransformOutput**, как показано в приведенном ниже примере кода. Каждый объект **TransformOutput** содержит **предустановку** (Preset). **Предустановка** описывает пошаговые инструкции для операций обработки видео и звука, которые будут использоваться для создания нужного объекта **TransformOutput**. Следующая предустановка **TransformOutput** создает настраиваемый кодек и параметры слоя вывода.

При создании [преобразования](https://docs.microsoft.com/rest/api/media/transforms) сначала проверьте, существует ли оно, с помощью метода **Get**, как показано в следующем коде.  В Службах мультимедиа версии 3 методы **Get**, отправленные к сущностям, возвращают значение **NULL**, если сущность не существует (проверка по имени без учета регистра).

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Дополнительная информация

[Файлы потоковой передачи](stream-files-tutorial-with-api.md) 
