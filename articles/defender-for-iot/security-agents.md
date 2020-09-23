---
title: Обзор агента безопасности
description: Приступите к изучению, настройке, развертыванию и использованию защитника Azure для агентов службы безопасности IoT на устройствах IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 888fdbb2352c4ac972eac8b24c29d22ce973e04d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939743"
---
# <a name="get-started-with-azure-defender-for-iot-device-security-agents"></a>Приступая к работе с защитником Azure для агентов безопасности устройств IoT

Защитник для агентов безопасности IoT предлагает расширенные возможности безопасности, такие как мониторинг удаленных подключений, активных приложений, событий входа в систему и рекомендации по настройке операционной системы. Управление защитой угроз в поле устройства и обеспечение безопасности с помощью одной службы.

Эталонная архитектура для агентов безопасности Linux и Windows предоставляется как в C#, так и в C.

Защитник для агентов безопасности IoT обрабатывает сбор необработанных событий из операционной системы устройства, агрегирование событий для снижения затрат и настройку с помощью модуля устройства двойника. Сообщения безопасности отправляются через центр Интернета вещей в защитник для служб аналитики IoT.

Используйте следующий рабочий процесс, чтобы развернуть и проверить защитник для агентов безопасности IoT:

1. [Включение защитника для службы IoT в центре Интернета вещей](quickstart-onboard-iot-hub.md)
1. Если в центре Интернета вещей нет зарегистрированных устройств, [Зарегистрируйте новое устройство](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Создайте модуль безопасности азуреиотсекурити](quickstart-create-security-twin.md) для устройств.
1. Чтобы установить агент на виртуальном устройстве Azure, а не при установке на реальном устройстве, следует выполнить [Запуск новой виртуальной машины Azure](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) в доступной зоне.
1. [Разверните защитник для агента безопасности IOT](how-to-deploy-linux-cs.md) на устройстве IOT или на новой виртуальной машине.
1. Следуйте инструкциям по [trigger_events](https://aka.ms/iot-security-github-trigger-events) , чтобы запустить безвредную имитацию атаки.
1. Проверьте защитник для оповещений IoT в ответ на имитацию атаки на предыдущем шаге. Начните проверку через пять минут после выполнения скрипта.
1. Ознакомьтесь с [оповещениями](concept-security-alerts.md), [рекомендациями](concept-recommendations.md)и [подробным обзором с помощью log Analytics](how-to-security-data-access.md) с помощью центра Интернета вещей.

## <a name="next-steps"></a>Дальнейшие действия

- Настройка [решения](quickstart-configure-your-solution.md)
- [Quickstart: Create an azureiotsecurity module twin](quickstart-create-security-twin.md) (Краткое руководство по созданию модуля двойника устройства azureiotsecurity)
- Настройка [пользовательских оповещений](quickstart-create-custom-alerts.md)
- [Развертывание агента безопасности](how-to-deploy-agent.md)
