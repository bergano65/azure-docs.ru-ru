---
title: Развертывание агента для подключения данных CEF к предварительной версии Azure Sentinel | Документация Майкрософт
description: Узнайте, как развернуть агент для подключения данных CEF к Azure Sentinel.
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
ms.date: 11/26/2019
ms.author: rkarlin
ms.openlocfilehash: 5451382763195172b48e93ef2ea859552b05d154
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/02/2020
ms.locfileid: "75615376"
---
# <a name="step-1-deploy-the-agent"></a>Шаг 1. Развертывание агента


На этом шаге необходимо выбрать компьютер Linux, который будет использоваться в качестве прокси-сервера между Sentinel Azure и решением безопасности. Вам потребуется запустить сценарий на прокси-компьютере, который:
- Устанавливает агент Log Analytics и настраивает его по мере необходимости для прослушивания сообщений системного журнала.
- Настраивает управляющую программу syslog на прослушивание сообщений Syslog с помощью TCP-порта 514, а затем пересылает только сообщения CEF агенту Log Analytics с помощью TCP-порта 25226.
- Устанавливает агент системного журнала для получения данных и их безопасного отправку в Azure Sentinel, где они анализируются и расширяются.
 
## <a name="deploy-the-agent"></a>Развертывание агента
 
1. На портале Sentinel Azure щелкните **соединители данных** и выберите **общий формат событий (CEF)** , а затем **откройте страницу соединителя**. 

1. В разделе **Установка и настройка агента syslog**выберите тип компьютера: Azure, другое облако или локально. 
   > [!NOTE]
   > Так как скрипт на следующем шаге устанавливает агент Log Analytics и подключает компьютер к рабочей области Sentinel Azure, убедитесь, что этот компьютер не подключен к какой-либо другой рабочей области.
1. Необходимо иметь повышенные разрешения (sudo) на компьютере. Убедитесь, что на компьютере установлен Python, выполнив следующую команду: `python –version`

1. Выполните следующий сценарий на прокси-компьютере.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. Во время выполнения скрипта убедитесь, что не получены сообщения об ошибках и предупреждения.

Перейдите к [шагу 2. Настройка решения безопасности для пересылки сообщений CEF](connect-cef-solution-config.md) .


## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить устройства CEF к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats.md).

