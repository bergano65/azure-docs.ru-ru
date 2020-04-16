---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 64eab53d24a14bb51633be3f979db20963677d93
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422087"
---
Обработка сжатого звука осуществляется с помощью [GStreamer.](https://gstreamer.freedesktop.org) По причинам лицензирования бинарные файлы GStreamer не компилируются и не связаны с Speech SDK. Разработчикам необходимо установить несколько зависимостей и плагинов, [см. Установка на Windows.](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) Бинарные файлы Gstreamer должны находиться в системе, так что речевой SDK может загружать бинарные файлы gstreamer во время выполнения. Если речь SDK в состоянии найти libgstreamer-1.0-0.dll во время выполнения это означает, что gstreamer бинарные находятся в системе пути.

