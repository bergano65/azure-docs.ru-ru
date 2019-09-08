---
title: Управление удаленными локальными ресурсами с помощью функций Azure PowerShell
description: Узнайте, как настроить гибридные подключения в Azure Relay для подключения приложения-функции PowerShell к локальным ресурсам, которые затем можно использовать для удаленного управления локальным ресурсом.
author: eamono
manager: gailey777
ms.service: azure-functions
ms.topic: conceptual
ms.date: 9/5/2019
ms.author: eamono
ms.openlocfilehash: 0acbe0892be50e9e1747e5839101110d4adcebcf
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70775420"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>Управление гибридными средами с помощью PowerShell в функциях Azure и гибридные подключения службы приложений

Служба приложений Azure гибридные подключения предоставляет доступ к ресурсам в других сетях. Дополнительные сведения об этой возможности можно узнать в документации по [гибридные подключения](../app-service/app-service-hybrid-connections.md) . Ниже приведены сведения об использовании этой возможности для запуска функций PowerShell, предназначенных для локального сервера. Затем этот сервер можно использовать для управления всеми ресурсами в локальной среде из функции Azure PowerShell.


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>Настройка локального сервера для удаленного взаимодействия PowerShell

Приведенный ниже скрипт включает удаленное взаимодействие PowerShell, создает новое правило брандмауэра и HTTPS-прослушиватель WinRM. В целях тестирования используется самозаверяющий сертификат. Для рабочей среды рекомендуется использовать подписанный сертификат.

```powershell
# For configuration of WinRM, please see
# https://docs.microsoft.com/windows/win32/winrm/installation-and-configuration-for-windows-remote-management

# Enable PowerShell remoting
Enable-PSRemoting -Force

# Create firewall rule for WinRM. The default HTTPS port is 5986 
New-NetFirewallRule -Name "WinRM HTTPS" `
                    -DisplayName "WinRM HTTPS" `
                    -Enabled True `
                    -Profile "Any" `
                    -Action "Allow" `
                    -Direction "Inbound" `
                    -LocalPort 5986 `
                    -Protocol "TCP"

# Create new self-signed-certificate to be used by WinRM
$Thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME  -CertStoreLocation Cert:\LocalMachine\My).Thumbprint

