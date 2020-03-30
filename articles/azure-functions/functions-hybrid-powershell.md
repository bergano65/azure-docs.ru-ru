---
title: Управление удаленными наемными ресурсами с помощью функций PowerShell
description: Узнайте, как настроить гибридные соединения в Azure Relay для подключения приложения функции PowerShell к ресурсам, которые затем могут быть использованы для удаленного управления ресурсом.
author: eamono
ms.topic: conceptual
ms.date: 9/5/2019
ms.author: eamono
ms.openlocfilehash: 36fc4c873dccfe9fa814bddccd829ed04207f095
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74226936"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>Управление гибридными средами с помощью PowerShell в функциях Azure и гибридных соединениях службы приложений

Функция гибридных подключений службы приложений Azure позволяет получить доступ к ресурсам в других сетях. Вы можете узнать больше об этой возможности в документации [гибридных](../app-service/app-service-hybrid-connections.md) подключений. В этой статье описывается, как использовать эту возможность для выполнения функций PowerShell, ориентированных на сервер. Этот сервер может использоваться для управления всеми ресурсами в предварительной среде с помощью функции Azure PowerShell.


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>Настройка внутреннего сервера для ремотивации PowerShell

Следующий скрипт позволяет PowerShell remoting, и он создает новое правило брандмауэра и слушателя WinRM https. Для целей тестирования используется самоподписанный сертификат. В производственной среде мы рекомендуем использовать подписанный сертификат.

```powershell
# For configuration of WinRM, see
# https://docs.microsoft.com/windows/win32/winrm/installation-and-configuration-for-windows-remote-management.

# Enable PowerShell remoting.
Enable-PSRemoting -Force

# Create firewall rule for WinRM. The default HTTPS port is 5986.
New-NetFirewallRule -Name "WinRM HTTPS" `
                    -DisplayName "WinRM HTTPS" `
                    -Enabled True `
                    -Profile "Any" `
                    -Action "Allow" `
                    -Direction "Inbound" `
                    -LocalPort 5986 `
                    -Protocol "TCP"

# Create new self-signed-certificate to be used by WinRM.
$Thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME  -CertStoreLocation Cert:\LocalMachine\My).Thumbprint

