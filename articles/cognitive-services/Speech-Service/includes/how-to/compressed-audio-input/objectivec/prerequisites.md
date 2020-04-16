---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 7106e139108681e1908b20d2daac5e619a63555d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422026"
---
Обработка сжатого звука осуществляется с помощью [GStreamer.](https://gstreamer.freedesktop.org) По причинам лицензирования бинарные файлы GStreamer не компилируются и не связаны с Speech SDK. Вместо этого, обертка библиотека, содержащая эти функции должны быть построены и отправлены с приложениями с использованием SDK.

Чтобы построить эту библиотеку обертки, сначала загрузите и установите [GStreamer SDK.](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg) Затем загрузите проект **Xcode** для [библиотеки обертки.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper)

Откройте проект в **Xcode** и создайте его для целевого показателя **общего устройства iOS** - он *не* будет работать, чтобы построить его для конкретной цели.

Шаг сборки создаст динамический рамочный пакет с динамической `GStreamerWrapper.framework`библиотекой для всех необходимых архитектур с названием .

Эта платформа должна быть включена во все приложения, которые используют сжатые аудио потоки с помощью службы speech SDK.

Для этого приложите следующие параметры в проекте **Xcode:**

1. Копируйте `GStreamerWrapper.framework` только что построенный вами и рамки Cognitive Services Speech SDK, который вы можете скачать [отсюда,](https://aka.ms/csspeech/iosbinary)в каталог, содержащий ваш образец проекта.
1. Отрегулируйте пути к настройкам *проекта.*
   1. В **общей** вкладке под **заголовком Встроенные файлы в** добавленные файлы добавьте библиотеку SDK в качестве платформы: **Добавьте встроенные файлы** > **добавить другие...** > Перейдите в выбранный каталог и выберите оба фреймворка.
   1. Перейдите на вкладку **Build Settings** (Параметры сборки) и активируйте **Все** параметры.
1. Добавьте каталог `$(SRCROOT)/..` в _Пути поиска платформы_ под заголовком **Пути поиска**.
