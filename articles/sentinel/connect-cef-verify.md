---
title: Проверка подключения к Azure Sentinel | Документация Майкрософт
description: Проверьте подключение решения безопасности, чтобы убедиться, что CEF сообщения перенаправляются в Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: rkarlin
ms.openlocfilehash: e192f5db212faae9a8a93fbf3991f05b0adef0f8
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/02/2020
ms.locfileid: "75615350"
---
# <a name="step-3-validate-connectivity"></a>Шаг 3. Проверка подключения



После развертывания агента и настройки решения безопасности для пересылки сообщений CEF используйте эту статью, чтобы узнать, как проверить подключение между Azure Sentinel и решением безопасности. 

## <a name="how-to-validate-connectivity"></a>Проверка подключения

1. Откройте Log Analytics, чтобы убедиться, что журналы получены с помощью схемы CommonSecurityLog.<br> Если журналы начнут появляться в Log Analytics, это может занять до 20 минут. 

1. Перед запуском скрипта рекомендуется отправить сообщения из решения безопасности, чтобы убедиться, что они перенаправлены на настроенный вами прокси-сервер syslog. 
1. Необходимо иметь повышенные разрешения (sudo) на компьютере. Убедитесь, что на компьютере установлен Python, выполнив следующую команду: `python –version`
1. Выполните следующий сценарий, чтобы проверить подключение между агентом, Sentinel Azure и решением по обеспечению безопасности. Он проверяет правильность настройки перенаправления управляющей программы, прослушивает правильные порты и не блокирует обмен данными между управляющей программой и агентом Log Analytics. Сценарий также отправляет фиктивные сообщения "Тесткоммоневентформат" для проверки сквозного подключения. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить устройства CEF к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats.md).
- [Используйте книги](tutorial-monitor-your-data.md) для отслеживания данных.

