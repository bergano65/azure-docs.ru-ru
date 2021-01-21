---
title: Устранение неполадок загрузки BitLocker на виртуальной машине Azure | Документация Майкрософт
description: Узнайте, как устранить неполадки загрузки BitLocker на виртуальной машине Azure
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2020
ms.author: genli
ms.custom: has-adal-ref
ms.openlocfilehash: 87bf311b5199ec187c24c28a42314d9dc6787998
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633033"
---
# <a name="bitlocker-boot-errors-on-an-azure-vm"></a>Ошибки загрузки BitLocker на виртуальной машине Azure

 В этой статье описаны ошибки BitLocker, которые могут возникнуть при запуске виртуальной машины Windows в Microsoft Azure.

## <a name="symptom"></a>Симптом

 Виртуальная машина Windows не запускается. При проверке скриншотов в окне [Диагностика загрузки](./boot-diagnostics.md) появляется одно из следующих сообщений об ошибке.

- Подключение USB-драйвера с ключом BitLocker

- Вы заблокированы. Введите ключ восстановления чтобы снова начать работу (макет клавиатуры: США). Неправильные входные данные вводились много раз, поэтому ваш компьютер был заблокирован для защиты вашей конфиденциальной информации. Чтобы получить ключ восстановления, перейдите в раздел https://windows.microsoft.com/recoverykeyfaq с другого компьютера или мобильного устройства. В случае необходимости ключ ID — XXXXXXX. Или можно вернуть компьютер в исходное состояние.

- Введите пароль для разблокировки этого диска [ ]. Нажмите клавишу Insert для просмотра пароля при вводе.
- Введите ключ восстановления. Загрузите ключ восстановления с USB-устройства.

## <a name="cause"></a>Причина

Эта проблема может возникнуть если виртуальная машина не сможет найти файл ключа восстановления BitLocker (BEK) для дешифрования зашифрованного диска.

## <a name="solution"></a>Решение

> [!TIP]
> Если у вас есть недавняя резервная копия виртуальной машины, можно попытаться [восстановить виртуальную машину из резервной копии](../../backup/backup-azure-arm-restore-vms.md) , чтобы устранить проблему загрузки.

Чтобы устранить эту проблему, закройте и отменяйте выделение виртуальной машины, а затем запустите ее. Эта операция заставляет виртуальную машину извлекать файл BEK из Azure Key Vault, а затем помещать его на зашифрованный диск. 

Если с помощью этого метода не удается устранить проблему, выполните следующие действия для восстановления BEK-файла вручную.

1. Сделайте снимок системного диска затронутой виртуальной машины в качестве резервной копии. Дополнительные сведения см. в статье [Создание моментального снимка](../windows/snapshot-copy-managed-disk.md).
2. [Подключите системный диск к виртуальной машине восстановления](troubleshoot-recovery-disks-portal-windows.md). Чтобы выполнить команду [Manage-bde](/windows-server/administration/windows-commands/manage-bde) на шаге 7, на виртуальной машине восстановления должна быть включена функция **Шифрование диска BitLocker** .

    Когда вы подключаете управляемый диск, может появиться сообщение об ошибке "диск содержит параметры шифрования и поэтому не может использоваться в качестве диска данных". В этом случае запустите следующий сценарий, чтобы повторить попытку подключения этого диска.

    ```Powershell
    $rgName = "myResourceGroup"
    $osDiskName = "ProblemOsDisk"
    # Set the EncryptionSettingsEnabled property to false, so you can attach the disk to the recovery VM.
    New-AzDiskUpdateConfig -EncryptionSettingsEnabled $false |Update-AzDisk -diskName $osDiskName -ResourceGroupName $rgName

    $recoveryVMName = "myRecoveryVM" 
    $recoveryVMRG = "RecoveryVMRG" 
    $OSDisk = Get-AzDisk -ResourceGroupName $rgName -DiskName $osDiskName;

    $vm = get-AzVM -ResourceGroupName $recoveryVMRG -Name $recoveryVMName 

    Add-AzVMDataDisk -VM $vm -Name $osDiskName -ManagedDiskId $osDisk.Id -Caching None -Lun 3 -CreateOption Attach 

    Update-AzVM -VM $vm -ResourceGroupName $recoveryVMRG
    ```
     Вы не можете подключить управляемый диск к виртуальной машине, которая была восстановлена из BLOB-объекта образа.

3. После подключения диска создайте удаленный рабочий стол для виртуальной машины восстановления, чтобы выполнять некоторые сценарии Azure PowerShell. Установите последнюю версию [Azure PowerShell](/powershell/azure/) на виртуальную машину восстановления.

4. Откройте расширенный сеанс Azure PowerShell (Запуск от имени администратора). Выполните приведенные ниже команды, чтобы войти в подписку Azure.

    ```Powershell
    Add-AzAccount -SubscriptionID [SubscriptionID]
    ```

