---
title: Сценарий PowerShell. Поиск хранилища для учетной записи хранения
description: Сведения об использовании сценария Azure PowerShell для поиска хранилища служб восстановления, в котором зарегистрирована учетная запись хранения.
ms.topic: sample
ms.date: 1/28/2020
ms.openlocfilehash: 786420ec8cef6516f7261c71b40641693efece07
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76775870"
---
# <a name="powershell-script-to-find-the-recovery-services-vault-where-a-storage-account-is-registered"></a>Сценарий Powershell для поиска хранилища Служб восстановления, в котором зарегистрирована учетная запись хранения.

Сценарий позволяет найти хранилище служб восстановления, в котором зарегистрирована учетная запись хранения.

## <a name="sample-script"></a>Пример скрипта

```powershell
Param(
        [Parameter(Mandatory=$True)][System.String] $ResourceGroupName,
        [Parameter(Mandatory=$True)][System.String] $StorageAccountName,
        [Parameter(Mandatory=$True)][System.String] $SubscriptionId
    )

Connect-AzAccount
Select-AzSubscription -Subscription $SubscriptionId
$vaults = Get-AzRecoveryServicesVault
$found = $false
foreach($vault in $vaults)
{
  Write-Verbose "Checking vault: $($vault.Id)" -Verbose
  
  $containers = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -FriendlyName $StorageAccountName -ResourceGroupName $ResourceGroupName -VaultId $vault.Id -Status Registered
  
  if($containers -ne $null)
  {
    $found = $True
    Write-Information "Found Storage account $StorageAccountName registered in vault: $($vault.Id)" -InformationAction Continue
    break;
  }
}

if(!$found)
{
     Write-Information "Storage account: $StorageAccountName is not registered in any vault of this subscription" -InformationAction Continue
}
```

## <a name="how-to-execute-the-script"></a>Выполнение скрипта

1. Сохраните приведенный выше сценарий на компьютере, назвав его на свое усмотрение. В этом примере мы сохранили его как *FindRegisteredStorageAccount.ps1*.
2. Выполните скрипт, указав следующие параметры:

    * **-ResourceGroupName** — группа ресурсов учетной записи хранения;
    * **-StorageAccountName** — имя учетной записи хранения;
    * **-SubscriptionID** — идентификатор подписки, в которой содержится учетная запись хранения.

В следующем примере предпринимается попытка найти хранилище служб восстановления, в котором зарегистрирована учетная запись хранения *afsaccount*:

```powershell
.\FindRegisteredStorageAccount.ps1 -ResourceGroupName AzureFiles -StorageAccountName afsaccount -SubscriptionId ef4ad5a7-c2c0-4304-af80-af49f49af3d1
```

## <a name="output"></a>Выходные данные

В выходных данных отобразится полный путь к хранилищу служб восстановления, в котором зарегистрирована учетная запись хранения. Пример выходных данных:

```output
Found Storage account afsaccount registered in vault: /subscriptions/ ef4ad5a7-c2c0-4304-af80-af49f49af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault123
```

## <a name="next-steps"></a>Дальнейшие действия

См. сведения в [Back up Azure file shares in a Recovery Services vault](https://docs.microsoft.com/azure/backup/backup-afs) (Резервное копирование Общих папок Azure из портала Azure)
