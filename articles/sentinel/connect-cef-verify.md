---
title: Проверка подключения к Azure Sentinel Документы Майкрософт
description: Проверка подключения решения безопасности, чтобы убедиться, что сообщения CEF перенаправляются в Azure Sentinel.
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
ms.openlocfilehash: e224f6d5cfd82dfc6cb1ce107d111ee0e031247b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588439"
---
# <a name="step-3-validate-connectivity"></a>ШАГ 3: Проверка подключения



После развертывания агента и настройки решения безопасности для переадресности сообщений CEF используйте эту статью, чтобы понять, как проверить связь между Azure Sentinel и вашим решением безопасности. 

## <a name="how-to-validate-connectivity"></a>Как проверить подключение

1. Open Log Analytics, чтобы убедиться, что журналы получены с помощью схемы CommonSecurityLog.<br> Это может занять более 20 минут, пока ваши журналы начинают появляться в журнале Analytics. 

1. Перед запуском скрипта мы рекомендуем отправлять сообщения из решения безопасности, чтобы убедиться, что они перенаправляются в настроенную на настройку прокси-машины Syslog. 
1. Вы должны иметь повышенные разрешения (судо) на вашей машине. Убедитесь, что у вас есть Python на вашей машине, используя следующую команду:`python –version`
1. Выполнить следующий скрипт, чтобы проверить связь между агентом, Azure Sentinel и вашим решением безопасности. Он проверяет, что переадресировка daemon правильно настроена, слушает на правильных портах, и что ничто не блокирует связь между daemon и агентом Log Analytics. Скрипт также отправляет макет сообщения 'TestCommonEventFormat', чтобы проверить сквозное подключение. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить приборы CEF к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats.md).
- [Используйте трудовые книжки](tutorial-monitor-your-data.md) для мониторинга данных.