5. Выполните следующий сценарий, чтобы проверить имя BEK-файла.

    ```powershell
    $vmName = "myVM"
    $vault = "myKeyVault"
    Get-AzKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.ContentType -match 'BEK')} `
            | Sort-Object -Property Created `
            | ft  Created, `
                @{Label="Content Type";Expression={$_.ContentType}}, `
                @{Label ="MachineName"; Expression = {$_.Tags.MachineName}}, `
                @{Label ="Volume"; Expression = {$_.Tags.VolumeLetter}}, `
                @{Label ="DiskEncryptionKeyFileName"; Expression = {$_.Tags.DiskEncryptionKeyFileName}}
    ```

    Ниже приведен пример выходных данных. В этом случае предполагается, что имя файла — EF7B2F5A-50C6-4637-0001-7F599C12F85C. BEK.

    ```
    Created               Content Type Volume MachineName DiskEncryptionKeyFileName
    -------               ------------ ------ ----------- -------------------------
    11/20/2020 7:41:56 AM BEK          C:\    myVM   EF7B2F5A-50C6-4637-0001-7F599C12F85C.BEK
    ```
    Если отображаются два дублированных тома, том, содержащий новую временную метку является текущим BEK-файлом, который используется виртуальной машиной восстановления.

    Если значение **Тип содержимого** — **зашифрованный BEK**, перейдите в меню [Сценарии ключа шифрования ключа (KEK)](#key-encryption-key-scenario).

    Теперь, когда имя BEK-файла для диска выбрано, необходимо создать секретное имя BEK-файла для разблокировки диска.

6.  Скачайте BEK-файл на диск восстановления. Следующий пример сохраняет BEK-файл в папку C:\BEK. Перед запуском скриптов убедитесь, что путь `C:\BEK\` существует.

    ```powershell
    $vault = "myKeyVault"
    $bek = "EF7B2F5A-50C6-4637-0001-7F599C12F85C"
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $vault -Name $bek
    $bstr = [Runtime.InteropServices.Marshal]::SecureStringToBSTR($keyVaultSecret.SecretValue)
    $bekSecretBase64 = [Runtime.InteropServices.Marshal]::PtrToStringAuto($bstr)
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "C:\BEK\DiskEncryptionKeyFileName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7.  Чтобы разблокировать подключенный диск с помощью файла BEK, выполните следующую команду.

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-0001-7F599C12F85C.BEK
    ```
    В этом примере диск F является подключенным диском ОС. Выберите правильное имя диска. 

8. После того как диск был успешно разблокирован с помощью ключа BEK, отсоедините диск от виртуальной машины восстановления, а затем Воссоздайте виртуальную машину с помощью этого нового диска ОС.

    > [!NOTE]
    > Замена диска ОС не поддерживается для виртуальных машин, использующих шифрование дисков.

9. Если новая виртуальная машина по-прежнему не может загрузиться в обычном режиме, попробуйте выполнить одно из следующих действий после разблокировки диска.

    - Приостановите защиту, чтобы временно отключить BitLocker, выполнив следующую команду:

    ```console
    manage-bde -protectors -disable F: -rc 0
    ```

    - Полностью расшифровать диск. Для этого выполните следующую команду:

    ```console
    manage-bde -off F:
    ```

### <a name="key-encryption-key-scenario"></a>Сценарий ключа шифрования ключа

Для сценария ключа шифрования ключа выполните следующие действия.

1. Убедитесь, что зарегистрированная учетная запись пользователя требует наличие "развернутого" разрешения в политиках доступа к хранилищу ключей в **USER|Key permissions|Cryptographic Operations|Unwrap Key**.
2. Сохраните следующий скрипт в. Файл PS1:

    ```powershell
    #Set the Parameters for the script
    param (
            [Parameter(Mandatory=$true)]
            [string] 
            $keyVaultName,
            [Parameter(Mandatory=$true)]
            [string] 
            $kekName,
            [Parameter(Mandatory=$true)]
            [string]
            $secretName,
            [Parameter(Mandatory=$true)]
            [string]
            $bekFilePath,
            [Parameter(Mandatory=$true)]
            [string] 
            $adTenant
            )
    # Load ADAL Assemblies
    $adal = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
    If ((Test-Path -Path $adal) -and (Test-Path -Path $adalforms)) { 

    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)
     }
     else
     {
    $adal="${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms ="${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)
     }  

    # Set well-known client ID for AzurePowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2" 
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURI = "https://vault.azure.net"
    # Set Authority to Azure AD Tenant
    $authority = "https://login.windows.net/$adtenant"
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $authResult = $authContext.AcquireTokenAsync($resourceAppIdURI, $clientId, $redirectUri, $platformParameters).result
    # Generate auth header 
    $authHeader = $authResult.CreateAuthorizationHeader()
    # Set HTTP request headers to include Authorization header
    $headers = @{'x-ms-version'='2014-08-01';"Authorization" = $authHeader}

    ########################################################################################################################
    # 1. Retrieve wrapped BEK
    # 2. Make KeyVault REST API call to unwrap the BEK
    # 3. Convert the Base64Url string returned by KeyVault unwrap to Base64 string 
    # 4. Convert Base64 string to bytes and write to the BEK file
    ########################################################################################################################

    #Get wrapped BEK and place it in JSON object to send to KeyVault REST API
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $keyVaultName -Name $secretName
    $bstr = [Runtime.InteropServices.Marshal]::SecureStringToBSTR($keyVaultSecret.SecretValue)
    $wrappedBekSecretBase64 = [Runtime.InteropServices.Marshal]::PtrToStringAuto($bstr)
    $jsonObject = @"
    {
    "alg": "RSA-OAEP",
    "value" : "$wrappedBekSecretBase64"
    }
    "@

    #Get KEK Url
    $kekUrl = (Get-AzKeyVaultKey -VaultName $keyVaultName -Name $kekName).Key.Kid;
    $unwrapKeyRequestUrl = $kekUrl+ "/unwrapkey?api-version=2015-06-01";

    #Call KeyVault REST API to Unwrap 
    $result = Invoke-RestMethod -Method POST -Uri $unwrapKeyRequestUrl -Headers $headers -Body $jsonObject -ContentType "application/json" -Debug

    #Convert Base64Url string returned by KeyVault unwrap to Base64 string
    $base64UrlBek = $result.value;
    $base64Bek = $base64UrlBek.Replace('-', '+');
    $base64Bek = $base64Bek.Replace('_', '/');
    if($base64Bek.Length %4 -eq 2)
    {
        $base64Bek+= '==';
    }
    elseif($base64Bek.Length %4 -eq 3)
    {
        $base64Bek+= '=';
    }

    #Convert base64 string to bytes and write to BEK file
    $bekFileBytes = [System.Convert]::FromBase64String($base64Bek);
    [System.IO.File]::WriteAllBytes($bekFilePath,$bekFileBytes)

    #Delete the key from the memory
    [Runtime.InteropServices.Marshal]::ZeroFreeBSTR($bstr)
    clear-variable -name wrappedBekSecretBase64
    ```
3. Задайте параметры. Сценарий будет обрабатывать секрет KEK для создания ключа BEK, а затем сохранит его в локальной папке на виртуальной машине восстановления. Если при выполнении скрипта появятся ошибки, см. раздел [Устранение неполадок сценария](#script-troubleshooting) .

4. После запуска сценария появится следующий результат.

    Расположение версии GAC                                                                              
    ---    -------        --------                                                                              
    False v 4.0.30319 C:\Program Филес\виндовсповершелл\модулес\аз.аккаунтс \. ..  False v 4.0.30319 C:\Program Филес\виндовсповершелл\модулес\аз.аккаунтс \. ..

    После завершения сценария отобразятся следующие выходные данные.

    ```output
    VERBOSE: POST https://myvault.vault.azure.net/keys/rondomkey/<KEY-ID>/unwrapkey?api-
    version=2015-06-01 with -1-byte payload
    VERBOSE: received 360-byte response of content type application/json; charset=utf-8
    ```

5. Чтобы разблокировать подключенный диск с помощью BEK-файла, выполните следующую команду.

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    В этом примере диск F является подключенным диском ОС. Выберите правильное имя диска. 

6. После того как диск был успешно разблокирован с помощью ключа BEK, отсоедините диск от виртуальной машины восстановления, а затем Воссоздайте виртуальную машину с помощью этого нового диска ОС. 

    > [!NOTE]
    > Замена диска ОС не поддерживается для виртуальных машин, использующих шифрование дисков.

7. Если новая виртуальная машина по-прежнему не может загрузиться в обычном режиме, попробуйте выполнить одно из следующих действий после разблокировки диска.

    - Приостановите защиту, чтобы временно отключить BitLocker, выполнив следующую команду:

    ```console
    manage-bde -protectors -disable F: -rc 0
    ```

    - Полностью расшифровать диск. Для этого выполните следующую команду:

    ```console
    manage-bde -off F:
    ```

## <a name="script-troubleshooting"></a>Устранение неполадок сценариев

**Ошибка: не удалось загрузить файл или сборку**

Эта ошибка возникает из-за неправильных путей к сборкам ADAL. Чтобы найти правильный путь, можно выполнить поиск по `Az.Accounts` папке.

**Ошибка: Get-AzKeyVaultSecret или Get-AzKeyVaultSecret не распознано как имя командлета**

Если вы используете старый модуль AZ PowerShell, необходимо изменить две команды на `Get-AzureKeyVaultSecret` и `Get-AzureKeyVaultSecret` .

**Примеры параметров**

| Параметры  | Пример значения  |Комментарии   |
|---|---|---|
|  $keyVaultName | myKeyVault2112852926  | Имя хранилища ключей, в котором хранится ключ |
|$kekName   |MyKey   | Имя ключа, используемого для шифрования виртуальной машины|
|$secretName   |7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D  | Имя секрета ключа виртуальной машины|
|$bekFilePath   |c:\bek\7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D. BEK |Путь для записи файла BEK.|
|$adTenant  |contoso.onmicrosoft.com   | Полное доменное имя или идентификатор GUID Azure Active Directory, на котором размещено хранилище ключей |
