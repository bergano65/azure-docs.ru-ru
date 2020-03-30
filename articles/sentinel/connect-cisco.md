---
title: Подключение данных Cisco к Azure Sentinel Документы Майкрософт
description: Узнайте, как подключить данные Cisco к Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 62029b5c-29d3-4336-8a22-a9db8214eb7e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 264dab4fabc890ffe03d911f8ee3c08ef51375d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588405"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>Подключение Cisco ASA к Azure Sentinel



В этой статье объясняется, как подключить прибор Cisco ASA к Azure Sentinel. Разъем данных Cisco ASA позволяет легко подключать журналы Cisco ASA к Azure Sentinel, просматривать панели мониторинга, создавать пользовательские оповещения и улучшать исследования. Использование Cisco ASA на Azure Sentinel предоставит вам более подробную информацию об использовании Интернета в вашей организации и повысит возможности ее работы в области безопасности. 



## <a name="forward-cisco-asa-logs-to-the-syslog-agent"></a>Форвард Cisco ASA входит в агент Syslog

Cisco ASA не поддерживает CEF, поэтому журналы отправляются как Syslog, а агент Azure Sentinel знает, как их разбирать, как если бы они были журналами CEF. Найдите Cisco ASA для переадресовки сообщений Syslog в рабочее пространство Azure через агента Syslog:

1. Отправьте [сообщения Syslog на внешний сервер Syslog](https://aka.ms/asi-syslog-cisco-forwarding)и следуйте инструкциям по настройке соединения. Используйте эти параметры по запросу:
    - Установите **порт** 514 или порт, который вы установили в агенте.
    - Установите **syslog_ip** на IP-адрес агента.

1. Чтобы использовать соответствующую схему в журнале Analytics для `CommonSecurityLog`событий Cisco, ищите .

1. Продолжить [STEP 3: Проверка подключения](connect-cef-verify.md).




## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить приборы Cisco ASA к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Используйте трудовые книжки](tutorial-monitor-your-data.md) для мониторинга данных.


