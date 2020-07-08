---
title: Сертификация виртуальной машины Azure (Azure Marketplace)
description: Сведения о том, как тестировать предложение виртуальной машины и отправлять ее образ на коммерческой платформе.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 63f18556847a717322b00092b973f59877102a1d
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2020
ms.locfileid: "85963909"
---
# <a name="azure-virtual-machine-vm-image-certification"></a>Сертификация образа виртуальной машины Azure

В этой статье описывается, как выполнить тестирование и отправку образа виртуальной машины на коммерческой платформе, гарантировав ее соответствие актуальным требованиям к публикации в Azure Marketplace.

Перед отправкой предложения виртуальной машины выполните следующие шаги:

1. Получите и разверните сертификаты.
2. Разверните виртуальную машину Azure с помощью универсального образа.
3. Выполните проверки.

## <a name="create-and-deploy-certificates-for-azure-key-vault"></a>Создание и развертывание сертификатов для Azure Key Vault

В этом разделе объясняется, как создать и развернуть самозаверяющие сертификаты, необходимые для настройки подключения службы удаленного управления Windows (WinRM) к размещенной в Azure виртуальной машине.

### <a name="create-certificates-for-azure-key-vault"></a>Создание сертификатов для Azure Key Vault

Эта процедура состоит из трех шагов:

1. Создание сертификата безопасности.
2. Создание Azure Key Vault для хранения сертификата.
3. Сохранение сертификата в хранилище ключей.

Для выполнения этой работы можно использовать новую или имеющуюся группу ресурсов Azure.

#### <a name="create-the-security-certificate"></a>Создание сертификата безопасности

Измените и выполните следующий скрипт Azure PowerShell, чтобы создать PFX-файл сертификата в локальной папке. Замените в нем значения следующих параметров, как показано в таблице.

| **Параметр** | **Описание** |
| --- | --- |
| $certroopath | Локальная папка для сохранения PFX-файла. |
| $location | Одно из стандартных географических расположений Azure. |
| $vmName | Имя запланированной виртуальной машины Azure. |
| $certname | Имя сертификата, которое должно соответствовать полному доменному имени запланированной виртуальной машины. |
| $certpassword | Пароль для сертификатов должен совпадать с паролем, используемым для запланированной виртуальной машины. |
| | |

```PowerShell
   # Certification creation script

    # pfx certification stored path
    $certroopath = "C:\certLocation"

    # location of the resource group
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
> Чтобы не терять значения параметров, не закрывайте и не завершайте сеанс консоли Azure PowerShell на весь период выполнения этих шагов.

> [!WARNING]
> Если вы решите сохранить этот скрипт, для хранения следует использовать только безопасное расположение, так как он содержит сведения системы безопасности (пароль).

#### <a name="create-the-azure-key-vault-to-store-the-certificate"></a>Создание хранилища ключей Azure для хранения сертификата

Скопируйте содержимое представленного ниже шаблона в файл на локальном компьютере. В приведенном ниже примере скрипта это ресурс `C:\certLocation\keyvault.json`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "defaultValue":"isvkv0001",
      "metadata": {
        "description": "Name of the Vault"
      }
    },
    "tenantId": {
      "type": "string",
      "defaultValue":"72f988bf-86f1-41af-91ab-2d7cd011db47",
      "metadata": {
        "description": "Tenant Id of the subscription. Get using Get-AzureSubscription cmdlet or Get Subscription API"
      }
    },
    "objectId": {
      "type": "string",
      "defaultValue":"d55739bf-d5d6-4ce0-be1c-49ade53c4315",
      "metadata": {
        "description": "Object Id of the AD user. Get using Get-AzureADUser or Get-AzureADServicePrincipal cmdlets"
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": ["all"],
      "metadata": {
        "description": "Permissions to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": ["all"],
      "metadata": {
        "description": "Permissions to secrets in the vault. Valid values are: all, get, set, list, and delete."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "SKU for the vault"
      }
    },
    "enableVaultForDeployment": {
      "type": "bool",
      "defaultValue": true,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies if the vault is enabled for a VM deployment"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "[parameters('keyVaultName')]",
      "apiVersion": "2015-06-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "[parameters('enableVaultForDeployment')]",
        "tenantId": "[parameters('tenantId')]",
        "accessPolicies": [
          {
            "tenantId": "[parameters('tenantId')]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]"
            }
          }
        ],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        }
      }
    }
  ]
}

```

