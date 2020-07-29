---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: amishu
ms.openlocfilehash: a715d0ece88cf2caf1cb3d20d703f550353094c5
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282753"
---
Обработка сжатого аудио-сигнала реализуется с помощью [`GStreamer`](https://gstreamer.freedesktop.org) . Из соображений лицензирования `GStreamer` двоичные файлы не компилируются и не связываются с пакетом SDK для распознавания речи. Разработчикам необходимо установить несколько зависимостей и подключаемых модулей, см. раздел [Установка в Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c). `GStreamer`двоичные файлы должны находиться в системном пути, чтобы пакет SDK для распознавания речи мог загружать двоичные файлы во время выполнения. Если речевой пакет SDK может найти `libgstreamer-1.0-0.dll` во время выполнения, это означает, что двоичные файлы находятся в системном пути.

