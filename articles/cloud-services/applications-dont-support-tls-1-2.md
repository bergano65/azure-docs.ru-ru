---
title: Устранение проблем, вызванных приложениями, которые не поддерживают TLS 1,2 | Документация Майкрософт
description: Устранение проблем, вызванных приложениями, которые не поддерживают TLS 1,2
services: cloud-services
documentationcenter: ''
author: MicahMcKittrick-MSFT
manager: vashan
editor: ''
tags: top-support-issue
ms.assetid: ''
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 01/17/2020
ms.author: tagore
ms.openlocfilehash: c4cbe2a99de97b2938d494e80d410a44c4143629
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026839"
---
# <a name="troubleshooting-applications-that-dont-support-tls-12"></a>Устранение неполадок в приложениях, которые не поддерживают TLS 1,2
В этой статье описывается, как включить старые протоколы TLS (TLS 1,0 и 1,1), а также применить устаревшие комплекты шифров для поддержки дополнительных протоколов в веб-ролях и рабочей роли облачной службы Windows Server 2019. 

Мы понимаем, что в то время, когда мы делаем действия по исключению TLS 1,0 и TLS 1,1, нашим клиентам может потребоваться поддержка более старых протоколов и комплектов шифров, пока они не смогут спланировать их устаревшие.  Хотя мы не рекомендуем повторно включать эти устаревшие значения, мы предоставляем рекомендации для помощи клиентам. Мы советуем клиентам оценить риск регрессии, прежде чем вносить изменения, описанные в этой статье. 

> [!NOTE]
> Выпуск семейства 6 гостевой ОС обеспечивает принудительное использование TLS 1,2 путем отключения шифров 1.0/1.0. 

  
## <a name="dropping-support-for-tls-10-tls-11-and-older-cipher-suites"></a>Удаление поддержки TLS 1,0, TLS 1,1 и старых комплектов шифров 
В соответствии с нашим обязательством по использованию наилучшего в своем классе шифрования корпорация Майкрософт объявила о планах начать миграцию с TLS 1,0 и 1,1 в июне 2017.   С момента первоначального объявления Корпорация Майкрософт объявила о нашей намеренности отключить протокол TLS 1,0 и 1,1 по умолчанию в поддерживаемых версиях Microsoft ребр и Internet Explorer 11 в первой половине 2020.  Аналогичные объявления от Apple, Google и Mozilla обозначают направление, в котором направляется отрасль.   

## <a name="tls-configuration"></a>Конфигурация TLS  
Образ сервера Windows Server 2019 Cloud Server настроен с помощью TLS 1,0 и TLS 1,1 отключен на уровне реестра. Это означает, что приложения, развернутые в этой версии Windows и использующие стек Windows для согласования TLS, не допускают обмен данными TLS 1,0 и TLS 1,1.   

Сервер также поставляется с ограниченным набором комплектов шифров: 

```Powershell
    TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 
    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 
    TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
    TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 
```
 
## <a name="step-1-create-the-powershell-script-to-enable-tls-10-and-tls-11"></a>Шаг 1. Создание скрипта PowerShell для включения TLS 1,0 и TLS 1,1 

Используйте следующий код в качестве примера, чтобы создать скрипт, включающий старые протоколы и комплекты шифров.   Для целей этой документации этот сценарий будет называться: Тлссеттингс. ps1. 
  
