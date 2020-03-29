---
title: Найди начало использовать Центр безопасности Azure для IoT Документы Майкрософт
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
ms.openlocfilehash: 86c6c0bac5caae0873d0067c6abcb5a8ac864c88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68596417"
---
# <a name="get-started-with-azure-security-center-for-iot"></a>Начало работы с Центром безопасности Azure для Интернета вещей

В этой статье приводится объяснение различных строительных блоков службы безопасности Azure Security Center for IoT и объясняется, как начать работу с службой с использованием двух возможных вариантов развертывания.  

## <a name="deployment-options"></a>Варианты развертывания

Выберите сценарий службы, который наилучшим образом отвечает требованиям IoT-устройства и среды. 

### <a name="built-in-deployment"></a>Встроенное развертывание
Используя бесшовную встроенную опцию развертывания, Центр безопасности Azure для IoT может быть быстро интегрирован в ваш концентратор IoT и обеспечить анализ безопасности конфигурации концентратора IoT, идентификации и управления устройствами, а также шаблонов связи концентратора.

Запустите [встроенное развертывание](iot-hub-integration.md) с участием мониторинга и рекомендаций IoT Hub. 
    <br>

### <a name="enhanced-deployment"></a>Расширенное развертывание
Для повышения возможностей безопасности развертывание Центра безопасности Azure для ioT-агентов в дополнение к обеспечению безопасности IoT Hub обеспечивает сбор, анализ и обнаружение ключевых данных безопасности с устройств IoT, а также всеобъемлющее возможности управления попозицией безопасности.

Запустите [расширенное развертывание](security-agents.md) с помощью комплексного решения по защите угроз и управлению защитой безопасности на основе агента.
   

## <a name="next-steps"></a>Дальнейшие действия

- Включить [Центр безопасности Azure для IoT](quickstart-onboard-iot-hub.md)
- Налажить [решение](quickstart-configure-your-solution.md)
- [Quickstart: Create an azureiotsecurity module twin](quickstart-create-security-twin.md) (Краткое руководство по созданию модуля двойника устройства azureiotsecurity)
- Настройка [пользовательских оповещений](quickstart-create-custom-alerts.md)
- [Развертывание агента безопасности](how-to-deploy-agent.md)
