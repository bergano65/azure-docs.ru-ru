---
title: Напередите решение безопасности для подключения данных CEF к Preview Azure Sentinel Документы Майкрософт
description: Узнайте, как настроить решение безопасности для подключения данных CEF к Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: bdb76954b1db8135d8a36d6658bb7fff274ac126
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588456"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>Шаг 2: Налаживайте решение безопасности для отправки сообщений CEF

На этом этапе вы будете выполнять необходимые изменения конфигурации на вашем решении безопасности себя для отправки журналов агенту CEF.

## <a name="configure-a-solution-with-a-connector"></a>Налаживание раствора с помощью разъема

Если в вашем решении безопасности уже есть существующий разъем, используйте инструкции для разъема следующим образом:

- [Check Point](connect-checkpoint.md)
- [Cisco](connect-cisco.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5](connect-f5.md)  
- [Fortinet](connect-fortinet.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Trend Micro Deep Security](connect-trend-micro.md)
- [Zscaler](connect-zscaler.md)   

## <a name="configure-any-other-solution"></a>Настройка любого другого решения
Если разъем не существует для вашего конкретного решения безопасности, используйте следующие общие инструкции для переадресовки журналов агенту CEF.

1. Перейдите к конкретной статье конфигурации для шагов о том, как настроить решение для отправки сообщений CEF. Если ваше решение не перечислено, на приборе необходимо установить эти значения таким образом, чтобы прибор отправлял необходимые журналы в необходимом формате агенту Azure Sentinel Syslog на основе агента Log Analytics. Эти параметры можно изменять в приборе, если вы также измените их в daemon Syslog на агенте Azure Sentinel.
    - Протокол - TCP
    - Порт No 514
    - Формат - CEF
    - IP-адрес - не забудьте отправить сообщения CEF на IP-адрес виртуальной машины, которую вы посвятили для этой цели.

   > [!NOTE]
   > Это решение поддерживает Syslog RFC 3164 или RFC 5424.


1. Чтобы использовать соответствующую схему в журнале Analytics для `CommonSecurityLog`событий CEF, ищите .

1. Продолжить STEP 3: [проверить подключение](connect-cef-verify.md).

## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить приборы CEF к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats.md).

