---
title: Службы связи Azure — известные проблемы
description: Сведения об известных проблемах со службами связи Azure
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 10/03/2020
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: e9e4b747d9d0ab39a1d0ecef6cf45e4cc0f9e2c5
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628161"
---
# <a name="known-issues-azure-communication-services"></a>Известные проблемы: службы связи Azure

В этой статье содержатся сведения об известных проблемах, связанных со службами связи Azure.

## <a name="video-streaming-quality-on-chromeandroid"></a>Качество потоковой передачи видео на Chrome/Android 

Производительность потоковой передачи видео может снижаться на Chrome Android.

### <a name="possible-causes"></a>Возможные причины
Качество удаленных потоков зависит от разрешения модуля подготовки клиента, который использовался для инициирования этого потока. При подписке на удаленный поток получатель определит собственное разрешение на основе измерений модуля подготовки отправителя на стороне клиента.

## <a name="bluetooth-headset-microphones-are-not-detected"></a>Микрофоны гарнитуры Bluetooth не обнаружены

При подключении гарнитуры Bluetooth к вызову служб связи могут возникнуть проблемы.

### <a name="possible-causes"></a>Возможные причины
В iOS нет возможности выбрать микрофон Bluetooth.


## <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>Многократное переключение видеоустройств может привести к временной пересбою потоковой передачи видео

Переключение между видеоустройствами может привести к тому, что поток видео будет приостановлен, пока поток получен от выбранного устройства.

### <a name="possible-causes"></a>Возможные причины
Потоковая передача и переключение между устройствами мультимедиа осуществляется с высокой интенсивностью вычислений. Частое переключение может привести к снижению производительности. Разработчикам рекомендуется прерывать один поток устройства перед началом другого.
