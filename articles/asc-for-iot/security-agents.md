---
title: Приступая к работе с центром безопасности Azure для агентов безопасности IoT | Документация Майкрософт
description: Приступая к работе с центром безопасности Azure для агентов безопасности IoT.
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
ms.openlocfilehash: 6e790c125c59aea85f1ac34240c5a1d1969544ae
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68600596"
---
# <a name="get-started-with-azure-security-center-for-iot-device-security-agents"></a>Приступая к работе с центром безопасности Azure для агентов безопасности устройств IoT

Центр безопасности Azure для агентов безопасности IoT предлагает расширенные возможности безопасности, такие как мониторинг удаленных подключений, активных приложений, событий входа в систему и рекомендации по настройке операционной системы. Управление защитой угроз в поле устройства и обеспечение безопасности с помощью одной службы. 

Эталонная архитектура для агентов безопасности Linux и Windows предоставляется как C# в, так и в C.

Центр безопасности Azure для агентов безопасности IoT обрабатывает сбор необработанных событий из операционной системы устройства, агрегирование событий для снижения затрат и настройку с помощью модуля устройства двойника. Сообщения безопасности отправляются через центр Интернета вещей в центре безопасности Azure для служб аналитики IoT.

Используйте следующий рабочий процесс для развертывания и тестирования центра безопасности Azure для агентов безопасности IoT: 

1. [Включение центра безопасности Azure для службы IoT в центр Интернета вещей](quickstart-onboard-iot-hub.md)
1. Если в центре Интернета вещей нет зарегистрированных устройств, [Зарегистрируйте новое устройство](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Создайте модуль безопасности азуреиотсекурити](quickstart-create-security-twin.md) для устройств.
1. Чтобы установить агент на виртуальном устройстве Azure, а не при установке на реальном устройстве, следует выполнить [Запуск новой виртуальной машины Azure](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) в доступной зоне. 
1. [Разверните центр безопасности Azure для агента безопасности IOT](how-to-deploy-linux-cs.md) на устройстве IOT или на новой виртуальной машине.
1. Следуйте инструкциям по [trigger_events](https://aka.ms/iot-security-github-trigger-events) , чтобы запустить безвредную имитацию атаки.
1. Проверьте центр безопасности Azure на наличие оповещений IoT в ответ на имитацию атаки на предыдущем шаге. Начните проверку через пять минут после выполнения скрипта.
1. Ознакомьтесь [с оповещениями](concept-security-alerts.md), [рекомендациями](concept-recommendations.md) и подробным обзором [с помощью Log Analytics](how-to-security-data-access.md) с помощью центра Интернета вещей. 


## <a name="next-steps"></a>Следующие шаги

- Настройка [решения](quickstart-configure-your-solution.md)
- [Quickstart: Create an azureiotsecurity module twin](quickstart-create-security-twin.md) (Краткое руководство по созданию модуля двойника устройства azureiotsecurity)
- Настройка [пользовательских оповещений](quickstart-create-custom-alerts.md)
- [Развертывание агента безопасности](how-to-deploy-agent.md)