Измените и выполните следующий скрипт Azure PowerShell для создания Azure Key Vault и связанной группы ресурсов. Замените в нем значения следующих параметров, как показано в таблице.

| **Параметр** | **Описание** |
| --- | --- |
| $postfix | Случайная числовая строка, добавляемая к идентификаторам развертывания. |
| $rgName | Имя создаваемой группы ресурсов Azure. |
| $location | Одно из стандартных географических расположений Azure. |
| $kvTemplateJson | Путь к файлу (keyvault.json), содержащему шаблон Resource Manager для хранилища ключей. |
| $kvname | Имя нового хранилища ключей.|
|   |   |

```PowerShell
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

#### <a name="store-the-certificates-to-the-key-vault"></a>Сохранение сертификата в хранилище ключей

Сохраните сертификаты из PFX-файла в новом хранилище ключей, выполнив для этого следующий скрипт:

```PowerShell
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
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $objAzureKeyVaultSecret=Set-AzureKeyVaultSecret -VaultName $kvname -Name "ISVSecret$postfix" -SecretValue $secret
            echo $objAzureKeyVaultSecret.Id

```

## <a name="deploy-an-azure-vm-using-your-generalized-image"></a>Развертывание виртуальной машины Azure из универсального образа

В этом разделе описывается, как развернуть универсальный образ VHD для создания нового ресурса виртуальной машины Azure. Для этого процесса мы применим предоставленные ресурсы шаблона Azure Resource Manager и скрипта Azure PowerShell.

### <a name="prepare-an-azure-resource-manager-template"></a>Подготовка шаблона Azure Resource Manager

Скопируйте следующий шаблон Azure Resource Manager для развертывания виртуального жесткого диска в локальный файл с именем VHDtoImage.json. Следующий скрипт запрашивает расположение на локальном компьютере, чтобы использовать этот код JSON.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "userStorageAccountName": {
            "type": "string"
        },
        "userStorageContainerName": {
            "type": "string",
            "defaultValue": "vhds"
        },
        "dnsNameForPublicIP": {
            "type": "string"
        },
        "adminUserName": {
            "defaultValue": "isv",
            "type": "string"
        },
        "adminPassword": {
            "type": "securestring",
            "defaultValue": "Password@123"
        },
        "osType": {
            "type": "string",
            "defaultValue": "windows",
            "allowedValues": [
                "windows",
                "linux"
            ]
        },
        "subscriptionId": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "vmSize": {
            "type": "string"
        },
        "publicIPAddressName": {
            "type": "string"
        },
        "vmName": {
            "type": "string"
        },
        "virtualNetworkName": {
            "type": "string"
        },
        "nicName": {
            "type": "string"
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the KeyVault"
            }
        },
        "vaultResourceGroup": {
            "type": "string",
            "metadata": {
                "description": "Resource Group of the KeyVault"
            }
        },
        "certificateUrl": {
            "type": "string",
            "metadata": {
                "description": "Url of the certificate with version in KeyVault e.g. https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7"
            }
        },
        "vhdUrl": {
            "type": "string",
            "metadata": {
                "description": "VHD Url..."
            }
        }
    },
        "variables": {
            "addressPrefix": "10.0.0.0/16",
            "subnet1Name": "Subnet-1",
            "subnet2Name": "Subnet-2",
            "subnet1Prefix": "10.0.0.0/24",
            "subnet2Prefix": "10.0.1.0/24",
            "publicIPAddressType": "Dynamic",
            "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
            "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
        },
        "resources": [
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/publicIPAddresses",
                "name": "[parameters('publicIPAddressName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                    "dnsSettings": {
                        "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                    }
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/virtualNetworks",
                "name": "[parameters('virtualNetworkName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "addressSpace": {
                        "addressPrefixes": [
                            "[variables('addressPrefix')]"
                        ]
                    },
                    "subnets": [
                        {
                            "name": "[variables('subnet1Name')]",
                            "properties": {
                                "addressPrefix": "[variables('subnet1Prefix')]"
                            }
                        },
                        {
                            "name": "[variables('subnet2Name')]",
                            "properties": {
                                "addressPrefix": "[variables('subnet2Prefix')]"
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/networkInterfaces",
                "name": "[parameters('nicName')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                    "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "ipConfigurations": [
                        {
                            "name": "ipconfig1",
                            "properties": {
                                "privateIPAllocationMethod": "Dynamic",
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                                },
                                "subnet": {
                                    "id": "[variables('subnet1Ref')]"
                                }
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-06-15",
                "type": "Microsoft.Compute/virtualMachines",
                "name": "[parameters('vmName')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
                ],
                "properties": {
                    "hardwareProfile": {
                        "vmSize": "[parameters('vmSize')]"
                    },
                    "osProfile": {
                        "computername": "[parameters('vmName')]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "adminPassword": "[parameters('adminPassword')]",
                        "secrets": [
                            {
                                "sourceVault": {
                                    "id": "[resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                                },
                                "vaultCertificates": [
                                    {
                                        "certificateUrl": "[parameters('certificateUrl')]",
                                        "certificateStore": "My"
                                    }
                                ]
                            }
                        ],
                        "windowsConfiguration": {
                            "provisionVMAgent": "true",
                            "winRM": {
                                "listeners": [
                                    {
                                        "protocol": "http"
                                    },
                                    {
                                        "protocol": "https",
                                        "certificateUrl": "[parameters('certificateUrl')]"
                                    }
                                ]
                            },
                            "enableAutomaticUpdates": "true"
                        }
                    },
                    "storageProfile": {
                        "osDisk": {
                            "name": "[concat(parameters('vmName'),'-osDisk')]",
                            "osType": "[parameters('osType')]",
                            "caching": "ReadWrite",
                            "image": {
                                "uri": "[parameters('vhdUrl')]"
                            },
                            "vhd": {
                                "uri": "[variables('osDiskVhdName')]"
                            },
                            "createOption": "FromImage"
                        }
                    },
                    "networkProfile": {
                        "networkInterfaces": [
                            {
                                "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                            }
                        ]
                    },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[concat('http://', parameters('userStorageAccountName'), '.blob.core.windows.net')]"
                    }
                }
                }
            }
        ]
    }

```

