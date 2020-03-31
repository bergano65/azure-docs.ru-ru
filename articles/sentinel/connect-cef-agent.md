---
title: Развертывание агента для подключения данных CEF к preview Azure Sentinel Документы Майкрософт
description: Узнайте, как развернуть агент для подключения данных CEF к Azure Sentinel.
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
ms.date: 11/26/2019
ms.author: yelevin
ms.openlocfilehash: b0c9335357cb793ea76e1dbe68575f716a50372a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588473"
---
# <a name="step-1-deploy-the-agent"></a>Шаг 1: Развертывание агента


На этом этапе необходимо выбрать машину Linux, которая будет выступать в качестве прокси между Azure Sentinel и вашим решением безопасности. Вам придется запустить скрипт на прокси-машине, который:
- Устанавливает агент Log Analytics и настраивает его по мере необходимости для прослушивания сообщений Syslog.
- Настраивает Syslog daemon для прослушивания сообщений Syslog с помощью порта TCP 514, а затем направляет только сообщения CEF агенту Log Analytics с помощью порта TCP 25226.
- Устанавливает агента Syslog для сбора данных и безопасного их отправки в Azure Sentinel, где они разбираются и обогащаются.
 
## <a name="deploy-the-agent"></a>Развертывание агента
 
1. На портале Azure Sentinel щелкните **разъемы данных** и выберите **общий формат событий (CEF),** а затем **откройте страницу разъема.** 

1. При **установке и настройке агента Syslog**выберите тип машины, как Azure, так и другое облако, или на месте. 
   > [!NOTE]
   > Поскольку скрипт на следующем этапе устанавливает агент Log Analytics и подключает машину к рабочему пространству Azure Sentinel, убедитесь, что эта машина не подключена к какому-либо другому рабочему пространству.
1. Вы должны иметь повышенные разрешения (судо) на вашей машине. Убедитесь, что у вас есть Python на вашей машине, используя следующую команду:`python –version`

1. Выполнить следующий скрипт на вашем прокси-машине.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. Во время работы скрипта проверьте, чтобы не получить никаких ошибок или предупреждающих сообщений.

Продолжить [STEP 2: Нанастройка решения безопасности для переадресовки сообщений CEF.](connect-cef-solution-config.md)


## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить приборы CEF к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats.md).

