---
title: Понимание Центра безопасности Azure для модуля безопасности IoT для IoT Edge Документы Майкрософт
description: Поймите архитектуру и возможности модуля безопасности Azure Security Center для IoT-модуля безопасности для IoT Edge.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 148c68234a937efde554ef00a6014cdc1a350f34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71315891"
---
# <a name="azure-iot-edge-security-module"></a>Модуль безопасности Azure IoT Edge

[Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) предоставляет мощные возможности для управления и выполнения бизнес-процессов на краю.
Ключевая часть, которую IoT Edge играет в средах IoT, делает его особенно привлекательным для злоумышленников.

Модуль безопасности Azure Security Center для IoT обеспечивает комплексное решение для безопасности устройств IoT Edge.
Центр безопасности Azure для модуля IoT собирает, агрегирует и анализирует необработанные данные безопасности из операционной системы и контейнерной системы в действенные рекомендации по безопасности и оповещения.

Как и в Центре безопасности Azure для агентов безопасности IoT для устройств IoT, модуль Azure Security Center для IoT Edge очень настраивается через модуль Twin.
Смотрите [Нанастройку вашего агента,](how-to-agent-configuration.md) чтобы узнать больше.

Центр безопасности Azure для модуля безопасности IoT для IoT Edge предлагает следующие функции:

- Собирает необработанные события безопасности из базовой операционной системы (Linux) и систем контейнеров IoT Edge.
  
  Ознакомиться с [конфигурацией агента Azure для IoT](how-to-agent-configuration.md) можно узнать больше о доступных сборщиках данных безопасности.

- Анализ манифестов развертывания IoT Edge.

- Агрегирует необработанные события безопасности в сообщения, отправляемые через [Концентратор IoT Edge.](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)

- Удалите конфигурацию с помощью двойного модуля безопасности.

  Подробнее [о настройке Центра безопасности Azure для агента IoT](how-to-agent-configuration.md) читайте в графике.

Центр безопасности Azure для модуля безопасности IoT для IoT Edge работает в привилегированном режиме под IoT Edge.
Привилегированный режим необходим для того, чтобы модуль контролировал операционную систему и другие модули IoT Edge.

## <a name="module-supported-platforms"></a>Модуль поддерживает платформы

Центр безопасности Azure для модуля безопасности IoT для IoT Edge в настоящее время доступен только для Linux. 

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали об архитектуре и возможностях Центра безопасности Azure для модуля безопасности IoT для IoT Edge.

Чтобы продолжить работу с Центром безопасности Azure для развертывания IoT, используйте следующие статьи:

- Развертывание [модуля безопасности для IoT Edge](how-to-deploy-edge.md)
- Узнайте, как [настроить модуль безопасности](how-to-agent-configuration.md)
- Просмотрите [предпосылки службы](service-prerequisites.md) безопасности Azure для службы IoT
- Узнайте, как [включить Центр безопасности Azure для обслуживания IoT в вашем концентраторе IoT](quickstart-onboard-iot-hub.md)
- Узнайте больше об службе в [Центре безопасности Azure для часто задаваемых вопросов IoT](resources-frequently-asked-questions.md)