# Create WinRM HTTPS listener
$Cmd = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:COMPUTERNAME ""; CertificateThumbprint=""$Thumbprint""}"
cmd.exe /C $Cmd
```

## <a name="create-a-powershell-function-app-in-the-portal"></a>Создание приложения-функции PowerShell на портале

Гибридные подключения службы приложений доступны только в планах "базовый", "Стандартный" и "изолированный". При создании приложения функции с помощью PowerShell создайте или выберите один из этих планов.

1. Перейдите на [портал Azure](https://portal.azure.com).

1. Выберите **+ создать ресурс** с левой стороны, а затем выберите приложение- **функция**.

1. В качестве **плана размещения**выберите **план службы приложений**, а затем выберите **план службы приложений или расположение**.

1. Выберите **создать**, введите имя **плана службы приложений** , выберите **Расположение** [в расположении рядом с](https://azure.microsoft.com/regions/) вами или рядом с другими службами, к которым ваши функции обращаются, а затем выберите **ценовая**Категория.

1. Выберите план S1 "Стандартный" и нажмите кнопку " **Применить**".

1. Нажмите кнопку **ОК** , чтобы создать план, а затем используйте оставшиеся параметры приложения функции, как указано в таблице под изображением. 

    ![Приложение функции PowerShell Core](./media/functions-hybrid-powershell/create-function-powershell-app.png)  

    | Параметр      | Предлагаемое значение  | Описание                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Имя приложения** | Глобально уникальное имя | Имя, которое идентифицирует ваше новое приложение-функцию. Допустимые символы: `a-z`, `0-9` и `-`.  | 
    | **Подписка** | Ваша подписка | Подписка, в которой создано приложение-функция. |
    | **Группа ресурсов** |  myResourceGroup | Имя новой группы ресурсов, в которой создается приложение-функция. Можно также использовать предлагаемое значение. |
    | **ОС** | Предпочтительная ОС | Выберите Windows. |
    | **Стек среды выполнения** | Предпочитаемый язык | Выберите PowerShell Core. |
    | **Хранилище** |  Глобально уникальное имя |  Создайте учетную запись хранения для использования приложением-функцией. Имя учетной записи хранения должно содержать от 3 до 24 символов и состоять только из цифр и строчных букв. Вы также можете использовать имеющуюся учетную запись.
    | **Application Insights** | Значение по умолчанию | Создает ресурс Application Insights с таким же *именем приложения* в ближайшем поддерживаемом регионе. Развернув этот параметр, можно задать **новое имя ресурса** или выбрать другое **расположение** в [географическом регионе Azure](https://azure.microsoft.com/global-infrastructure/geographies/), где будут хранится данные. |

1. После проверки параметров нажмите кнопку **создать**.

1. Выберите значок уведомления в правом верхнем углу портала. Вы должны увидеть сообщение **Развертывание выполнено**.

1. Выберите **Перейти к ресурсу** для просмотра нового приложения-функции. Можно также установить флажок **Закрепить на панели мониторинга**. Это упростит возвращение к этому ресурсу приложения-функции из панели мониторинга.

## <a name="create-a-hybrid-connection-for-the-function-app"></a>Создание гибридного подключения для приложения-функции

Гибридные подключения настраиваются из раздела сети приложения функции.

1. Перейдите на вкладку платформа приложения функции и выберите сеть.
![Обзор приложений для сетей платформы](./media/functions-hybrid-powershell/app-overview-platform-networking.png)  
1. Выберите Настройка конечных точек гибридных подключений.
![Сеть](./media/functions-hybrid-powershell/select-network-feature.png)  
1. Выберите Добавить гибридное подключение.
![Гибридное подключение](./media/functions-hybrid-powershell/hybrid-connection-overview.png)  
1. Введите сведения о гибридном подключении, как показано ниже. Узел конечной точки может также сопоставить имя узла локального сервера, чтобы упростить запоминание сервера при выполнении удаленных команд. Порт совпадает с портом службы удаленного управления Windows по умолчанию, определенным на сервере ранее.
![Добавить гибридное подключение](./media/functions-hybrid-powershell/add-hybrid-connection.png)  

    **Имя гибридного подключения** контосохибридонпремисессервер
    
    Finance1 **узла конечной точки**
    
    **Порт конечной точки** 5986
    
    **Пространство имен servicebus** Создать новый
    
    **Расположение** Выберите доступное расположение
    
    **Имя** контосоповершеллхибрид

5. Нажмите кнопку ОК, чтобы создать гибридное подключение.

## <a name="download-and-install-the-hybrid-connection"></a>Скачивание и установка гибридного подключения

1. Щелкните значок скачать диспетчер соединений, чтобы сохранить MSI файл на локальном компьютере.
![Скачать установщик](./media/functions-hybrid-powershell/download-hybrid-connection-installer.png)  
1. Скопируйте MSI-файл с локального компьютера на локальный сервер.
1. Запустите установщик гибридного подключения, чтобы установить службу на локальном сервере.
![Установить гибридное подключение](./media/functions-hybrid-powershell/hybrid-installation.png)  
1. На портале откройте гибридное подключение и скопируйте строку подключения шлюза в буфер обмена.
![Копировать строку гибридного подключения](./media/functions-hybrid-powershell/copy-hybrid-connection.png)  
1. Откройте пользовательский интерфейс Диспетчер гибридных подключений на локальном сервере.
![Открытие пользовательского интерфейса гибридного подключения](./media/functions-hybrid-powershell/hybrid-connection-ui.png)  
1. Нажмите кнопку "Ввод вручную" и вставьте строку подключения из буфера обмена.
![Вставить соединение](./media/functions-hybrid-powershell/enter-manual-connection.png)  
1. Перезапустите диспетчер гибридных подключений из PowerShell, если он не отображается как подключенный.
```powershell
Restart-Service HybridConnectionManager
```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>Создание параметра приложения для пароля учетной записи администратора

1. Выберите вкладку платформа в приложении функции.
1. Выберите конфигурацию в разделе ![общие параметры выбор конфигурации платформы.](./media/functions-hybrid-powershell/select-configuration.png)  
1. Выберите новый параметр приложения, чтобы создать новый параметр для пароля.
1. Присвойте параметру имя Контосаусерпассворд и введите пароль.
1. Нажмите кнопку ОК, а затем сохранить, чтобы сохранить пароль в приложении ![функции добавить параметр приложения для пароля.](./media/functions-hybrid-powershell/add-appsetting-password.png)  

## <a name="create-a-function-http-trigger-to-test"></a>Создание триггера HTTP-функции для тестирования

1. Создание новой функции триггера HTTP из приложения ![-функции Создание нового триггера HTTP](./media/functions-hybrid-powershell/create-http-trigger-function.png)  
1. Замените код PowerShell из шаблона следующим кодом:

    ```powershell
    # Input bindings are passed in via param block.
    param($Request, $TriggerMetadata)
    
    # Write to the Azure Functions log stream.
    Write-Output "PowerShell HTTP trigger function processed a request."
    
    # Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword
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

3. Нажмите кнопку Сохранить и запустить, чтобы протестировать приложение-функцию тестирования функции ![.](./media/functions-hybrid-powershell/test-function-hybrid.png)  

## <a name="managing-other-systems-on-premises"></a>Управление другими системами в локальной среде

Подключенный локальный сервер можно использовать для подключения к другим серверам и системам управления в локальной среде. Это позволяет управлять операциями центра обработки данных из Azure с помощью функций PowerShell. Следующий скрипт регистрирует сеанс конфигурации PowerShell, работающий под предоставленными учетными данными. Эти учетные данные должны быть администраторами на удаленных серверах. Затем эту конфигурацию можно использовать для доступа к другим конечным точкам на локальном сервере или в центре обработки данных.

```powershell
# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword
$UserName = "ContosoUser"
$SecuredPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
$Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)

# This is the name of the hybrid connection Endpoint.
$HybridEndpoint = "finance1"

# The remote server that will be conected to run remote PowerShell commands on
$RemoteServer = "finance2"

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

# Script to run on the jump box to run against the second machine
$RemoteScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $ComputerName)
        # Write out the hostname of the hybrid connection server
        hostname
        # Write out the hostname of the remote server
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

Замените эти переменные в приведенном выше скрипте значениями в вашей среде:
* $HybridEndpoint
* $RemoteServer

Приведенные выше два сценария позволяют подключать локальные среды и управлять ими с помощью PowerShell в функциях Azure и гибридные подключения. Дополнительные сведения о [гибридные подключения](../app-service/app-service-hybrid-connections.md) или PowerShell см. в статье о [функциях](./functions-reference-powershell.md) в документации.

[Виртуальные сети](./functions-create-vnet.md) Azure также можно использовать для подключения к локальной среде с помощью функций Azure.

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"] 
> [Дополнительные сведения о работе с функциями PowerShell](functions-reference-powershell.md)
