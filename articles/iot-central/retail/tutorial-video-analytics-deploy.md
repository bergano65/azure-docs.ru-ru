---
title: Руководство. Развертывание шаблона приложения видеоаналитики для распознавания объектов и движения (Azure IoT Central)
description: Руководство. В этом руководстве показано, как развернуть приложение Azure IoT Central с помощью шаблона приложения видеоаналитики для распознавания объектов и движения.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: abeae674cef8f13a092b53bde6040ad853bd0cca
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820370"
---
# <a name="tutorial-how-to-deploy-an-iot-central-application-using-the-video-analytics---object-and-motion-detection-application-template"></a>Руководство. Развертывание приложения IoT Central с помощью шаблона приложения видеоаналитики для распознавания объектов и движения

Общие сведения о ключевых компонентах приложения *видеоаналитики для распознавания объектов и движения* см. в описании [архитектуры приложения видеоаналитики](architecture-video-analytics.md).

В следующем видео представлено пошаговое руководство по использованию шаблона приложения _видеоаналитики для распознавания объектов и движения_ для развертывания решения IoT Central.

> [!VIDEO https://www.youtube.com/embed/Bo3FziU9bSA]

## <a name="deploy-the-application"></a>Развертывание приложения

Чтобы развернуть приложение IoT Central с помощью шаблона приложения видеоаналитики, сделайте следующее:

1. Выполните инструкции из статьи [Руководство. Создание приложения видеоаналитики для распознавания объектов и движения в Azure IoT Central (YOLO v3)](tutorial-video-analytics-create-app-yolo-v3.md) или [Руководство. Создание приложения видеоаналитики для распознавания объектов и движения в Azure IoT Central (OpenVINO)&trade;](tutorial-video-analytics-create-app-openvino.md), чтобы:
    - Создание учетной записи служб мультимедиа Azure;
    - Создать приложение IoT Central с помощью шаблона приложения "Видеоаналитика — распознавание объектов и движения".
    - Настроить устройство шлюза в приложении IoT Central. Шлюз позволяет устройствам камеры подключаться к приложению.

1. Выполните инструкции из статьи [Руководство. Создание экземпляра IoT Edge для видеоаналитики (виртуальная машина Linux)](tutorial-video-analytics-iot-edge-vm.md) или [Руководство. Создание экземпляра IoT Edge для видеоаналитики (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md), чтобы:
    - Создать виртуальную машину Azure с установленной средой выполнения Azure IoT Edge и подготовить установку IoT Edge для размещения модуля видеоаналитики.
    - Подключить устройство IoT Edge к приложению IoT Central.

1. Выполните инструкции из руководства [Мониторинг приложения для видеоаналитики и управление им](tutorial-video-analytics-manage.md), чтобы:
    - Добавить камеры обнаружения объектов и движения в шлюз в приложении IoT Central.
    - Запустить обработку данных с камеры.
    - Установить локальный проигрыватель мультимедиа для просмотра захваченного видео в AMS.
    - Просмотреть захваченное видео с обнаруженными объектами.
    - Удалить ненужные элементы.

## <a name="next-steps"></a>Следующие шаги

Ознакомившись с шагами, требуемыми для развертывания и использования шаблона приложения видеоаналитики, см. следующие ресурсы:

> [!div class="nextstepaction"]
> [Создание приложения видеоаналитики в Azure IoT Central (YOLO v3)](tutorial-video-analytics-create-app-yolo-v3.md)

> [!div class="nextstepaction"]
> [Создание приложения видеоаналитики в Azure IoT Central (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md)
