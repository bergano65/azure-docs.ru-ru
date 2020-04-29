---
title: Доступность сценария — служба речи
titleSuffix: Azure Cognitive Services
description: В пакете SDK для речевых функций реализовано множество сценариев в различных языках программирования и средах. Не все сценарии доступны во всех языках программирования или во всех средах. Ниже приведена доступность каждого сценария.
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: cbasoglu
ms.openlocfilehash: a72e5055a3b0f858a732ec28eeab511b09dd450c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81400146"
---
# <a name="scenario-availability"></a>Доступность сценариев

В пакете SDK для речевых функций реализовано множество сценариев в различных языках программирования и средах. Не все сценарии доступны во всех языках программирования или во всех средах. Ниже приведена доступность каждого сценария.

- **Распознавание речи (SR), список фраз, намерения, перевод и локальные контейнеры**
  - C++/Виндовс & Linux & macOS
  - C# (платформа & .NET Core)/Виндовс & UWP & Unity & Xamarin & Linux & macOS
  - Java (JRE и Android)
  - JavaScript (Брауэр (и NodeJS)
  - Python
  - Swift
  - Objective-C  
- **Преобразование текста в речь (TTS)**
  - C++/Виндовс & Linux
  - C#/Виндовс & UWP & Unity
  - Java (JRE и Android)
  - Python
  - Swift
  - Objective-C
  - REST API TTS можно использовать в любой другой ситуации.
- **Заданное ключевое слово (КВС)**
  - C++/Виндовс & Linux
  - C#/Виндовс & Linux
  - Python и Windows & Linux
  - Java и Windows & Linux & Android (пакет SDK для речевых устройств)
  - Функция поиска ключевых слов (КВС) может работать с любым типом микрофона, официальная поддержка КВС, однако, в настоящее время ограничена только массивами микрофонов, находящихся на оборудовании Azure Kinect DK или в пакете SDK для речевых устройств.
- **Голосовые помощники**
  - C++/Виндовс & Linux & macOS
  - C/Windows
  - Java/Windows & Linux & macOS & Android (пакет SDK для речевых устройств)
- **Транскрибирование бесед**
  - C++/Виндовс & Linux
  - C# (платформа & .NET Core)/Виндовс & UWP & Linux
  - Java и Windows & Linux & Android (пакет SDK для речевых устройств)
- **Ведение беседы с использованием нескольких устройств**
  - /Виндовс C++
  - C# (платформа & .NET Core),/Виндовс
- **Транскрипция центра обработки вызовов**
  - REST API и могут использоваться в любой ситуации.
- **Звуковые входные данные, сжатые кодеком**
  - /Линукс C++
  - /Линукс C#
  - Java, Linux, Android и iOS
