---
title: Установка Windows Центра безопасности Azure для агента IoT Документы Майкрософт
description: Узнайте о том, как установить Центр безопасности Azure для ioT-агента на 32-битных или 64-битных устройствах Windows.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: acc99f260931de7fd8c7566a3ff6daf43f34c5ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68597213"
---
# <a name="deploy-an-azure-security-center-for-iot-c-based-security-agent-for-windows"></a>Развертывание агента безопасности Центра безопасности Azure для Интернета вещей на основе C# для Windows

В этом руководстве объясняется, как установить центр безопасности Azure для агента безопасности на основе IoT C в Windows.

Из этого руководства вы узнаете, как выполнить следующие задачи: 
> [!div class="checklist"]
> * Установка
> * Проверка развертывания
> * Удаление агента
> * Устранение неполадок 

## <a name="prerequisites"></a>Предварительные требования

Для других платформ и ароматов агента, [см.](how-to-deploy-agent.md)

1. Местные права админа на машину, которую вы хотите установить на. 

1. [Создайте модуль безопасности](quickstart-create-security-twin.md) для устройства.

## <a name="installation"></a>Установка 

Для установки агента безопасности используйте следующий рабочий процесс:

1. Установите на устройство Центр безопасности Azure для агента IoT Windows C. Загрузите самую полную версию на компьютер из центра безопасности Azure для [репозитория IoT GitHub.](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

1. Извлеките содержимое пакета и перейдите в папку /Install.

1. Откройте Windows PowerShell в качестве администратора. 
1. Добавление запущенных разрешений в скрипт InstallSecurityAgent, запустив:<br>
    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```
    
    затем запустить:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    Пример:
    
    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```
    
    Для получения дополнительной информации о параметрах аутентификации [см.](concept-security-agent-authentication-methods.md)

Этот скрипт выполняет следующие действия:

- Установка необходимых компонентов.

- Добавляет пользователя сервиса (с интерактивным знаком в инвалидах).

- Установка агента в качестве **системной службы**.

- Настройка агента с предоставленными параметрами проверки подлинности.


Для получения дополнительной справки выполните команду Get-Help в PowerShell <br>Ниже приведен пример команды Get-Help:  
    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Проверка состояния развертывания

- Проверьте состояние развертывания агента, выполнив следующую команду:<br>
    ```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>Удаление агента

Чтобы удалить агент, сделайте следующее:

1. Запустите следующий скрипт PowerShell с параметром **-mode**, для которого задано значение **Uninstall**.  

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ``` 

## <a name="troubleshooting"></a>Устранение неполадок

Если агент не запускается, включите ведение журнала (по умолчанию ведение журнала *отключено*), чтобы просмотреть дополнительные сведения.

Чтобы включить ведение журнала, сделайте следующее:

1. Откройте файл конфигурации (General.config) для редактирования с помощью стандартного редактора файлов.

1. Измените следующие значения:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/> 
   <add key="diagnosticVerbosityLevel" value="Some" /> 
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > Мы рекомендуем **отключить** ведение журнала после устранения неполадок. Если оставить ведение журнала **включенным**, размер файла журнала и использование данных увеличится. 

1. Перезапустите агент, выполнив следующую команду в PowerShell или командной строке.

    **Powershell**
     ```
     Restart-Service "ASC IoT Agent"
     ```
     
   или диспетчер конфигурации служб

    **Cmd**
     ```
     sc.exe stop "ASC IoT Agent" 
     sc.exe start "ASC IoT Agent" 
     ```

1. Дополнительные сведения о сбое можно просмотреть в файле журнала.

   Расположение файла журнала: `%WinDir%/System32/IoTAgentLog.log`.


## <a name="next-steps"></a>Дальнейшие действия
- Прочтите [обзор](overview.md) службы безопасности Azure для обслуживания IoT
- Узнайте больше о Центре безопасности Azure для [IoT-архитектуры](architecture.md)
- Включите [службу](quickstart-onboard-iot-hub.md).
- Читать [часто задаваемые вопросы](resources-frequently-asked-questions.md)
- Ознакомьтесь со сведениями об [оповещениях](concept-security-alerts.md).