# Create WinRM HTTPS listener.
$Cmd = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:COMPUTERNAME ""; CertificateThumbprint=""$Thumbprint""}"
cmd.exe /C $Cmd
```

## <a name="create-a-powershell-function-app-in-the-portal"></a>Создание приложения функции PowerShell на портале

Функция гибридных подключений службы Приложений доступна только в базовых, стандартных и изолированных тарифных планах. При создании приложения функции с помощью PowerShell создайте или выберите один из этих планов.

1. На [портале Azure](https://portal.azure.com)выберите **: Создайте ресурс** в меню слева, а затем выберите **приложение Функция**.

1. Для **плана хостинга**выберите **план службы приложений,** а затем выберите план **службы app/Location.**

1. Выберите **Создать новое,** введите имя **плана службы приложений,** выберите **место** в [регионе](https://azure.microsoft.com/regions/) рядом с вами или рядом с другими службами, к которых вы хотите получить доступ, а затем выберите **уровень ценообразования.**

1. Выберите стандартный план S1, а затем выберите **Apply**.

1. Выберите **OK** для создания плана, а затем настроите оставшиеся настройки **приложения функции,** указанные в таблице сразу после следующего скриншота:

    ![Функциональное приложение PowerShell Core](./media/functions-hybrid-powershell/create-function-powershell-app.png)  

    | Параметр      | Рекомендуемое значение  | Описание                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Имя приложения** | Глобально уникальное имя | Имя, которое идентифицирует ваше новое приложение-функцию. Допустимые символы: `a-z`, `0-9` и `-`.  | 
    | **Подписка** | Ваша подписка | Подписка, в которой создано приложение-функция. |
    | **Ресурсная группа** |  myResourceGroup | Имя новой группы ресурсов, в которой создается приложение-функция. Вы также можете использовать предлагаемое значение. |
    | **Os** | Предпочтительный ОС | Выберите "Windows". |
    | **Стек среды выполнения** | Предпочитаемый язык | Выберите ядро PowerShell. |
    | **Хранилище** |  Глобально уникальное имя |  Создайте учетную запись хранения для использования приложением-функцией. Имена учетных записей хранения должны быть от 3 до 24 символов в длину и могут содержать только номера и буквы нижнего регистра. Вы также можете использовать имеющуюся учетную запись.
    | **Application Insights** | Значение по умолчанию | Создает ресурс Application Insights с таким же *именем приложения* в ближайшем поддерживаемом регионе. Расширяя эту настройку, можно изменить **имя нового ресурса** или выбрать другое **местоположение** в [области географии Azure,](https://azure.microsoft.com/global-infrastructure/geographies/) где требуется хранить данные. |

1. После проверки настроек выберите **«Создать».**

1. Выберите значок **уведомления** в правом верхнем углу портала и дождитесь сообщения «Развертывание удалось»

1. Выберите **Перейти к ресурсу** для просмотра нового приложения-функции. Можно также установить флажок **Закрепить на панели мониторинга**. Это упростит возвращение к этому ресурсу приложения-функции из панели мониторинга.

## <a name="create-a-hybrid-connection-for-the-function-app"></a>Создание гибридного соединения для приложения функции

Гибридные соединения настроены из сетевого раздела приложения функции:

1. Выберите вкладку **функций Платформы** в приложении функции, а затем выберите **Networking.** 
   ![Обзор приложений для сетей платформ](./media/functions-hybrid-powershell/app-overview-platform-networking.png)  
1. Выберите **Настроить конечные точки гибридных соединений.**
   ![Сеть](./media/functions-hybrid-powershell/select-network-feature.png)  
1. Выберите **Добавить гибридное соединение.**
   ![Гибридное соединение](./media/functions-hybrid-powershell/hybrid-connection-overview.png)  
1. Введите информацию о гибридном соединении, показанную сразу после следующего скриншота. У вас есть возможность сделать настройки **Endpoint Host** совпадают с именем хоста на территории, чтобы было легче запомнить сервер позже, когда вы работаете удаленные команды. Порт соответствует порту удаленного управления Windows по умолчанию, который был определен на сервере ранее.
  ![Добавление гибридного подключения](./media/functions-hybrid-powershell/add-hybrid-connection.png)  

    **Название гибридного соединения**: ContosoHybridOnPremisesServer
    
    **Конечный хозяин**: finance1
    
    **Порт конечных точек**: 5986
    
    **Пространство имен Servicebus:** Создание нового
    
    **Расположение**: Выберите доступное местоположение
    
    **Название**: contosopowershellhybrid

5. Выберите **OK** для создания гибридного соединения.

## <a name="download-and-install-the-hybrid-connection"></a>Скачать и установить гибридное соединение

1. Выберите **менеджер подключения Download,** чтобы сохранить файл .msi локально на вашем компьютере.
![Скачивание установщика](./media/functions-hybrid-powershell/download-hybrid-connection-installer.png)  
1. Копирование файла .msi с локального компьютера на локальный сервер.
1. Запустите установщик гибридного подключения для установки службы на сервере.
![Установка гибридного соединения](./media/functions-hybrid-powershell/hybrid-installation.png)  
1. С портала откройте гибридное соединение, а затем скопируйте строку соединения шлюза к буферу обмена.
![Копирование строки гибридного соединения](./media/functions-hybrid-powershell/copy-hybrid-connection.png)  
1. Откройте uI менеджера гибридного соединения на персонале внутреннего офиса.
![Открытый ui гибридного соединения](./media/functions-hybrid-powershell/hybrid-connection-ui.png)  
1. Выберите кнопку **Enter Manually** и вставьте строку соединения из буфера обмена.
![Соединение пасты](./media/functions-hybrid-powershell/enter-manual-connection.png)  
1. Перезапустите менеджер гибридного соединения от PowerShell, если он не отображаются как подключенный.
    ```powershell
    Restart-Service HybridConnectionManager
    ```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>Создание настройки приложения для пароля учетной записи администратора

1. Выберите вкладку **функций Платформы** в приложении функции.
1. В **общих настройках**выберите **Конфигурацию.**
![Выберите конфигурацию платформы](./media/functions-hybrid-powershell/select-configuration.png)  
1. Расширьте **новую настройку приложения,** чтобы создать новую настройку пароля.
1. Назовите настройку _ContosoUserPassword_и введите пароль.
1. Выберите **OK,** а затем сохраните для хранения пароля в функциональном приложении.
![Добавление настройки приложения для пароля](./media/functions-hybrid-powershell/add-appsetting-password.png)  

## <a name="create-a-function-http-trigger-to-test"></a>Создание триггера функции http для тестирования

1. Создайте новую функцию триггера HTTP из приложения функции.
![Создание нового триггера HTTP](./media/functions-hybrid-powershell/create-http-trigger-function.png)  
1. Замените код PowerShell из шаблона следующим кодом:

    ```powershell
    # Input bindings are passed in via param block.
    param($Request, $TriggerMetadata)
    
    # Write to the Azure Functions log stream.
    Write-Output "PowerShell HTTP trigger function processed a request."
    
    # Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
    $UserName = "ContosoUser"
    $securedPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
    $Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)
    
    # This is the name of the hybrid connection Endpoint.
    $HybridEndpoint = "finance1"
    
    $Script = {
        Param(
            [Parameter(Mandatory=$True)]
            [String] $Service
        )
        Get-Service $Service
    }
    
    Write-Output "Scenario 1: Running command via Invoke-Command"
    Invoke-Command -ComputerName $HybridEndpoint `
                   -Credential $Credential `
                   -Port 5986 `
                   -UseSSL `
                   -ScriptBlock $Script `
                   -ArgumentList "*" `
                   -SessionOption (New-PSSessionOption -SkipCACheck)
    ```

3. Выберите **Сохранить** и **выполнить** для тестирования функции.
![Проверьте функциональное приложение](./media/functions-hybrid-powershell/test-function-hybrid.png)  

## <a name="managing-other-systems-on-premises"></a>Управление другими системами на месте

Вы можете использовать подключенный локальный сервер для подключения к другим серверам и системам управления в локальной среде. Это позволяет управлять операциями центра обработки данных с Azure, используя функции PowerShell. Следующий скрипт регистрирует сеанс конфигурации PowerShell, который выполняется под предоставленными учетными данными. Эти учетные данные должны быть для администратора на удаленных серверах. Затем эту конфигурацию можно использовать для доступа к другим конечным точкам локального сервера или центра обработки данных.

```powershell
# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
$UserName = "ContosoUser"
$SecuredPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
$Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)