Измените этот файл, указав значения следующих параметров:

| **Параметр** | **Описание** |
| --- | --- |
| ResourceGroupName | Имя существующей группы ресурсов Azure. Обычно используется та же группа репликации, что и для хранилища ключей. |
| TemplateFile | Полный путь к файлу VHDtoImage.json. |
| userStorageAccountName | Имя учетной записи хранения. |
| sNameForPublicIP | DNS-имя общедоступного IP-адреса в нижнем регистре. |
| subscriptionId | Идентификатор подписки Azure. |
| Расположение | Стандартное географическое расположение Azure для группы ресурсов. |
| vmName | Имя виртуальной машины. |
| vaultName | Имя хранилища ключей. |
| vaultResourceGroup | Группа ресурсов хранилища ключей. |
| certificateUrl | Веб-адрес (URL-адрес) сертификата, включая сохраненную в хранилище ключей версию, например: `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7`. |
| vhdUrl | Веб-адрес виртуального жесткого диска. |
| vmSize | Размер экземпляра виртуальной машины. |
| publicIPAddressName | Имя общедоступного IP-адреса. |
| virtualNetworkName | Имя виртуальной сети. |
| nicName | Имя сетевого адаптера для виртуальной сети. |
| adminUserName | Имя пользователя учетной записи администратора. |
| adminPassword | Пароль администратора. |
|   |   |

### <a name="deploy-an-azure-vm"></a>Развертывание виртуальной машины Azure

Скопируйте и измените приведенный ниже скрипт, указав нужные значения переменных `$storageaccount` и `$vhdUrl`. Выполните его, чтобы создать ресурс виртуальной машины Azure из существующего универсального диска VHD.

```PowerShell

# storage account of existing generalized VHD

$storageaccount = "testwinrm11815"

# generalized VHD URL

$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD

New-AzResourceGroupDeployment -Name"dplisvvm$postfix" -ResourceGroupName"$rgName" -TemplateFile"C:\certLocation\VHDtoImage.json" -userStorageAccountName"$storageaccount" -dnsNameForPublicIP"$vmName" -subscriptionId"$mysubid" -location"$location" -vmName"$vmName" -vaultName"$kvname" -vaultResourceGroup"$rgName" -certificateUrl$objAzureKeyVaultSecret.Id  -vhdUrl"$vhdUrl" -vmSize"Standard\_A2" -publicIPAddressName"myPublicIP1" -virtualNetworkName"myVNET1" -nicName"myNIC1" -adminUserName"isv" -adminPassword$pwd

```

## <a name="run-validations"></a>Выполнение проверок

Выполнить проверки на развернутом образе можно двумя способами:

