---
title: Установка Windows в центре безопасности Azure для агента IoT | Документация Майкрософт
description: Узнайте, как установить центр безопасности Azure для агента IoT на 32-разрядных или 64-разрядных устройствах Windows.
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
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597213"
---
# <a name="deploy-an-azure-security-center-for-iot-c-based-security-agent-for-windows"></a>Развертывание центра безопасности Azure для агента безопасности C#на основе Интернета вещей для Windows

В этом руководство объясняется, как установить центр безопасности Azure для C#агента безопасности на основе Интернета вещей в Windows.

Из этого руководства вы узнаете, как выполнить следующие задачи: 
> [!div class="checklist"]
> * Установка
> * Проверка развертывания
> * Удаление агента.
> * Устранение неполадок 

## <a name="prerequisites"></a>Предварительные требования

Сведения о других платформах и разновидностях агентов см. в разделе Выбор подходящего [агента безопасности](how-to-deploy-agent.md).

1. Права локального администратора на компьютере, на котором вы хотите установить. 

1. [Создайте модуль безопасности](quickstart-create-security-twin.md) для устройства.

## <a name="installation"></a>Установка 

Чтобы установить агент безопасности, используйте следующий рабочий процесс:

1. Установите на устройстве центр безопасности Azure для агента C# Windows IOT. Скачайте последнюю версию на свой компьютер из [репозитория GitHub](https://github.com/Azure/Azure-IoT-Security-Agent-CS)центра безопасности Azure для IOT.

1. Извлеките содержимое пакета и перейдите в папку /Install.

1. Откройте Windows PowerShell от имени администратора. 
1. Добавьте разрешения на выполнение скрипта Инсталлсекуритяжент, выполнив команду:<br>
    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```
    
    затем выполните:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    Пример:
    
    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```
    
    Дополнительные сведения о параметрах проверки подлинности см. [в разделе Настройка проверки](concept-security-agent-authentication-methods.md)подлинности.

Этот сценарий выполняет следующие действия:

- Установка необходимых компонентов.

- Добавляет пользователя службы (с отключенным интерактивным входом).

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

1. Откройте файл конфигурации (General. config) для редактирования с помощью стандартного редактора файлов.

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

    **PowerShell**
     ```
     Restart-Service "ASC IoT Agent"
     ```
     
   или диспетчер конфигурации служб

    **CMD**
     ```
     sc.exe stop "ASC IoT Agent" 
     sc.exe start "ASC IoT Agent" 
     ```

1. Дополнительные сведения о сбое можно просмотреть в файле журнала.

   Расположение файла журнала: `%WinDir%/System32/IoTAgentLog.log`.


## <a name="next-steps"></a>Следующие шаги
- Ознакомьтесь с [обзором](overview.md) центра безопасности Azure для службы IOT
- Узнайте больше о центре безопасности Azure для [архитектуры](architecture.md) IOT
- Включите [службу](quickstart-onboard-iot-hub.md).
- Просмотрите [часто задаваемые вопросы](resources-frequently-asked-questions.md).
- Ознакомьтесь со сведениями об [оповещениях](concept-security-alerts.md).