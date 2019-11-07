---
title: Подключение приложения IoT Central Azure к службам Dynamics 365 Field Service | Документация Майкрософт
description: Узнайте, как создать комплексное решение с помощью Azure IoT Central и службы Dynamics 365 Field Service.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 957e9337bf8ea5941b140ba4f3266417d36df6a7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498763"
---
# <a name="build-end-to-end-solution-with-azure-iot-central-and-dynamics-365-field-service"></a>Создание комплексного решения с помощью Azure IoT Central и службы Dynamics 365 Field Service 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Создатель решения может включить интеграцию приложения IoT Central Azure с другими бизнес-системами. 


Например, в подключенном решении для управления отходами можно оптимизировать отправку грузовиков корзины на основе данных датчиков IoT из подключенных мусорных контейнеров. В [подключенном к IoT Central приложении для управления отходами](./tutorial-connected-waste-management.md) можно настроить правила и действия и настроить его для активации создания предупреждений в службе Dynamics Field Service. Этот сценарий выполняется с помощью Microsoft Flow, который можно настроить непосредственно в IoT Central для автоматизации рабочих процессов в приложениях и службах. Кроме того, на основе действий службы в Field Service данные можно отправить обратно в Azure IoT Central. 

## <a name="how-to-connect-your-azure-iot-central-application-with-dynamics-365-field-services"></a>Подключение приложения IoT Central Azure к службам Dynamics 365 Field Service 

Приведенные ниже комплексные процессы интеграции можно легко реализовать, обладая опытом настройки с самого начала.
* Azure IoT Central может отправлять сведения об аномалиях устройств в подключенную службу Field Service (как оповещение IoT) для диагностики.
* Подключенная служба Field Service может создавать сценарии или заказы на работу, активируемые аномалиями устройств.
* Подключенная служба Field Service может запланировать визит специалистов для осмотра, чтобы предотвратить простои.
* Панель мониторинга устройств IoT Central Azure можно обновить с помощью соответствующей службы и сведений о расписании.


## <a name="next-steps"></a>Дополнительная информация
* Узнайте больше о [шаблонах IoT Central для государственных организаций](./overview-iot-central-government.md).
* Дополнительные сведения об [IoT Central](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)
* Дополнительные сведения о службах [Dynamics 365 Field Service](https://docs.microsoft.com/dynamics365/field-service/cfs-iot-overview)
