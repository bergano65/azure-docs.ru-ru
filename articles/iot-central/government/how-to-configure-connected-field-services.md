---
title: Подключение приложения IoT Central Azure к службам Dynamics 365 Field Service | Документация Майкрософт
description: Узнайте, как создать комплексное решение с помощью Azure IoT Central и службы Dynamics 365 Field Service.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 7c708268eaeade1cfb625c73ff8758a1941ff5b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157455"
---
# <a name="build-end-to-end-solution-with-azure-iot-central-and-dynamics-365-field-service"></a>Создание комплексного решения с помощью Azure IoT Central и службы Dynamics 365 Field Service 



Создатель решения может включить интеграцию приложения IoT Central Azure с другими бизнес-системами. 


Например, в подключенном решении для утилизации отходов можно оптимизировать отправку грузовиков для уборки мусора. Оптимизация может быть проведена на основе данных датчиков IoT из подключенных мусорных баков. В [приложении для управления отходами, подключенном к IoT Central,](./tutorial-connected-waste-management.md) вы можете настроить правила и действия и настроить его для запуска оповещений в полевой службе Dynamics. Этот сценарий выполняется с помощью решения Microsoft Flow, которое будет настроено непосредственно в IoT Central для автоматизации рабочих процессов в приложениях и службах. Кроме того, на основе действий службы в Field Service данные можно отправить обратно в Azure IoT Central. 

## <a name="how-to-connect-your-azure-iot-central-application-with-dynamics-365-field-services"></a>Подключение приложения IoT Central Azure к службам Dynamics 365 Field Service 

Описанные ниже процессы интеграции можно легко реализовать, обладая опытом настройки с самого начала:
* Azure IoT Central может отправлять сведения об аномалиях устройств в подключенную службу Field Service (как оповещение IoT) для диагностики.
* Подключенная служба Field Service может создавать сценарии или заказы на работу, активируемые аномалиями устройств.
* Подключенная служба Field Service может запланировать визит специалистов для осмотра, чтобы предотвратить простои.
* Панель мониторинга устройств IoT Central Azure можно обновить с помощью соответствующей службы и сведений о расписании.


## <a name="next-steps"></a>Дальнейшие действия
* Узнайте больше о [шаблонах IoT Central для государственных организаций](./overview-iot-central-government.md).
* Дополнительные сведения об [IoT Central](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)
* Дополнительные сведения о службах [Dynamics 365 Field Service](https://docs.microsoft.com/dynamics365/field-service/cfs-iot-overview)
