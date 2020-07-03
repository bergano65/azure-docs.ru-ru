---
title: Сертификация виртуальных машин Azure в Azure Marketplace
description: Узнайте, как протестировать и отправить предложение виртуальной машины в коммерческом магазине.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 9bd7e40855f30612b90cf28365c0b1410cd3e3d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81731125"
---
# <a name="azure-virtual-machine-vm-image-certification"></a>Сертификация образа виртуальной машины Azure

> [!NOTE]
> Вы перемещаете Управление предложениями виртуальных машин Azure из Портал Cloud Partner в центр партнеров. Пока ваши предложения не будут перенесены, следуйте инструкциям в статье [Создание сертификатов для Azure Key Vault](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-key-vault-cert) в портал Cloud Partner для управления вашими предложениями.

В этой статье описывается, как протестировать и отправить образ виртуальной машины в коммерческом магазине, чтобы убедиться, что он соответствует последним требованиям к публикации в Azure Marketplace.

Перед отправкой предложения виртуальной машины выполните следующие действия:

1. Создание и развертывание сертификатов.
2. Развертывание виртуальной машины Azure с помощью обобщенного образа.
3. Выполнить проверки.

## <a name="create-and-deploy-certificates-for-azure-key-vault"></a>Создание и развертывание сертификатов для Azure Key Vault

В этом разделе описывается создание и развертывание самозаверяющих сертификатов, необходимых для настройки подключения служба удаленного управления Windows (WinRM) к виртуальной машине, размещенной в Azure.

### <a name="create-certificates-for-azure-key-vault"></a>Создание сертификатов для Azure Key Vault

Эта процедура состоит из трех шагов:

1. Создание сертификата безопасности.
2. Создайте Azure Key Vault для хранения сертификата.
3. Храните сертификаты в хранилище ключей.

Для выполнения этой работы можно использовать новую или имеющуюся группу ресурсов Azure.

#### <a name="create-the-security-certificate"></a>Создание сертификата безопасности

Измените и выполните следующий сценарий Azure PowerShell, чтобы создать файл сертификата (PFX) в локальной папке. Замените значения параметров, приведенных в следующей таблице.

| **Параметр** | **Описание** |
| --- | --- |
| $certroopath | Локальная папка для сохранения PFX-файла. |
| $location | Одно из географических расположений Azure Standard. |
| $vmName | Имя запланированной виртуальной машины Azure. |
| $certname | Имя сертификата; должно соответствовать полному доменному имени запланированной виртуальной машины. |
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
> Оставьте один и тот же сеанс консоли Azure PowerShell открытым и выполняемым во время этих действий, чтобы хранить значения различных параметров.

> [!WARNING]
> Если сохранить этот скрипт, сохраните его в безопасном месте, так как он содержит сведения о безопасности (пароль).

#### <a name="create-the-azure-key-vault-to-store-the-certificate"></a>Создание хранилища ключей Azure для хранения сертификата

Скопируйте содержимое шаблона ниже в файл на локальном компьютере. В приведенном ниже примере скрипта этот ресурс `C:\certLocation\keyvault.json`—).

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

Измените и выполните следующий Azure PowerShell скрипт, чтобы создать Azure Key Vault и связанную с ним группу ресурсов. Замените значения для параметров, показанных в следующей таблице.

| **Параметр** | **Описание** |
| --- | --- |
| $postfix | Случайная числовая строка, присоединенная к идентификаторам развертывания. |
| $rgName | Имя создаваемой группы ресурсов Azure (RG). |
| $location | Одно из географических расположений Azure Standard. |
| $kvTemplateJson | Путь к файлу (keyvault. JSON), содержащему шаблон диспетчер ресурсов для хранилища ключей. |
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

#### <a name="store-the-certificates-to-the-key-vault"></a>Сохранение сертификатов в хранилище ключей

Храните сертификаты, содержащиеся в PFX-файле, в новое хранилище ключей с помощью этого сценария:

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

## <a name="deploy-an-azure-vm-using-your-generalized-image"></a>Развертывание виртуальной машины Azure с помощью обобщенного образа

В этом разделе описывается, как развернуть обобщенный образ VHD для создания нового ресурса виртуальной машины Azure. Для этого процесса мы будем использовать указанный шаблон Azure Resource Manager и скрипт Azure PowerShell.

### <a name="prepare-an-azure-resource-manager-template"></a>Подготовка шаблона Azure Resource Manager

Скопируйте следующий шаблон Azure Resource Manager для развертывания VHD в локальный файл с именем Вхдтоимаже. JSON. Следующий скрипт запросит расположение на локальном компьютере для использования этого JSON.

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

Измените этот файл, чтобы указать значения для этих параметров:

| **Параметр** | **Описание** |
| --- | --- |
| ResourceGroupName | Имя существующей группы ресурсов Azure. Как правило, используйте те же RG, что и для хранилища ключей. |
| TemplateFile | Полный путь к файлу Вхдтоимаже. JSON. |
| userStorageAccountName | Имя учетной записи хранения. |
| sNameForPublicIP | DNS-имя для общедоступного IP-адреса; должен быть строчным. |
| subscriptionId | Идентификатор подписки Azure. |
| Расположение | Стандартное географическое расположение Azure группы ресурсов. |
| vmName | Имя виртуальной машины. |
| vaultName | Имя хранилища ключей. |
| vaultResourceGroup | Группа ресурсов хранилища ключей. |
| certificateUrl | Веб-адрес (URL) сертификата, включая версию, хранящуюся в хранилище ключей, например: `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7`. |
| vhdUrl | Веб-адрес виртуального жесткого диска. |
| vmSize | Размер экземпляра виртуальной машины. |
| publicIPAddressName | Имя общедоступного IP-адреса. |
| virtualNetworkName | Имя виртуальной сети. |
| nicName | Имя сетевой карты для виртуальной сети. |
| adminUserName | Имя пользователя учетной записи администратора. |
| adminPassword | Пароль администратора. |
|   |   |

