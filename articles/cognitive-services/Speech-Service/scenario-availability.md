---
title: Доступность сценария — служба речи
titleSuffix: Azure Cognitive Services
description: Справочник по регионам службы "Речь".
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: cbasoglu
ms.openlocfilehash: 6ec31df7cef8391728eae7845f64f55bb1c6466a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491348"
---
# <a name="scenario-availability"></a>Доступность сценариев

В пакете SDK службы речевых функций реализовано множество сценариев в различных языках программирования и средах.  Не все сценарии сейчас доступны на всех языках программирования или в всех средах.  Ниже приведена доступность каждого сценария.

- **Распознавание речи (SR), список фраз, намерения, перевод и локальные контейнеры**
  - Все языки программирования и среды, в которых есть ссылка со стрелкой <img src="media/index/link.jpg" height="15" width="15"></img> в таблице краткого руководства [здесь](https://aka.ms/csspeech).
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
- **Речевые помощники**
  - C++/Виндовс & Linux & macOS
  - C#/виндовс
  - Java/Windows & Linux & macOS & Android (пакет SDK для речевых устройств)
- **Транскрибирование бесед**
  - C++/Виндовс & Linux
  - C#(Платформа & .NET Core)/Виндовс & UWP & Linux
  - Java и Windows & Linux & Android (пакет SDK для речевых устройств)
- **Транскрипция центра обработки вызовов**
  - REST API и могут использоваться в любой ситуации.
- **Звуковые входные данные, сжатые кодеком**
  - C++/линукс
  - C#/линукс
  - Java, Linux, Android и iOS
