---
title: Руководство по установке и развертыванию агента Linux C Центра безопасности Azure для агента IoT Документы Майкрософт
description: Узнайте, как установить Центр безопасности Azure для агента IoT как на 32-битном, так и на 64-битном Linux.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 7578811c37cd0bbe47821dadacce5fa5974f56cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68812725"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Развертывание агента безопасности Центра безопасности Azure для Интернета вещей на основе C для Linux

В этом руководстве объясняется, как установить и развернуть Центр безопасности Azure для агента безопасности на основе IoT C на Linux.

Из этого руководства вы узнаете, как выполнить следующие задачи: 
> [!div class="checklist"]
> * Установка
> * Проверка развертывания
> * Удаление агента
> * Устранение неполадок 

## <a name="prerequisites"></a>Предварительные требования

Для других платформ и ароматов агента, [см.](how-to-deploy-agent.md)

1. Для развертывания агента безопасности на машине, которую вы хотите установить (sudo), требуются локальные права админа.

1. [Создайте модуль безопасности](quickstart-create-security-twin.md) для устройства.

## <a name="installation"></a>Установка 

Для установки и развертывания агента безопасности используйте следующий рабочий процесс:


1. Скачать самую самую версию на компьютер с [GitHub](https://aka.ms/iot-security-github-c).

1. Извлеките содержимое пакета и перейдите к _папке /src/installation._

1. Добавление разрешений на запуск в **скрипт InstallSecurityAgent,** запустив следующую команду:
    
   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Затем выполните приведенную ниже команду: 

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```
   
   Дополнительные сведения о параметрах проверки подлинности см. в [этой статье](concept-security-agent-authentication-methods.md).

Этот скрипт выполняет следующую функцию:

1. Установка необходимых компонентов.

2. Добавляет пользователя сервиса (с интерактивным знаком в инвалидах).

3. Установка агента в качестве **управляющей программы** (предполагается, что устройство использует **systemd** для управления службой).

4. Настройка агента с предоставленными параметрами проверки подлинности. 

Для получения дополнительной справки запустите скрипт с параметром –help: 
    
    ./InstallSecurityAgent.sh --help

### <a name="uninstall-the-agent"></a>Удаление агента

Чтобы удалить агент, запустите скрипт с параметром --удалить:

    ./InstallSecurityAgent.sh -–uninstall

## <a name="troubleshooting"></a>Устранение неполадок
Проверьте состояние развертывания, выполнив следующую команду:

    systemctl status ASCIoTAgent.service


## <a name="next-steps"></a>Дальнейшие действия
- Прочтите [обзор](overview.md) службы безопасности Azure для обслуживания IoT
- Узнайте больше о Центре безопасности Azure для [IoT-архитектуры](architecture.md)
- Включите [службу](quickstart-onboard-iot-hub.md).
- Читать [часто задаваемые вопросы](resources-frequently-asked-questions.md)
- Понимание [предупреждений безопасности](concept-security-alerts.md)
