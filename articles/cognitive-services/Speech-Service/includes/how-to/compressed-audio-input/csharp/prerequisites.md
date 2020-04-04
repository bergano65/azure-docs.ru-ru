---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 07405ad0da9c9ba280810402a638395a7feb8554
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637291"
---
Обработка сжатого звука осуществляется с помощью [GStreamer.](https://gstreamer.freedesktop.org) По причинам лицензирования бинарные файлы GStreamer не компилируются и не связаны с Speech SDK. Разработчикам необходимо установить несколько зависимостей и плагинов, [см. Установка на Windows.](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) Бинарные файлы Gstreamer должны находиться в системе, так что речевой SDK может загружать бинарные файлы gstreamer во время выполнения. Если речь SDK в состоянии найти libgstreamer-1.0-0.dll во время выполнения это означает, что gstreamer бинарные находятся в системе пути.

