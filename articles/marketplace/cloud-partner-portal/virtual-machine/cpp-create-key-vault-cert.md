---
title: Создание сертификата Azure Key Vault | Документация Майкрософт
description: Здесь описано, как зарегистрировать виртуальную машину из виртуального жесткого диска, развернутого в Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/29/2018
ms.author: pbutlerm
ms.openlocfilehash: a25418f30225184424011527def468d0d3909563
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045702"
---
# <a name="create-certificates-for-azure-key-vault"></a>Создание сертификатов для Azure Key Vault

В этой статье объясняется, как подготовить к работе самозаверяющие сертификаты, необходимые для установки подключения службы удаленного управления Windows (WinRM) к размещенной в Azure виртуальной машине (ВМ). Эта процедура состоит из трех шагов:

1.  Создание сертификата безопасности. 
2.  Создание Azure Key Vault для хранения этого сертификата. 
3.  Сохранение сертификатов в этом хранилище ключей. 

Для выполнения этой работы можно использовать новую или имеющуюся группу ресурсов Azure.  В приведенных ниже сведениях используется первый вариант.



[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="create-the-certificate"></a>Создание сертификата

Измените и выполните следующий скрипт Azure Powershell для создания файла сертификата (PFX) в локальной папке.  Необходимо заменить значения для следующих параметров:

|  **Параметр**        |   **ОПИСАНИЕ**                                                               |
|  -------------        |   ---------------                                                               |
| `$certroopath` | Локальная папка для сохранения PFX-файла  |
| `$location`    | Одно из стандартных географических расположений Azure  |
| `$vmName`      | Имя запланированной виртуальной машины Azure.   |
| `$certname`    | Имя сертификата, которое должно соответствовать полному доменному имени запланированной виртуальной машины  |
| `$certpassword` | Пароль для сертификатов должен совпадать с паролем, используемым для запланированной виртуальной машины  |
|  |  |

```powershell
    # Certification creation script 
    
    # pfx certification stored path
    $certroopath = "C:\certLocation"
    
    #location of the resource group
    $location = "westus"
    
    # Azure virtual machine name that we are going to create
    $vmName = "testvm000000906"
    
    # Certification name - should match with FQDN of Windows Azure creating VM 
    $certname = "$vmName.$location.cloudapp.azure.com"
    
    # Certification password - should be match with password of Windows Azure creating VM 
    $certpassword = "SecretPassword@123"
    
    $cert=New-SelfSignedCertificate -DnsName "$certname" -CertStoreLocation cert:\LocalMachine\My
    $pwd = ConvertTo-SecureString -String $certpassword -Force -AsPlainText
    $certwithThumb="cert:\localMachine\my\"+$cert.Thumbprint
    $filepath="$certroopath\$certname.pfx"
    Export-PfxCertificate -cert $certwithThumb -FilePath $filepath -Password $pwd
    Remove-Item -Path $certwithThumb 

```
> [!TIP]
> Таким образом, для сохранения значений различных параметров необходимо поддерживать активным один и тот же сеанс консоли PowerShell при выполнении всех этих действий.

> [!WARNING]
> Так как этот скрипт содержит сведения системы безопасности (пароль), для его хранения следует использовать только безопасное расположение.


## <a name="create-the-key-vault"></a>Создание хранилища ключей

Скопируйте содержимое [шаблона развертывания хранилища ключей](./cpp-key-vault-deploy-template.md) в файл на локальном компьютере. (В приведенном ниже примере скрипта этот ресурс находится здесь: `C:\certLocation\keyvault.json`.)  Измените и выполните следующий скрипт Azure Powershell для создания экземпляра Azure Key Vault и связанной группы ресурсов.  Необходимо заменить значения для следующих параметров:

|  **Параметр**        |   **ОПИСАНИЕ**                                                               |
|  -------------        |   ---------------                                                               |
| `$postfix`            | Произвольная числовая строка, добавляемая в конце идентификаторов развертывания                     |
| `$rgName`             | Имя создаваемой группы ресурсов Azure                                        |
|  `$location`          | Одно из стандартных географических расположений Azure                                  |
| `$kvTemplateJson`     | Путь к файлу (keyvault.json), содержащему шаблон Resource Manager для хранилища ключей |
| `$kvname`             | Имя нового хранилища ключей                                                       |
|  |  |

```powershell
    # Creating Key vault in resource group
    
    # "Random" number for deployment identifiers
    $postfix = "0101048"
    
    # Resource group name
    $rgName = "TestRG$postfix"
    
    # Location of Resource Group
    $location = "westus"
    
    # Key vault template location
    $kvTemplateJson = "C:\certLocation\keyvault.json"
    
    # Key vault name
    $kvname = "isvkv$postfix"
    
    # code snippet to get the Azure user object ID
    try
       {
        $accounts = Get-AzureAccount
        $accountNum = 0
        $accounts.Id | %{ ++$accountNum; Write-Host $accountNum $_}
        Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
        [Int] $accountChoice = Read-Host 
        
        While($accountChoice -lt 1 -or $accountChoice -gt $accounts.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red 
            Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
            [Int] $accountChoice = Read-Host
        }
        
        $accountSelected = $accounts[$accountChoice-1]
        echo $accountSelected
        $id = $accountSelected.Id
                              
        Write-Host "User $id Selected"
        $myobjectId=(Get-AzADUser -Mail $id)[0].Id
      }
      catch
      {
      Write-Host $_.Exception.Message    
      Break
      } 

    # code snippet to get Azure User Tenant Id
      # SELECT Subscriptions
        #**************************************
        try
        {
        $subslist=Get-AzureSubscription
        for($i=1; $i -le $subslist.Length;$i++)
        {
           Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
        }
        Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
        [int] $selectedsub=Read-Host
    
        While($selectedsub -lt 1 -or $selectedsub -gt $subslist.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red 
            for($i=1; $i -le $subslist.Length;$i++)
             {
              Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
             }
            Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
           [int] $selectedsub=Read-Host
        }
        if($selectedsub -ge 1 -and $selectedsub -le $subslist.Length)
        {
        $mysubid=$subslist[$selectedsub-1].SubscriptionId
        $mysubName=$subslist[$selectedsub-1].SubscriptionName
        $mytenantId=$subslist[$selectedsub-1].TenantId
        Write-Host "$mysubName selected"
        }
        }
        catch
        {
        Write-Host $_.Exception.Message    
        Break
        }
    
    # Create a resource group
     Write-Host "Creating Resource Group $rgName"
     Create-ResourceGroup -rgName $rgName -location $location
     Write-Host "-----------------------------------" 
    
    # Create key vault and configure access
    New-AzResourceGroupDeployment -Name "kvdeploy$postfix" -ResourceGroupName $rgName -TemplateFile $kvTemplateJson -keyVaultName $kvname -tenantId $mytenantId -objectId $myobjectId
    
    Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $myobjectId -PermissionsToKeys all -PermissionsToSecrets all 
        
```

## <a name="store-the-certificate"></a>Сохранение сертификата

Теперь можно сохранить сертификаты, содержащиеся в PFX-файле, в новом хранилище ключей, выполнив для этого следующий скрипт. 

```powershell
    #push certificate to key vault secret

     $fileName =$certroopath+"\$certname"+".pfx" 
      
     $fileContentBytes = get-content $fileName -Encoding Byte
     $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    
            $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certpassword"
    }
    "@
            echo $certpassword
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
            $objAzureKeyVaultSecret=Set-AzureKeyVaultSecret -VaultName $kvname -Name "ISVSecret$postfix" -SecretValue $secret
            echo $objAzureKeyVaultSecret.Id 
    
```


## <a name="next-steps"></a>Дальнейшие действия

Далее следует [развернуть виртуальную машину из образа ВМ пользователя](./cpp-deploy-vm-user-image.md).
