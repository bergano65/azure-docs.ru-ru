---
title: включить файл
description: включить файл
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 60a62733a17d1a3dcc4ba80ed7ceb1c37c8ac5d6
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876726"
---
## <a name="create-the-azure-iot-edge-gateway-device"></a>Создание устройства шлюза Azure IoT Edge

Приложение видеоаналитики для распознавания объектов и движения включает в себя шаблон устройства **детектора объектов LVA Edge**, а также шаблон **обнаружения движения LVA Edge**. В этом разделе вы создадите шаблон устройства шлюза с помощью манифеста развертывания и добавите устройство шлюза в приложение IoT Central.

### <a name="create-a-device-template-for-the-lva-edge-gateway"></a>Создание шаблона устройства для шлюза LVA Edge

Чтобы импортировать манифест развертывания и создать шаблон устройства **шлюза LVA Edge**, сделайте следующее:

1. В приложении IoT Central перейдите в раздел **Шаблоны устройств** и щелкните **+ Создать**.

1. На странице **Select template type** (Выбрать тип шаблона) выберите плитку **Azure IoT Edge**. Затем щелкните **Next: Настройка**.

1. На странице **Upload an Azure IoT Edge deployment manifest** (Отправка манифеста развертывания Azure IoT Edge) введите в качестве имени шаблона *шлюз LVA Edge* и установите флажок **Gateway device with downstream devices** (Устройство шлюза с подчиненными устройствами).

    Пока не выполняйте поиск манифеста развертывания. Если это сделать, мастер развертывания будет ожидать интерфейс для каждого модуля, но вам нужно предоставить интерфейс только для **LvaEdgeGatewayModule**. Вы отправите манифест позже.

    :::image type="content" source="./media/iot-central-video-analytics-part3/upload-deployment-manifest.png" alt-text="Не отправляйте манифест развертывания":::

    По завершении выберите **Next: Review** (Далее: проверка).

1. На странице **Отзыв** выберите **Создать**.

### <a name="import-the-device-capability-model"></a>Импорт модели возможностей устройства

В каждом шаблоне устройства должна быть модель возможностей устройства. На странице **шлюза LVA Edge** выберите **Импорт модели возможностей**. Перейдите к папке *lva-configuration*, созданной ранее, и выберите файл *LvaEdgeGatewayDcm.json*.

Шаблон устройства **шлюза LVA Edge** теперь включает в себя **модуль шлюза LVA Edge**, а также три интерфейса: **Сведения об устройстве**, **параметры шлюза LVA Edge** и **интерфейс шлюза LVA Edge**.