- с помощью средств проверки сертификации для Azure Certitfied;
- с помощью API самопроверки.

### <a name="download-and-run-the-certification-test-tool"></a>Загрузка и запуск средства проверки сертификации

Средство проверки сертификации для Azure Certified выполняется на локальном компьютере Windows, но тестирует виртуальную машину Azure на базе Windows или Linux. Оно проверяет, можно ли использовать пользовательский образ виртуальной машины в Microsoft Azure и выполнены ли рекомендации и требования по подготовке виртуального жесткого диска. Это средство возвращает отчет о совместимости, который вы затем отправите на портал Центра партнеров с запросом на сертификацию виртуальной машины.

1. Скачайте и установите [средство проверки сертификации Azure](https://www.microsoft.com/download/details.aspx?id=44299) последней версии.
2. Откройте средство сертификации и щелкните **Запустить новую проверку**.
3. На экране **Test Information** (Сведения о проверке) введите **имя теста** для тестового запуска.
4. Выберите для виртуальной машины **платформу** Windows Server или Linux. Выбор платформы влияет на остальные параметры.
5. Если ваша виртуальная машина использует эту службу базы данных, установите флажок **Test for Azure SQL Database** (Тестирование для Базы данных SQL Azure).

### <a name="connect-the-certification-tool-to-a-vm-image"></a>Подключение средства сертификации к образу виртуальной машины

Средство подключается к виртуальным машинам Windows через [Azure PowerShell](https://docs.microsoft.com/powershell/), а к виртуальным машинам Linux — через [SSH.Net](https://www.ssh.com/ssh/protocol/).

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Подключение средства сертификации к образу виртуальной машины Linux

1. Выберите режим **SSH Authentication** (Аутентификация по SSH): Аутентификация по паролю или по файлу ключа.
2. При использовании аутентификации на основе пароля введите значения параметров **VM DNS Name** (DNS-имя виртуальной машины), **User name** (Имя пользователя) и **Password** (Пароль). Вы также можете изменить указанный по умолчанию **SSH Port** (Порт SSH).

    ![Средство проверки для Azure Certified, аутентификация по паролю для образа виртуальной машины Linux](media/avm-cert2.png)

3. При использовании проверки подлинности на основе файла ключа введите значения **DNS-имени**, **имени пользователя** и расположение **закрытого ключа**. Также можно предоставить значение **Passphrase** (Парольная фраза) или изменить указанный по умолчанию **SSH Port** (Порт SSH).

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Подключение средства сертификации к образу виртуальной машины Windows**

1. Введите полное доменное имя виртуальной машины, например MyVMName.Cloudapp.net, в поле **VM DNS name**.
2. Введите значения для **имени пользователя** и **пароля**.

    ![Средство проверки для Azure Certified, аутентификация по паролю для образа виртуальной машины под управлением Windows](media/avm-cert4.png)

### <a name="run-a-certification-test"></a>Выполнение теста сертификации

Завершив ввод параметров для образа виртуальной машины в средстве сертификации, щелкните **Test Connection** (Проверить подключение), чтобы создать рабочее подключение к виртуальной машине. Когда подключение будет проверено, нажмите кнопку **Далее**, чтобы начать тестирование. После завершения теста его результаты будут приведены в таблице. В столбце "Состояние" для каждого теста отображается одно из значений: Pass (Успех), Fail (Неудача) или Warning (Предупреждение). Если хотя бы один из тестов не будет пройден, образ _не будет_ сертифицирован. В этом случае ознакомьтесь с требованиями и сообщениями об ошибках, внесите предложенные изменения и повторно выполните тест.

После завершения автоматической проверки укажите дополнительную информацию об этом образе виртуальной машины, перейдя в окне **Questionnaire** (Анкета) на вкладки **General Assessment** (Общая оценка) и **Kernel Customization** (Настройка ядра), затем щелкните **Далее**.

На последнем экране вы можете предоставить дополнительные сведения, например данные для доступа по протоколу SSH к образу виртуальной машины Linux или пояснения по непройденным проверкам, если вы надеетесь получить исключение.

Наконец, щелкните **Generate Report** (Сформировать отчет), чтобы скачать результаты проверки и файлы журналов по всем выполненным тестам в комплекте с ответами на вопросы анкеты. Сохраните результаты в том же контейнере, что и диски VHD.

## <a name="next-step"></a>Следующий шаг

- [Распространенные проблемы с URI SAS и способы их устранения](common-sas-uri-issues.md)
