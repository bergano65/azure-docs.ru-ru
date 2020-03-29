---
title: Получение пакета SDK для устройств, подключаемых к службе "Речь"
titleSuffix: Azure Cognitive Services
description: Служба речевой речи работает с широким спектром устройств и аудиоисточников. Теперь ваши речевые приложения могут стать еще эффективнее благодаря соответствующему оборудованию и программному обеспечению. В этой статье вы узнаете, как получить доступ к Речевому устройству SDK и начать разработку.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: f8c1500dbbd9135a850e145199de8fea68cc4630
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220537"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Получение пакета SDK для речевых устройств в Cognitive Services

DDK Speech Devices — это предварительно настроенная библиотека, предназначенная для работы с специально созданными комплектами разработки и различными конфигурациями микрофонных массивов.

## <a name="choose-a-development-kit"></a>Выберите комплект разработки

|Устройства|Спецификация|Описание|Сценарии|
|--|--|--|--|
|[Roobo Smart Аудио Dev Kit](https://ddk.roobo.com)<br>[Настройка](speech-devices-sdk-roobo-v1.md) / [Быстрый старт](speech-devices-sdk-android-quickstart.md)![Roobo Smart Аудио Dev Kit](media/speech-devices-sdk/device-roobo-v1.jpg)|7 Mic Array, ARM SOC, WIFI, Audio Out, IO. <br>[Android](speech-devices-sdk-android-quickstart.md)|Первые речевые устройства SDK для адаптации Microsoft Mic Array и передние обработки SDK, для разработки высококачественных транскрипции и речевых сценариев|Разговор Транскрипция, умный динамик, голосовой агент, Носители|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)<br>[Настройка](https://docs.microsoft.com/azure/Kinect-dk/set-up-azure-kinect-dk) / [Быстрый запуск](speech-devices-sdk-windows-quickstart.md)![Azure Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 микрофонных RGB и глубинных камер. <br>[Windows](speech-devices-sdk-windows-quickstart.md)/[Linux](speech-devices-sdk-linux-quickstart.md)|Комплект разработчика с передовыми датчиками искусственного интеллекта (ИИ) для создания сложных моделей компьютерного зрения и речи. Он сочетает в себе лучший в своем классе массив пространственного микрофона и глубину камеры с видеокамерой и датчиком ориентации - все в одном небольшом устройстве с несколькими режимами, опциями и SDK для размещения целого ряда типов вычислений.|Разговор транскрипция, Робототехника, Умное здание|
|Roobo Smart Аудио Dev Kit 2<br>[Установки](speech-devices-sdk-roobo-v2.md)<br>![Roobo Smart Аудио Dev Kit 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 Mic Array, ARM SOC, WIFI, Bluetooth, IO. <br>Linux|2-го поколения речевых устройств SDK, который предоставляет альтернативные ОС и больше функций в экономически эффективной разработки ссылки.|Разговор Транскрипция, умный динамик, голосовой агент, Носители|
|Совет по развитию URbetter T11![URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 Mic Array, ARM SOC, WIFI, Ethernet, HDMI, USB Camera. <br>Linux|Отраслевой уровень Речевых устройств SDK, который адаптирует массив Microsoft Mic и поддерживает расширенный ввод/вывод, такие как HDMI/Ethernet и другие периферийные устройства USB|Разговор Транскрипция, Образование, Больница, Роботы, OTT Box, Голосовой агент, Drive Thru|

## <a name="download-the-speech-devices-sdk"></a>Загрузка пакета SDK для речевых устройств

Скачать [Речевые устройства SDK](https://aka.ms/sdsdk-download).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Начало работы с пакетом SDK для речевых устройств](https://aka.ms/sdsdk-quickstart)