# This is the name of the hybrid connection Endpoint.
$HybridEndpoint = "finance1"

# The remote server that will be connected to run remote PowerShell commands on
$RemoteServer = "finance2".

Write-Output "Use hybrid connection server as a jump box to connect to a remote machine"

# We are registering an endpoint that runs under credentials ($Credential) that has access to the remote server.
$SessionName = "HybridSession"
$ScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $SessionName)

    if (-not (Get-PSSessionConfiguration -Name $SessionName -ErrorAction SilentlyContinue))
    {
        Register-PSSessionConfiguration -Name $SessionName -RunAsCredential $Using:Credential
    }
}

Write-Output "Registering session on hybrid connection jumpbox"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $ScriptCommand `
               -ArgumentList $SessionName `
               -SessionOption (New-PSSessionOption -SkipCACheck)

# Script to run on the jump box to run against the second machine.
$RemoteScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $ComputerName)
        # Write out the hostname of the hybrid connection server.
        hostname
        # Write out the hostname of the remote server.
        Invoke-Command -ComputerName $ComputerName -Credential $Using:Credential -ScriptBlock {hostname} `
                        -UseSSL -Port 5986 -SessionOption (New-PSSessionOption -SkipCACheck)
}

Write-Output "Running command against remote machine via jumpbox by connecting to the PowerShell configuration session"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $RemoteScriptCommand `
               -ArgumentList $RemoteServer `
               -SessionOption (New-PSSessionOption -SkipCACheck) `
               -ConfigurationName $SessionName
```

Замените следующие переменные в этом скрипте применимыми значениями из среды:
* $HybridEndpoint
* $RemoteServer

В двух предыдущих сценариях можно подключить и управлять средами, используя PowerShell в функциях Azure и гибридных соединениях. Мы рекомендуем вам узнать больше о [гибридных соединениях](../app-service/app-service-hybrid-connections.md) и [PowerShell в функциях.](./functions-reference-powershell.md)

Вы также можете использовать [виртуальные сети](./functions-create-vnet.md) Azure для подключения к вашей предварительной среде через функции Azure.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"] 
> [Подробнее о работе с функциями PowerShell](functions-reference-powershell.md)
