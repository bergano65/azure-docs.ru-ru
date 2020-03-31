---
title: Понять Центр безопасности Azure для близнецов модулей модулей безопасности IoT Документы Майкрософт
description: Узнайте о концепции близнецов модулей безопасности и о том, как они используются в Центре безопасности Azure для IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: ab3b6e740e644a1ed1495eb776045888be448047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68596503"
---
# <a name="security-module"></a>Модуль безопасности


В этой статье объясняется, как Центр безопасности Azure для IoT использует близнецы и модули устройств. 

## <a name="device-twins"></a>Двойники устройства

Для созданных в Azure решений Интернета вещей двойники устройств играют ключевую роль в управлении устройствами и автоматизации процессов.  

Центр безопасности Azure для Интернета вещей обеспечивает полную интеграцию с платформой управления устройством Интернета вещей, позволяя управлять состоянием защиты устройства и использовать существующие возможности управления устройства. Интеграция достигается за счет использования двойного механизма IoT Hub.  

Узнайте больше о концепции [близнецов устройств](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) в Azure IoT Hub. 

## <a name="security-module-twins"></a>Близнецы модулей безопасности

Центр безопасности Azure для IoT поддерживает модуль безопасности для каждого устройства в службе.
Модуль безопасности twin содержит всю информацию, относящуюся к безопасности устройства для каждого конкретного устройства в вашем решении.
Свойства безопасности устройств поддерживаются в специальном модуле безопасности twin для более безопасной связи и для включения обновлений и технического обслуживания, что требует меньше ресурсов.  

Смотрите [Создать модуль безопасности twin](quickstart-create-security-twin.md) и настроить [агенты безопасности,](how-to-agent-configuration.md) чтобы узнать, как создавать, настраивать и настраивать близнеца. Смотрите [Понимание модуль близнецов,](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) чтобы узнать больше о концепции модуля близнецов в IoT концентратор. 
 

## <a name="see-also"></a>См. также
- [Центр безопасности Azure для обзора IoT](overview.md)
- [Развертывание агентов безопасности](how-to-deploy-agent.md)
- [Методы аутентификации агента безопасности](concept-security-agent-authentication-methods.md)