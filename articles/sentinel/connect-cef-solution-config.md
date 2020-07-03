---
title: Настройка решения безопасности для подключения данных CEF к предварительной версии Azure Sentinel | Документация Майкрософт
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588456"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>Шаг 2. Настройка решения безопасности для отправки сообщений CEF

На этом шаге будут выполнены необходимые изменения в конфигурации решения по обеспечению безопасности для отправки журналов агенту CEF.

## <a name="configure-a-solution-with-a-connector"></a>Настройка решения с помощью соединителя

Если в решении для обеспечения безопасности уже есть существующий соединитель, используйте инструкции, относящиеся к этому соединителю, как показано ниже.

- [Check Point](connect-checkpoint.md)
- [Cisco](connect-cisco.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [Жмем](connect-f5.md)  
- [Fortinet](connect-fortinet.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Trend Micro Deep Security](connect-trend-micro.md)
- [Zscaler](connect-zscaler.md)   

## <a name="configure-any-other-solution"></a>Настройка любого другого решения
Если соединитель не существует для конкретного решения безопасности, используйте следующие общие инструкции для перенаправления журналов в агент CEF.

1. Инструкции по настройке решения для отправки CEF сообщений см. в статье о конфигурации. Если решение отсутствует в списке, на устройстве необходимо задать эти значения, чтобы устройство отправляло необходимые журналы в нужном формате в агент syslog Sentinel Azure на основе агента Log Analytics. Вы можете изменить эти параметры на своем устройстве при условии, что они также будут изменены в управляющей программе syslog в агенте Sentinel Azure.
    - Протокол = TCP
    - Порт = 514
    - Format = CEF
    - IP-адрес. не забудьте отправить сообщения CEF по IP-адресу виртуальной машины, выделенной для этой цели.

   > [!NOTE]
   > Это решение поддерживает syslog RFC 3164 или RFC 5424.


1. Чтобы использовать соответствующую схему в Log Analytics для событий CEF, выполните поиск по `CommonSecurityLog`запросу.

1. Перейдите к ШАГУ 3. [Проверка подключения](connect-cef-verify.md).

## <a name="next-steps"></a>Дальнейшие шаги
В этом документе вы узнали, как подключить устройства CEF к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats.md).

