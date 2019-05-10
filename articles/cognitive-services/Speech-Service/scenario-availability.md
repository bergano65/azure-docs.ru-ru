---
title: Сценарии доступности - службы распознавания речи
titlesuffix: Azure Cognitive Services
description: Справочник по регионам службы "Речь".
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cbasoglu
ms.openlocfilehash: d844b171ff99dc97e5d1107bcb745f9e8d5b3e9d
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65519832"
---
# <a name="scenario-availability"></a>Сценарии доступности

Пакет SDK для служб речи функции во многих сценариях различных языков и сред программирования.  Не все сценарии еще настоящее время недоступны на всех языках программирования или ко всем средам.  Ниже приведен доступность каждого сценария.

- **Распознавания речи (SR), списка фраз, цель, перевод и локальных контейнеров**
  - Все программирования языков и сред там, где ссылку со стрелкой <img src="media/index/link.jpg" height="15" width="15"></img> в таблице быстрого запуска [здесь](https://aka.ms/csspeech).
- **Преобразование текста в речь (TTS)**
  - C++/ Windows и Linux
  - C#/ Windows
  - В любой другой ситуации можно использовать REST API преобразования текста в РЕЧЬ.
- **Настроить слово (ключевое слово Spotter/KWS)**
  - C++/ Windows и Linux
  - C#/ Windows и Linux
  - Python/Windows и Linux
  - Java/Windows и Linux, Android (пакет SDK для речевых устройств)
  - Функциональные возможности пробуждения Word (ключевое слово Spotter/KWS) смогут работать с любым типом "микрофон", официальной поддержки KWS, однако в настоящее время ограничен массивы "микрофон" найден в оборудования Azure Kinect DK или пакет SDK для устройств речи
- **Виртуальные помощники Voice-First**
  - C++/ Windows, Linux и macOS
  - C#/ Windows
  - Java/Windows & Linux & macOS и Android (Speech SDK устройств)
- **Транскрибирование бесед**
  - C++/ Windows и Linux
  - C#(Framework и .NET Core) или Windows и универсальной платформы Windows и Linux
  - Java/Windows и Linux, Android (пакет SDK для речевых устройств)
- **Расшифровка дикторского текста для центра обработки вызовов**
  - REST API и могут использоваться в любой ситуации
- **Кодек сжатия аудио вход**
  - C++/ Linux
  - C#/ Linux
  - Java или Linux и Android
