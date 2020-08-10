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
ms.openlocfilehash: d2ab93708950743eafdb6cf733273b602cfb5a68
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88038281"
---
# <a name="how-to-deploy-an-iot-central-application-using-the-video-analytics---object-and-motion-detection-application-template"></a>Развертывание IoT Central приложения с помощью шаблона приложения "аналитика видео — объект и приложение обнаружения движения"

Общие сведения о компонентах приложения "основные *видео-аналитики — объект и обнаружение движения* " см. в разделе [архитектура приложения для обнаружения объектов и](architecture-video-analytics.md)перемещений.

## <a name="deploy-the-application"></a>Развертывание приложения

Выполните следующие действия, чтобы развернуть приложение IoT Central с помощью шаблона приложения Video Analytics.

1. Выполните инструкции из руководства [Создание видео-аналитики в Azure IOT Central](tutorial-video-analytics-create-app.md) .
    - Создание учетной записи служб мультимедиа Azure;
    - Создайте приложение IoT Central из шаблона приложения "аналитика видео — объект и приложение обнаружения движения".
    - Настройте устройство шлюза в приложении IoT Central. Шлюз позволяет устройствам камеры подключаться к приложению.

1. Выполните инструкции из руководства [Создание экземпляра IOT Edge для аналитики видео (Linux)](tutorial-video-analytics-iot-edge-vm.md) :
    - Создайте виртуальную машину Azure с установленной средой выполнения Azure IoT Edge. Подготовьте IoT Edgeную установку для размещения модуля Video Analytics.
    - Подключите устройство IoT Edge к приложению IoT Central.

1. Выполните инструкции из руководства [мониторинг и управление приложением Video Analytics](tutorial-video-analytics-manage.md) , чтобы:
    - Добавление камер обнаружения объектов и движения в шлюз в приложении IoT Central.
    - Запустите обработку камеры.
    - Установите локальный проигрыватель мультимедиа для просмотра захваченного видео в AMS.
    - Просмотр захваченного видео, показывающего обнаруженные объекты.
    - Очистка.

## <a name="next-steps"></a>Дальнейшие действия

Теперь у вас есть обзор действий по развертыванию и использованию шаблона приложения Video Analytics. чтобы начать работу, см. статью [Создание приложения Video Analytics n в IOT Central Azure](tutorial-video-analytics-create-app.md) .
