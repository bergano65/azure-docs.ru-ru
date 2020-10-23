---
title: Подключение приложения IoT Central Azure к службам Dynamics 365 Field Service | Документация Майкрософт
description: Узнайте, как создать комплексное решение с помощью Azure IoT Central и службы Dynamics 365 Field Service.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 2b3e006f717ed1c66c7db29dbd70c226d2d75ea8
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92127222"
---
# <a name="build-end-to-end-solution-with-azure-iot-central-and-dynamics-365-field-service"></a>Создание комплексного решения с помощью Azure IoT Central и службы Dynamics 365 Field Service 



Создатель решения может включить интеграцию приложения IoT Central Azure с другими бизнес-системами. 


Например, в подключенном решении для утилизации отходов можно оптимизировать отправку грузовиков для уборки мусора. Оптимизация может быть выполнена на основе данных датчиков IoT из подключенных ячеек отхода. В [IOT Central подключенном приложении управления отходами](./tutorial-connected-waste-management.md) можно настроить правила и действия и настроить их для активации создания предупреждений в службе полей Dynamics. Этот сценарий выполняется с помощью решения Microsoft Flow, которое будет настроено непосредственно в IoT Central для автоматизации рабочих процессов в приложениях и службах. Кроме того, на основе действий службы в Field Service данные можно отправить обратно в Azure IoT Central. 

## <a name="how-to-connect-your-azure-iot-central-application-with-dynamics-365-field-services"></a>Подключение приложения IoT Central Azure к службам Dynamics 365 Field Service 

Описанные ниже процессы интеграции можно легко реализовать, обладая опытом настройки с самого начала:
* Azure IoT Central может отправлять сведения об аномалиях устройств в подключенную службу Field Service (как оповещение IoT) для диагностики.
* Подключенная служба Field Service может создавать сценарии или заказы на работу, активируемые аномалиями устройств.
* Подключенная служба Field Service может запланировать визит специалистов для осмотра, чтобы предотвратить простои.
* Панель мониторинга устройств IoT Central Azure можно обновить с помощью соответствующей службы и сведений о расписании.


## <a name="next-steps"></a>Дальнейшие шаги
* Узнайте больше о [шаблонах IoT Central для государственных организаций](./overview-iot-central-government.md).
* Дополнительные сведения об [IoT Central](../core/overview-iot-central.md)
* Дополнительные сведения о службах [Dynamics 365 Field Service](/dynamics365/field-service/cfs-iot-overview)