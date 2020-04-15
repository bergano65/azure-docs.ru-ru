---
title: Варианты развертывания
description: Начайтесь с пониманием базового рабочего процесса Центра безопасности Azure для функций и обслуживания IoT.
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
ms.openlocfilehash: 452372f187e97a95b9eee88936b15a0409dd4fe0
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311373"
---
# <a name="get-started-with-azure-security-center-for-iot"></a>Начало работы с Центром безопасности Azure для Интернета вещей

В этой статье приводится объяснение различных компонентов службы безопасности Azure Security Center for IoT и объясняется, как начать работу с службой с использованием двух возможных вариантов развертывания.

## <a name="deployment-options"></a>Варианты развертывания

Выберите сценарий службы, который наилучшим образом отвечает требованиям IoT-устройства и среды.

### <a name="built-in-deployment"></a>Встроенное развертывание

Используя бесшовную встроенную опцию развертывания, Центр безопасности Azure для IoT может быть быстро интегрирован в ваш концентратор IoT и обеспечить анализ безопасности конфигурации концентратора IoT, идентификации и управления устройствами, а также шаблонов связи концентратора.

Запустите [встроенное развертывание](iot-hub-integration.md) с участием мониторинга и рекомендаций IoT Hub.
    <br>

### <a name="enhanced-deployment"></a>Расширенное развертывание

Для повышения возможностей безопасности развертывание Центра безопасности Azure для агентов IoT в дополнение к обеспечению безопасности IoT Hub обеспечивает сбор, анализ и обнаружение ключевых данных безопасности на основе агентов с устройств IoT, а также комплексные возможности управления позами безопасности.

Запустите [расширенное развертывание](security-agents.md) с помощью комплексного решения по защите угроз и управлению защитой безопасности на основе агента.

## <a name="next-steps"></a>Следующие шаги

- Включить [Центр безопасности Azure для IoT](quickstart-onboard-iot-hub.md)
- Налажить [решение](quickstart-configure-your-solution.md)
- [Quickstart: Create an azureiotsecurity module twin](quickstart-create-security-twin.md) (Краткое руководство по созданию модуля двойника устройства azureiotsecurity)
- Настройка [пользовательских оповещений](quickstart-create-custom-alerts.md)
- [Развертывание агента безопасности](how-to-deploy-agent.md)
