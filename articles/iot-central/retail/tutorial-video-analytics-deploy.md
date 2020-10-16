---
title: Развертывание шаблона "аналитика видео — объект и обнаружение движения" Azure IoT Central шаблон приложения
description: В этом руководстве кратко описаны шаги по развертыванию приложения IoT Central Azure с помощью шаблона приложения Video Analytics — объект и приложение обнаружения движения.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: how-to
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: decfa7020be7778e8ca64a9fb0cb4aac1657da27
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91873342"
---
# <a name="how-to-deploy-an-iot-central-application-using-the-video-analytics---object-and-motion-detection-application-template"></a>Развертывание IoT Central приложения с помощью шаблона приложения "аналитика видео — объект и приложение обнаружения движения"

Общие сведения о компонентах приложения "основные *видео-аналитики — объект и обнаружение движения* " см. в разделе [архитектура приложения для обнаружения объектов и](architecture-video-analytics.md)перемещений.

В следующем видео представлено пошаговое руководство по использованию _шаблона приложения Video Analytics — объект и приложение обнаружения движения_ для развертывания IOT Centralного решения.

> [!VIDEO https://www.youtube.com/embed/Bo3FziU9bSA]

## <a name="deploy-the-application"></a>Развертывание приложения

Выполните следующие действия, чтобы развернуть приложение IoT Central с помощью шаблона приложения Video Analytics.

1. Заполните [приложение "Создание видео-аналитики" в azure IOT Central (Йоло v3)](tutorial-video-analytics-create-app-yolo-v3.md) или в учебнике [Создание видео-аналитики в Azure IOT Central (опенвино &trade; )](tutorial-video-analytics-create-app-openvino.md) , чтобы:
    - Создание учетной записи служб мультимедиа Azure;
    - Создайте приложение IoT Central из шаблона приложения "аналитика видео — объект и приложение обнаружения движения".
    - Настройте устройство шлюза в приложении IoT Central. Шлюз позволяет устройствам камеры подключаться к приложению.

1. Выполните инструкции из руководства [Создание экземпляра IOT Edge для аналитики видео (виртуальная машина Linux)](tutorial-video-analytics-iot-edge-vm.md) или [руководство по созданию экземпляра IOT Edge для аналитики видео (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md) :
    - Создайте виртуальную машину Azure с установленной средой выполнения Azure IoT Edge. Подготовьте IoT Edgeную установку для размещения модуля Video Analytics.
    - Подключите устройство IoT Edge к приложению IoT Central.

1. Выполните инструкции из руководства [мониторинг и управление приложением Video Analytics](tutorial-video-analytics-manage.md) , чтобы:
    - Добавление камер обнаружения объектов и движения в шлюз в приложении IoT Central.
    - Запустите обработку камеры.
    - Установите локальный проигрыватель мультимедиа для просмотра захваченного видео в AMS.
    - Просмотр захваченного видео, показывающего обнаруженные объекты.
    - Очистка.

## <a name="next-steps"></a>Дальнейшие шаги

Теперь у вас есть обзор действий по развертыванию и использованию шаблона приложения Video Analytics. см. статью [Создание приложения Video Analytics в azure IOT Central (Йоло v3)](tutorial-video-analytics-create-app-yolo-v3.md) или [Создание видео-аналитики в Azure IOT Central (опенвино &trade; )](tutorial-video-analytics-create-app-openvino.md) , чтобы приступить к работе.
