---
title: Начнутесь использовать Центр безопасности Azure для агентов безопасности IoT Документы Майкрософт
description: Начайтесь с пониманием, настройкой, развертыванием и использованием Центра безопасности Azure для агентов службы безопасности IoT на устройствах IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 1ed890d9d3602de24e6f85f6f0ae7f59849f3df2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74664198"
---
# <a name="get-started-with-azure-security-center-for-iot-device-security-agents"></a>Начало работы с Центром безопасности Azure для агентов безопасности устройств IoT

Центр безопасности Azure для агентов безопасности IoT предлагает расширенные возможности безопасности, такие как мониторинг удаленных подключений, активные приложения, события входа и рекомендации конфигурации операционной системы. Возьмите под свой контроль защиту поля устройства и позу безопасности с помощью одной службы. 

Предоставляются справочные архитектуры для агентов безопасности Linux и Windows, как в C,, так и c.

Центр безопасности Azure для агентов безопасности IoT обрабатывает необработанный сбор событий с операционной системы устройства, агрегацию событий для снижения затрат и конфигурацию с помощью двойного модуля устройства. Сообщения безопасности отправляются через концентратор IoT, в Центр безопасности Azure для служб аналитики IoT.

Используйте следующий рабочий процесс для развертывания и тестирования Центра безопасности Azure для агентов безопасности IoT: 

1. [Включите центр безопасности Azure для обслуживания IoT в ваш концентратор IoT](quickstart-onboard-iot-hub.md)
1. Если у вашего Концентратора IoT нет зарегистрированных устройств, [зарегистрируйте новое устройство.](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy)
1. [Создайте модуль безопасности azureiotsecurity](quickstart-create-security-twin.md) для ваших устройств.
1. Чтобы установить агент на устройство, имитирующее Azure вместо установки на фактическое устройство, [закрутите новую виртуальную машину Azure (VM)](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) в доступную зону. 
1. [Развертывание Центра безопасности Azure для агента безопасности IoT](how-to-deploy-linux-cs.md) на устройстве IoT или новом VM.
1. Следуйте инструкциям для [trigger_events,](https://aka.ms/iot-security-github-trigger-events) чтобы запустить безобидную имитацию атаки.
1. Проверка Центра безопасности Azure для оповещений IoT в ответ на смоделированную атаку на предыдущем этапе. Начните проверку через пять минут после запуска скрипта.
1. Изучите [оповещения,](concept-security-alerts.md) [рекомендации](concept-recommendations.md)и [глубокое погружение с помощью Log Analytics](how-to-security-data-access.md) с помощью IoT Hub. 


## <a name="next-steps"></a>Дальнейшие действия

- Налажить [решение](quickstart-configure-your-solution.md)
- [Quickstart: Create an azureiotsecurity module twin](quickstart-create-security-twin.md) (Краткое руководство по созданию модуля двойника устройства azureiotsecurity)
- Настройка [пользовательских оповещений](quickstart-create-custom-alerts.md)
- [Развертывание агента безопасности](how-to-deploy-agent.md)