### <a name="deploy-an-azure-vm"></a>Развертывание виртуальной машины Azure

Скопируйте и измените следующий скрипт, чтобы предоставить значения для переменных `$storageaccount` и `$vhdUrl` . Выполните его, чтобы создать ресурс виртуальной машины Azure из существующего универсального диска VHD.

```PowerShell

# storage account of existing generalized VHD

$storageaccount = "testwinrm11815"

# generalized VHD URL

$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD

New-AzResourceGroupDeployment -Name"dplisvvm$postfix" -ResourceGroupName"$rgName" -TemplateFile"C:\certLocation\VHDtoImage.json" -userStorageAccountName"$storageaccount" -dnsNameForPublicIP"$vmName" -subscriptionId"$mysubid" -location"$location" -vmName"$vmName" -vaultName"$kvname" -vaultResourceGroup"$rgName" -certificateUrl$objAzureKeyVaultSecret.Id  -vhdUrl"$vhdUrl" -vmSize"Standard\_A2" -publicIPAddressName"myPublicIP1" -virtualNetworkName"myVNET1" -nicName"myNIC1" -adminUserName"isv" -adminPassword$pwd

```

## <a name="run-validations"></a>Выполнить проверки

Выполнять проверки на развернутом образе можно двумя способами:

- Использование средства проверки сертификатов для сертификации Azure
- Использование API самотестирования

### <a name="download-and-run-the-certification-test-tool"></a>Загрузка и запуск средства проверки сертификации

Средство проверки сертификации для сертифицированных Azure работает на локальном компьютере под управлением Windows, но тестирует виртуальную машину Windows или Linux на основе Azure. Он удостоверяет, что образ виртуальной машины пользователя можно использовать с Microsoft Azure и что были выполнены рекомендации и требования по подготовке виртуального жесткого диска. Выходные данные средства — это отчет о совместимости, который будет отправлен на портал центра партнеров для запроса сертификации виртуальной машины.

1. Скачайте и установите [средство проверки сертификации Azure](https://www.microsoft.com/download/details.aspx?id=44299) последней версии.
2. Откройте средство сертификации, а затем выберите **запустить новый тест**.
3. На экране **Test Information** (Сведения о проверке) введите **имя теста** для тестового запуска.
4. Выберите **платформу** для виртуальной машины: Windows Server или Linux. Выбор платформы влияет на остальные параметры.
5. Если виртуальная машина использует эту службу базы данных, установите флажок **тест для базы данных SQL Azure** .

### <a name="connect-the-certification-tool-to-a-vm-image"></a>Подключение средства сертификации к образу виртуальной машины

Средство подключается к виртуальным машинам под управлением Windows с [Azure PowerShell](https://docs.microsoft.com/powershell/) и подключается к виртуальным машинам Linux через [SSH.NET](https://www.ssh.com/ssh/protocol/).

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Подключение средства сертификации к образу виртуальной машины Linux

1. Выберите режим **проверки подлинности SSH** : Проверка пароля или проверка подлинности файла ключа.
2. Если используется проверка подлинности на основе пароля, введите значения для **DNS-имени виртуальной машины**, **имени пользователя**и **пароля**. Можно также изменить номер **порта SSH** по умолчанию.

    ![Средство проверки сертифицированного Azure, проверка пароля для образа виртуальной машины Linux](media/avm-cert2.png)

3. При использовании проверки подлинности на основе файла ключа введите значения **DNS-имени**, **имени пользователя** и расположение **закрытого ключа**. Можно также включить **парольную фразу** или изменить номер **порта SSH** по умолчанию.

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Подключение средства сертификации к образу виртуальной машины Windows**

1. Введите полное **DNS-имя виртуальной машины** (например, MyVMName.cloudapp.NET).
2. Введите значения для **имени пользователя** и **пароля**.

    ![Средство проверки сертифицированного Azure, проверка пароля для образа виртуальной машины под управлением Windows](media/avm-cert4.png)

### <a name="run-a-certification-test"></a>Выполнение теста сертификации

После того как вы задаете значения параметров для образа виртуальной машины в средстве сертификации, выберите **проверить подключение** , чтобы создать допустимое подключение к виртуальной машине. Когда подключение будет проверено, нажмите кнопку **Далее**, чтобы начать тестирование. По завершении теста результаты теста отображаются в таблице. В столбце Состояние отображается значение (успех/неудача/предупреждение) для каждого теста. Если хотя бы один из тестов не будет пройден, образ _не будет_ сертифицирован. В этом случае проверьте требования и сообщения о сбое, внесите предлагаемые изменения и запустите тест еще раз.

После завершения автоматического теста укажите дополнительные сведения о образе виртуальной машины на двух вкладках на экране **анкеты** , **общую оценку** и **настройку ядра**, а затем нажмите кнопку **Далее**.

Последний экран позволяет предоставить дополнительные сведения, такие как сведения о доступе к SSH для образа виртуальной машины Linux, а также пояснения к неудачным оценкам, если вы ищете исключения.

Наконец, выберите **создать отчет** , чтобы скачать результаты теста и файлы журнала для выполненных тестовых случаев вместе с ответами на анкету. Сохраните результаты в том же контейнере, что и диски VHD.

## <a name="next-step"></a>Дальнейшие действия

- [Создание универсальных идентификаторов ресурсов (URI) для каждого виртуального жесткого диска](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-get-sas-uri)
