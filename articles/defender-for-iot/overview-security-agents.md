---
title: Обзор агента безопасности
description: Приступите к изучению, настройке, развертыванию и использованию защитника Azure для агентов службы безопасности IoT на устройствах IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/27/2019
ms.author: shhazam
ms.openlocfilehash: d3b50d909fb167ee123b548603628bae7d844c5b
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/31/2020
ms.locfileid: "97843694"
---
# <a name="get-started-with-azure-defender-for-iot-device-security-agents"></a>Приступая к работе с защитником Azure для агентов безопасности устройств IoT

Защитник для агентов безопасности IoT предлагает расширенные возможности безопасности, такие как мониторинг удаленных подключений, активных приложений, событий входа в систему и рекомендации по настройке операционной системы. Управление защитой угроз в поле устройства и обеспечение безопасности с помощью одной службы.

Эталонная архитектура для агентов безопасности Linux и Windows предоставляется как в C#, так и в C.

Защитник для агентов безопасности IoT обрабатывает сбор необработанных событий из операционной системы устройства, агрегирование событий для снижения затрат и настройку с помощью модуля устройства двойника. Сообщения безопасности отправляются через центр Интернета вещей в защитник для служб аналитики IoT.

Используйте следующий рабочий процесс, чтобы развернуть и проверить защитник для агентов безопасности IoT:

1. [Включение защитника для службы IoT в центре Интернета вещей](quickstart-onboard-iot-hub.md)
1. Если в центре Интернета вещей нет зарегистрированных устройств, [Зарегистрируйте новое устройство](../iot-accelerators/quickstart-device-simulation-deploy.md).
1. [Создайте модуль безопасности азуреиотсекурити](quickstart-create-security-twin.md) для устройств.
1. Чтобы установить агент на виртуальном устройстве Azure, а не при установке на реальном устройстве, следует выполнить [Запуск новой виртуальной машины Azure](../virtual-machines/linux/quick-create-portal.md) в доступной зоне.
1. [Разверните защитник для агента безопасности IOT](how-to-deploy-linux-cs.md) на устройстве IOT или на новой виртуальной машине.
1. Следуйте инструкциям по [trigger_events](https://aka.ms/iot-security-github-trigger-events) , чтобы запустить безвредную имитацию атаки.
1. Проверьте защитник для оповещений IoT в ответ на имитацию атаки на предыдущем шаге. Начните проверку через пять минут после выполнения скрипта.
1. Ознакомьтесь с [оповещениями](concept-security-alerts.md), [рекомендациями](concept-recommendations.md)и [подробным обзором с помощью log Analytics](how-to-security-data-access.md) с помощью центра Интернета вещей.

## <a name="next-steps"></a>Дальнейшие действия

- Настройка [решения](quickstart-configure-your-solution.md)
- [Quickstart: Create an azureiotsecurity module twin](quickstart-create-security-twin.md) (Краткое руководство по созданию модуля двойника устройства azureiotsecurity)
- Настройка [пользовательских оповещений](quickstart-create-custom-alerts.md)
- [Развертывание агента безопасности](how-to-deploy-agent.md)