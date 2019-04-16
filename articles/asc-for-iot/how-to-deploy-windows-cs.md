---
title: Установка Windows центра безопасности Azure для Интернета вещей агент предварительной версии | Документация Майкрософт
description: Дополнительные сведения об установке центра безопасности Azure для Интернета вещей агент на 32-разрядная или 64-разрядных устройствах Windows.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: mlottner
ms.openlocfilehash: 601ee706adedf522890acc3f3996a7d36b349348
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59577848"
---
# <a name="deploy-an-azure-security-center-for-iot-c-based-security-agent-for-windows"></a>Развертывание в центр безопасности Azure для IoT C#-основе security agent для Windows

> [!IMPORTANT]
> Центр безопасности Azure для Интернета вещей сейчас предоставляется в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

В этом руководстве объясняется, как установить центр безопасности Azure (ASC) для Интернета вещей C#-агент безопасности в Windows.

Из этого руководства вы узнаете, как выполнить следующие задачи: 
> [!div class="checklist"]
> * Install
> * Проверка развертывания
> * Удаление агента.
> * Устранение неполадок 

## <a name="prerequisites"></a>Технические условия

Сведения о других платформах и версиях агентов см. в [этой статье](how-to-deploy-agent.md).

1. Права локального администратора на компьютере, которым вы хотите установить на. 

1. [Создайте модуль безопасности](quickstart-create-security-twin.md) для устройства.

## <a name="installation"></a>Установка 

Чтобы установить агент безопасности, сделайте следующее.

1. Для установки ASC для Интернета вещей Windows C# агента на устройстве, загрузите последнюю версию на компьютер от ASC для Интернета вещей [репозиторий GitHub](https://github.com/Azure/Azure-IoT-Security-Agent-CS).

2. Извлеките содержимое пакета и перейдите в папку /Install.

3. Откройте Windows PowerShell от имени администратора. 
    1. Добавьте права на выполнение к скрипту InstallSecurityAgent, выполнив следующую команду ```Unblock-File .\InstallSecurityAgent.ps1```.
    
        Затем выполните приведенную ниже команду:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    Например: 
    
    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```
    
    Дополнительные сведения о параметрах проверки подлинности см. в [этой статье](concept-security-agent-authentication-methods.md).

Скрипт выполняет следующее:

- Установка необходимых компонентов.

- Добавление пользователя службы (с отключенным интерактивным входом).

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

1. Откройте файл конфигурации (General.config) для редактирования в редакторе стандартный файл.

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
     
   или

    **CMD**
     ```
     sc.exe stop "ASC IoT Agent" 
     sc.exe start "ASC IoT Agent" 
     ```

1. Дополнительные сведения о сбое можно просмотреть в файле журнала.

   Расположение файла журнала: `%WinDir%/System32/IoTAgentLog.log`.


## <a name="next-steps"></a>Дальнейшие действия
- Просмотрите [обзор](overview.md) службы ASC для Интернета вещей.
- Ознакомьтесь с дополнительными сведениями об [архитектуре](architecture.md) ASC для Интернета вещей.
- Включите [службу](quickstart-onboard-iot-hub.md).
- Просмотрите [часто задаваемые вопросы](resources-frequently-asked-questions.md).
- Ознакомьтесь со сведениями об [оповещениях](concept-security-alerts.md).