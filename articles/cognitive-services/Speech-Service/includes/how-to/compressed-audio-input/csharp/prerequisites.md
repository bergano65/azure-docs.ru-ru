---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 64eab53d24a14bb51633be3f979db20963677d93
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "81422087"
---
Обработка сжатого аудио-сигнала реализуется с помощью [гстреамер](https://gstreamer.freedesktop.org). По соображениям лицензирования двоичные файлы Гстреамер не компилируются и не связываются с пакетом SDK для распознавания речи. Разработчикам необходимо установить несколько зависимостей и подключаемых модулей, см. раздел [Установка в Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c). Двоичные файлы гстреамер должны находиться в системном пути, чтобы пакет SDK для распознавания речи мог загружать двоичные файлы гстреамер во время выполнения. Если речевой пакет SDK может найти libgstreamer-1.0-0.dll во время выполнения, это означает, что двоичные файлы гстреамер находятся в системном пути.

