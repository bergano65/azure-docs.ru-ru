---
title: Потоковая передача медиаслужб Azure
description: Узнайте, как перейти на вторичную учетную запись Media Services, если происходит сбой или сбой в работе регионального центра обработки данных.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: 1492dd392eabc4331f8e3d4604fb245a89dedff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78899234"
---
# <a name="media-services-high-availability-streaming"></a>Медиа-услуги высокой доступности потокового

В настоящее время медиаслужба Azure не обеспечивает мгновенного сбоя службы при сбое в работе регионального центра обработки данных или сбое в работе базовых или зависимых служб. В этой статье дается руководство, как построить потоковое видео по запросу.

## <a name="prerequisites"></a>Предварительные требования

Обзор [Как построить межрегиональную систему кодирования](media-services-high-availability-encoding.md)

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>Как построить видео по требованию кросс-регион потокового 

* Потоковое воспроизведение потоков по перекрестному региону видео по требованию включает дублирование [активов,](assets-concept.md) [политик и ключов содержимого](content-key-policy-concept.md) (при использовании), [политик потоковой передачи](streaming-policy-concept.md)и [потоковых локаторов.](streaming-locators-concept.md) 
* Вам придется создавать политики в обоих регионах и держать их в актуальном состоянии. 
* При создании потоковых локаторов необходимо использовать то же значение Locator ID, значение ContentKey ID и значение ContentKey.  
* Если вы кодируете содержимое, рекомендуется кодировать содержимое в области А и публиковать его, а затем скопировать закодированное содержимое в область B и опубликовать его с использованием тех же значений, что и из региона А.
* Вы можете использовать диспетчер трафика на именах хоста для происхождения и службы доставки ключей (в конфигурации Media Services это будет выглядеть как пользовательский URL-адрес сервера ключей).

## <a name="next-steps"></a>Дальнейшие действия

Извлечь:

* [Учебник: Кодирование удаленного файла на основе URL и потоковое видео](stream-files-dotnet-quickstart.md)
* [образцы кода](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
