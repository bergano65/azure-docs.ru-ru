---
title: Приступая к работе с центром безопасности Azure для IoT | Документация Майкрософт
description: Приступите к работе, чтобы понять базовый рабочий процесс центра безопасности Azure для функций и служб IoT.
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
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596417"
---
# <a name="get-started-with-azure-security-center-for-iot"></a>Начало работы с Центром безопасности Azure для Интернета вещей

В этой статье содержится описание различных стандартных блоков центра безопасности Azure для службы IoT и объясняется, как приступить к работе со службой с помощью двух возможных вариантов развертывания.  

## <a name="deployment-options"></a>Параметры развертывания

Выберите сценарий службы, который наилучшим образом соответствует требованиям к устройству и среде Интернета вещей. 

### <a name="built-in-deployment"></a>Встроенное развертывание
С помощью простого встроенного развертывания центр безопасности Azure для Интернета вещей можно быстро интегрировать в центре Интернета вещей и обеспечить анализ безопасности для настройки центра Интернета вещей, идентификации устройств и управления ими, а также шаблонов связи устройств.

Запустите [встроенное развертывание](iot-hub-integration.md) с помощью мониторинга и рекомендаций центра Интернета вещей. 
    <br>

### <a name="enhanced-deployment"></a>Расширенное развертывание
В целях повышения безопасности Развертывание центра безопасности Azure для агентов IoT в дополнение к включению безопасности центра Интернета вещей обеспечивает сбор событий на основе агентов, анализ и обнаружение угроз для ключевых данных безопасности с устройств Интернета вещей, а также комплексные возможности возможности управления уровнями безопасности.

Запустите [Улучшенное развертывание](security-agents.md) с помощью комплексного решения для защиты от угроз и управления уровнями безопасности на основе агентов.
   

## <a name="next-steps"></a>Следующие шаги

- Включение [центра безопасности Azure для Интернета вещей](quickstart-onboard-iot-hub.md)
- Настройка [решения](quickstart-configure-your-solution.md)
- [Quickstart: Create an azureiotsecurity module twin](quickstart-create-security-twin.md) (Краткое руководство по созданию модуля двойника устройства azureiotsecurity)
- Настройка [пользовательских оповещений](quickstart-create-custom-alerts.md)
- [Развертывание агента безопасности](how-to-deploy-agent.md)
