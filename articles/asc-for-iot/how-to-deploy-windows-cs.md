---
title: Установка Windows центра безопасности Azure для Интернета вещей агент предварительной версии | Документация Майкрософт
description: Дополнительные сведения об установке центра безопасности Azure для Интернета вещей агент на 32-разрядная или 64-разрядных устройствах Windows.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: mlottner
ms.openlocfilehash: 7e3cb1f44711a8eedb248320db8bce5dfd0eaf0c
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58754602"
---
# <a name="deploy-an-azure-security-center-for-iot-c-based-security-agent-for-windows"></a>Развертывание в центр безопасности Azure для IoT C#-основе security agent для Windows

> [!IMPORTANT]
> Центр безопасности Azure для Интернета вещей находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

В этом руководстве объясняется, как установить центр безопасности Azure (ASC) для Интернета вещей C#-агент безопасности в Windows.

Из этого руководства вы узнаете, как выполнить следующие задачи: 
> [!div class="checklist"]
> * Install
> * Проверка развертывания
> * Удаление агента
> * Устранение неполадок 

## <a name="prerequisites"></a>Технические условия

Для других платформ и flavours агента, см. в разделе [выберите агент защиты](how-to-deploy-agent.md).

1. Права локального администратора на компьютере, которым вы хотите установить на. 

1. [Создайте модуль безопасности](quickstart-create-security-twin.md) для устройства.

## <a name="installation"></a>Установка 

Чтобы установить агент безопасности, сделайте следующее:

1. Для установки ASC для Интернета вещей Windows C# агента на устройстве, загрузите последнюю версию на компьютер от ASC для Интернета вещей [репозиторий GitHub](https://github.com/Azure/Azure-IoT-Security-Agent-CS).

2. Извлеките содержимое пакета и перейдите к папке/Install /.

3. Откройте Windows PowerShell от имени администратора. 
    1. Добавьте разрешения на выполнение скрипта InstallSecurityAgent, выполнив ```Unblock-File .\InstallSecurityAgent.ps1```
    
        и выполните:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    Например: 
    
    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```
    
    См. в разделе [Настройка проверки подлинности](concept-security-agent-authentication-methods.md) Дополнительные сведения о параметрах проверки подлинности.

Скрипт выполняет следующее:

- Установка необходимых компонентов.

- Добавляет пользователя службы (с помощью интерактивного входа в систему отключены).

- Устанавливает агент в качестве **Системная служба**.

- Настраивает агент с параметрами предоставленный проверки подлинности.


Для получения дополнительных сведений используйте команду Get-Help в PowerShell <br>Пример Get-Help.  
    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Проверьте состояние развертывания

- Проверьте состояние агента развертывания, выполнив:<br>
    ```sc.exe query "ASC IoT Agent" ```

### <a name="uninstall-the-agent"></a>Удаление агента

Чтобы удалить агент:

1. Выполните следующий сценарий PowerShell с **-режим** параметру присвоить **удаления**.  

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ``` 

## <a name="troubleshooting"></a>Устранение неполадок

Если агент не запускается, включите ведение журнала (Ведение журнала является *off* по умолчанию) для получения дополнительных сведений.

Чтобы включить ведение журнала:

1. Откройте файл конфигурации (General.config) для редактирования в редакторе стандартный файл.

1. Измените следующие значения:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/> 
   <add key="diagnosticVerbosityLevel" value="Some" /> 
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > Мы рекомендуем включить ведение журнала **off** после завершения устранения неполадок. Оставив ведения журнала **на** увеличения журнала файла размера и использования данных. 

1. Перезапустите агент, выполнив следующие PowerShell или командной строки:

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

1. Просмотрите файл журнала, Дополнительные сведения о сбое.

   Расположение файла журнала: `%WinDir%/System32/IoTAgentLog.log`


## <a name="next-steps"></a>Дальнейшие действия
- Чтение ASC для службы Интернета вещей [Обзор](overview.md)
- Дополнительные сведения о ASC для Интернета вещей [архитектуры](architecture.md)
- Включить [службы](quickstart-onboard-iot-hub.md)
- Чтение [часто задаваемые вопросы](resources-frequently-asked-questions.md)
- Понять [оповещения](concept-security-alerts.md)