---
title: Включение резервного копирования для Azure Stack с помощью PowerShell | Документация Майкрософт
description: Включите службу резервного копирования инфраструктуры с помощью Windows PowerShell для восстановления Azure Stack в случае сбоя.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 280a811e943c2e81a96875e3c8ba8efdb86fbf2a
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56004831"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Включение резервного копирования для Azure Stack с помощью PowerShell

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Включите службу "Резервное копирование инфраструктуры" с помощью Windows PowerShell, чтобы периодически создавать резервные копии:
 - внутренней службы идентификации и корневого сертификата;
 - планов, предложений и подписок пользователей;
 - квота сетевого пользователя, хранилища и вычисления;
 - секреты хранилища ключей пользователя;
 - ролей и политик RBAC пользователей.
 - пользовательские учетные записи хранения.

Чтобы включить резервное копирование, начать его и получить сведения о результатах резервного копирования через конечную точку управления оператором, вы можете использовать командлеты PowerShell.

## <a name="prepare-powershell-environment"></a>Подготовка среды PowerShell

Инструкции по настройке среды PowerShell приведены в разделе [Установка PowerShell для Azure Stack](azure-stack-powershell-install.md). Сведения о том, как войти в Azure Stack, см. в статье [Настройка среды оператора и вход в Azure Stack](azure-stack-powershell-configure-admin.md).

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Предоставление прав на совместное использование резервной копии, учетных данных и ключа шифрования для включения резервного копирования

В том же сеансе PowerShell измените следующий сценарий PowerShell, добавив переменные среды. Выполните обновленный сценарий, чтобы предоставить службе архивации инфраструктуры права на совместное использование учетных данных и ключа шифрования.

| Переменная        | ОПИСАНИЕ   |
|---              |---                                        |
| $username       | Введите **имя пользователя**, указав домен и имя пользователя для расположения общего диска. У этого пользователя должны быть достаточные права на чтение и запись файлов. Например, `Contoso\backupshareuser`. |
| $password       | Введите **пароль** пользователя. |
| $sharepath      | Введите путь к **расположению хранилища резервных копий**. В качестве пути к общей папке на отдельном устройстве нужно использовать UNC-строку. UNC-строка указывает расположение ресурсов, например общих файлов или устройств. Чтобы обеспечить доступность резервных копий, устройство должно находиться в отдельном расположении. |
| $frequencyInHours | Частота, измеряемая в часах, определяет, как часто создаются резервные копии. По умолчанию используется значение 12. Планировщик поддерживает число, находящееся в диапазоне межу 4 и 12.|
| $retentionPeriodInDays | Период хранения в днях определяет, сколько дней резервные копии будут храниться во внешнем расположении. По умолчанию используется значение 7. Планировщик поддерживает число, находящееся в диапазоне межу 2 и 14. Резервные копии, период хранения которых превысил допустимое значение, автоматически удаляются из внешнего расположения.|
| $encryptioncertpath | Путь к сертификату шифрования указывает путь к CER-файлу с помощью открытого ключа, используемого для шифрования данных. |
|     |     |

```powershell
    # Example username:
    $username = "domain\backupadmin"
 
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString

    # Create a self-signed certificate using New-SelfSignedCertificate, export the public key portion and save it locally.

    $cert = New-SelfSignedCertificate `
        -DnsName "www.contoso.com" `
        -CertStoreLocation "cert:\LocalMachine\My" 

    New-Item -Path "C:\" -Name "Certs" -ItemType "Directory" 

    #make sure to export the PFX format of the certificate with the public and private keys and then delete the certifcate from the local certificate store of the machine where you created the certificate
    
    Export-Certificate `
        -Cert $cert `
        -FilePath c:\certs\AzSIBCCert.cer 

    # Set the backup settings with the name, password, share, and CER certificate file.
    Set-AzsBackupConfiguration -BackupShare $sharepath -Username $username -Password $password -EncryptionCertPath "c:\temp\cert.cer"
```
   
##  <a name="confirm-backup-settings"></a>Подтверждение параметров архивации

В этом же сеансе PowerShell выполните следующие команды:

   ```powershell
    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName
   ```

В результате должно отобразиться следующее.

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
   ```

## <a name="update-backup-settings"></a>Обновление резервного набора данных
В этом же сеансе PowerShell можно обновить значения по умолчанию для срока хранения и частоту резервного копирования. 

   ```powershell
    #Set the backup frequency and retention period values.
    $frequencyInHours = 10
    $retentionPeriodInDays = 5

    Set-AzsBackupConfiguration -BackupFrequencyInHours $frequencyInHours -BackupRetentionPeriodInDays $retentionPeriodInDays

    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
   ```

В результате должно отобразиться следующее.

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
    BackupFrequencyInHours      : 10
    BackupRetentionPeriodInDays : 5
   ```

###<a name="azure-stack-powershell"></a>PowerShell для Azure Stack 
Set-AzsBackupConfiguration — это командлет PowerShell для настройки резервного копирования инфраструктуры. В предыдущих выпусках этим командлетом был Set-AzsBackupShare. Этот командлет требует наличия сертификата. Если резервное копирование инфраструктуры настроено с помощью ключа шифрования, вы не сможете обновить ключ шифрования или просмотреть свойства. Вам нужно использовать администратора PowerShell версии 1.6. 

Если резервное копирование инфраструктуры настроено перед обновлением до версии 1901, вы можете использовать версию 1.6 администратора PowerShell, чтобы настроить и просмотреть ключ шифрования. Версия 1.6 не позволит вам обновить ключ шифрования до файла сертификата.
Дополнительные сведения об установке правильной версии модуля см. в статье [Установка PowerShell для Azure Stack](azure-stack-powershell-install.md). 


## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о резервном копировании см. в статье [Резервное копирование для Azure Stack](azure-stack-backup-back-up-azure-stack.md)

Дополнительные сведения о подтверждении завершения резервного копирования см. в [этой статье](azure-stack-backup-back-up-azure-stack.md)
