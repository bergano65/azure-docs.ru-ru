---
title: Смена сертификата в кластере Azure Service Fabric | Документация Майкрософт
description: Узнайте, как сменить сертификат кластера Service Fabric, идентифицируемый по общему имени сертификата.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: aljo
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: aljo
ms.openlocfilehash: dd4b6026772a20c522532e1ba65c6846addfa161
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59046365"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Смена сертификата для кластера Service Fabric вручную
Если срок действия для сертификата кластера Service Fabric подходит к концу, необходимо обновить сертификат.  Сменить сертификат не сложно, если кластер [настроен для использования сертификатов на основе общего имени](service-fabric-cluster-change-cert-thumbprint-to-cn.md) (вместо отпечатка).  Получите новый сертификат из центра сертификации с новой датой окончания срока действия.  Самозаверяющие сертификаты не поддерживаются производственными кластерами Service Fabric, включая сертификаты, созданные при выполнении рабочего процесса создания кластера на портале Azure. Новый сертификат должен иметь то же общее имя, что и старый. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Когда на узле установлено несколько сертификатов проверки, кластер Service Fabric будет автоматически использовать объявленный сертификат с дальнейшей датой истечения срока действия. Для подготовки ресурсов Azure мы рекомендуем использовать шаблон Resource Manager. Для непроизводственной среды приведенный ниже сценарий можно использовать, чтобы отправить новый сертификат в хранилище ключей. Затем он устанавливается в масштабируемом наборе виртуальных машин. 

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  <subscription ID>

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "keyvaultgroup"
$VaultName = "cntestvault2"
$certFilename = "C:\users\sfuser\sftutorialcluster20180419110824.pfx"
$certname = "cntestcert"
$Password  = "!P@ssw0rd321"
$VmssResourceGroupName     = "sfclustertutorialgroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Get the key vault.  The key vault must be enabled for deployment.
$keyVault = Get-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName 
$resourceId = $keyVault.ResourceId  

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

>[!NOTE]
> Так как каждый секрет является уникальным ресурсом с контролем версий, секреты масштабируемого набора виртуальных машин в Вычислительных средах не поддерживают один идентификатор ресурса для двух отдельных секретов. 

Дополнительные сведения см. в следующих статьях:
* Дополнительные сведения о безопасности кластеров см. в статье [Сценарии защиты кластера Service Fabric](service-fabric-cluster-security.md).
* [Обновление сертификатов кластера и управление ими](service-fabric-cluster-security-update-certs-azure.md)