```Powershell
#******************* FUNCTION THAT ACTUALLY UPDATES KEYS; WILL RETURN REBOOT FLAG IF CHANGES *********************** 

Function Set-CryptoSetting {  
    param (  
        $regKeyName,  
        $value,  
        $valuedata,  
        $valuetype       
    )  
    
    $restart = $false 
  
    # Check for existence of registry key, and create if it does not exist  
    If (!(Test-Path -Path $regKeyName)) {  
        New-Item $regKeyName | Out-Null  
    }  

    # Get data of registry value, or null if it does not exist  
    $val = (Get-ItemProperty -Path $regKeyName -Name $value -ErrorAction SilentlyContinue).$value  

    If ($val -eq $null) {  
        # Value does not exist - create and set to desired value  
        New-ItemProperty -Path $regKeyName -Name $value -Value $valuedata -PropertyType $valuetype | Out-Null  
        $restart = $true 
    } 

    Else {  
        # Value does exist - if not equal to desired value, change it  
        If ($val -ne $valuedata) {  
            Set-ItemProperty -Path $regKeyName -Name $value -Value $valuedata  
            $restart = $true  
        }  
    }  

    $restart  
}  

#*************************************************************************************************************** 

#****************************** CIPHERSUITES FOR OS VERSIONS WINDOWS 10 AND ABOVE ****************************** 

function Get-BaseCipherSuitesWin10Above() 
{ 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256" 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384" 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256" 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384" 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256" 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384" 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256" 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384" 

# Legacy cipher suites 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA_P256" 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA_P256" 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256" 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256" 
        $cipherorder += "TLS_RSA_WITH_AES_256_GCM_SHA384"  
        $cipherorder += "TLS_RSA_WITH_AES_128_GCM_SHA256"  
        $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA256"  
        $cipherorder += "TLS_RSA_WITH_AES_128_CBC_SHA256"  
        $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA" 
        $cipherorder += "TLS_RSA_WITH_AES_128_CBC_SHA" 

  return $cipherorder 
} 

  
#*************************************************************************************************************** 


#********************************************** REGISTRY KEYS **************************************************** 

  
function Get-RegKeyPathToEnable() 
{ 
    $regKeyPath = @( 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2",         
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" , 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Client", 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" , 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Client",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" 
    ) 
    return $regKeyPath 
} 

#*************************************************************************************************************** 

$localRegistryPath = @() 

# Enable TLS 1.2, TLS 1.1 and TLS 1.0 
$localRegistryPath += Get-RegKeyPathToEnable 

#******************* CREATE THE REGISTRY KEYS IF THEY DON'T EXIST******************************** 

# Check for existence of the registry keys, and create if they do not exist  
For ($i = 0; $i -lt $localRegistryPath .Length; $i = $i + 1) {  
   Write-Log -Message "Checking for existing of key: $($localRegistryPath [$i]) " -Logfile $logLocation  -Severity Information 
   If (!(Test-Path -Path $localRegistryPath [$i])) {  
        New-Item $localRegistryPath [$i] | Out-Null 
               Write-Log -Message "Creating key: $($localRegistryPath [$i]) "  -Logfile $logLocation -Severity Information 
           } 
}  

#********************************* EXPLICITLY Enable TLS12,  TLS11 and TLS10********************************* 

For ($i = 0; $i -lt $localRegistryPath .Length; $i = $i + 1) { 
    if ($localRegistryPath [$i].Contains("Client") -Or $localRegistryPath [$i].Contains("Server")) { 
        Write-Log -Message "Enabling this key: $($localRegistryPath [$i]) "  -Logfile $logLocation -Severity Information  
        $result = Set-CryptoSetting $localRegistryPath [$i].ToString() Enabled 1 DWord   
        $result = Set-CryptoSetting $localRegistryPath [$i].ToString() DisabledByDefault 0 DWord  
        $reboot = $reboot -or $result 
    } 
} 
 
#**************************************** SET THE CIPHER SUITE ORDER******************************** 

$cipherlist = @() 

# Set cipher suite order 
$cipherlist += Get-BaseCipherSuitesWin10Above 
$cipherorder = [System.String]::Join(",", $cipherlist) 
$CipherSuiteRegKey = "HKLM:\SOFTWARE\Policies\Microsoft\Cryptography\Configuration\SSL\00010002"  

if (!(Test-Path -Path $CipherSuiteRegKey))  
{  
    New-Item $CipherSuiteRegKey | Out-Null  
    $reboot = $True  
    Write-Log -Message "Creating key: $($CipherSuiteRegKey) "  -Logfile $logLocation -Severity Information 
}  

Set-ItemProperty -Path $CipherSuiteRegKey -Name Functions -Value $cipherorder  

#********************************************* REBOOT ******************************************* 

Write-Host "A reboot is required in order for changes to effect"  
Write-Host "Rebooting now..."  
shutdown.exe /r /t 5 /c "Crypto settings changed" /f /d p:2:4  
```
  
## <a name="step-2-create-a-command-file"></a>Шаг 2. Создание командного файла 

Создайте CMD-файл со следующими сведениями. 

```
IF "%ComputeEmulatorRunning%" == "true" ( 
   ECHO Not launching the script, since is DEV Machine >> "Log.txt" 2>&1 
) ELSE ( 

PowerShell .\TLSsettings.ps1   
  
) 

REM This line is required to ensure the startup tasks does not block the role from starting in case of error.  DO NOT REMOVE!!!! 

EXIT /B 0   
```

## <a name="step-3-add-the-startup-task-to-the-roles-service-definition-csdef"></a>Шаг 3. Добавление задачи запуска в определение службы роли (CSDEF) 

Ниже приведен пример, демонстрирующий рабочую роль и веб-роль. 
  
```
<?xml version="1.0" encoding="utf-8"?> 
<ServiceDefinition name="NugetExampleCloudServices" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6"> 
  <WebRole name="WebRole1" vmsize="Standard_D1_v2"> 
    <Sites> 
      <Site name="Web"> 
        <Bindings> 
          <Binding name="Endpoint1" endpointName="Endpoint1" /> 
        </Bindings> 
      </Site> 
    </Sites> 
    <Startup> 
      <Task executionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
      </Task> 
    </Startup> 
    <Endpoints> 
      <InputEndpoint name="Endpoint1" protocol="http" port="80" /> 
    </Endpoints> 
  </WebRole> 
  <WorkerRole name="WorkerRole1" vmsize="Standard_D1_v2"> 
    <Startup> 
      <Task executionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
      </Task> 
    </Startup> 
  </WorkerRole> 
</ServiceDefinition> 
```

## <a name="step-4-validation"></a>Шаг 4. Проверка 

[Ссллабс](https://www.ssllabs.com/) можно использовать для проверки состояния TLS ваших конечных точек. 

 
