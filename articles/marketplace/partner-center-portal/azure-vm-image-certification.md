---
title: Сертификация виртуальных машин Azure - Azure Marketplace
description: Узнайте, как протестировать и представить предложение виртуальной машины на коммерческом рынке.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 009a8e3db097790788f71486431a3b5b05c488ea
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81265971"
---
# <a name="azure-virtual-machine-vm-image-certification"></a>Сертификация изображений виртуальной машины Azure (VM)

> [!NOTE]
> Мы перемещаем управление вашими предложениями Azure VM с облачного партнерского портала в партнерский центр. До тех пор, пока ваши предложения не будут перенесены, пожалуйста, продолжайте следовать инструкциям в [сертификатах «Создание» для портала Azure Key Vault](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-key-vault-cert) в облачном партнерской сети для управления вашими предложениями.

В этой статье описывается, как протестировать и отправить изображение виртуальной машины (VM) на коммерческом рынке, чтобы убедиться, что оно соответствует последним требованиям публикации Azure Marketplace.

Выполнить эти шаги, прежде чем представить свое предложение VM:

1. Создание и развертывание сертификатов.
2. Развертывание VM Azure с помощью обобщенного изображения.
3. Выполнить проверки.

## <a name="create-and-deploy-certificates-for-azure-key-vault"></a>Создание и развертывание сертификатов для Убежища ключей Azure

В этом разделе описывается, как создавать и развертывать самоподписанные сертификаты, необходимые для настройки подключения Windows Remote Management (WinRM) к виртуальной машине, размещенной в Azure.

### <a name="create-certificates-for-azure-key-vault"></a>Создание сертификатов для Azure Key Vault

Эта процедура состоит из трех шагов:

1. Создание сертификата безопасности.
2. Создайте Хранилище ключей Azure для хранения сертификата.
3. Храните сертификаты в хранилище ключей.

Для выполнения этой работы можно использовать новую или имеющуюся группу ресурсов Azure.

#### <a name="create-the-security-certificate"></a>Создание сертификата безопасности

Отспособите и запустите следующий скрипт Azure PowerShell для создания файла сертификата (.pfx) в локальной папке. Замените значения для параметров, показанных в следующей таблице.

| **Параметр** | **Описание** |
| --- | --- |
| $certroopath | Локальная папка для сохранения файла .pfx. |
| $location | Одно из стандартных географических местоположений Azure. |
| $vmName | Название запланированной виртуальной машины Azure. |
| $certname | Наименование сертификата; должны соответствовать полностью квалифицированной доменное имя запланированного VM. |
| $certpassword | Пароль для сертификатов, должны соответствовать паролю, используемому для запланированного VM. |
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
> Держите ту же консольную сессию Azure PowerShell открытой и запущенной во время этих шагов, чтобы сохранить значения различных параметров.

> [!WARNING]
> Если вы сохраните этот скрипт, сохраните его только в безопасном месте, поскольку он содержит информацию о безопасности (пароль).

#### <a name="create-the-azure-key-vault-to-store-the-certificate"></a>Создание хранилища ключей Azure для хранения сертификата

Копируйте содержимое шаблона ниже в файл на локальной машине. В приведенном ниже примере `C:\certLocation\keyvault.json`сценария этот ресурс).

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

Отспособите и запустите следующий скрипт Azure PowerShell для создания Убежища ключей Azure и связанной с ними группы ресурсов. Заменить значения для параметров, показанных в следующей таблице

| **Параметр** | **Описание** |
| --- | --- |
| $postfix | Случайная числовая строка, прикрепленная к идентификаторам развертывания. |
| $rgName | Для создания группы ресурсов Azure (RG) будет создано имя. |
| $location | Одно из стандартных географических местоположений Azure. |
| $kvTemplateJson | Путь файла (keyvault.json), содержащий шаблон управления ресурсами для хранилища ключей. |
| $kvname | Название нового хранилища ключей.|
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

#### <a name="store-the-certificates-to-the-key-vault"></a>Храните сертификаты в хранилище ключей

Храните сертификаты, содержащиеся в файле .pfx, в новом хранилище ключей с помощью этого скрипта:

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

## <a name="deploy-an-azure-vm-using-your-generalized-image"></a>Развертывание Azure VM с помощью обобщенного изображения

В этом разделе описывается, как развернуть обобщенное изображение VHD для создания нового ресурса Azure VM. Для этого процесса мы будем использовать предоставленный шаблон Azure Resource Manager и скрипт Azure PowerShell.

### <a name="prepare-an-azure-resource-manager-template"></a>Подготовьте шаблон менеджера ресурсов Azure

Скопируйте следующий шаблон менеджера ресурсов Azure для развертывания VHD в локальном файле под названием VHDtoImage.json. Следующий скрипт будет запрашивать местоположение на локальной машине, чтобы использовать этот JSON.

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

Отспособьте этот файл, чтобы упросмотреть значения для следующих параметров:

| **Параметр** | **Описание** |
| --- | --- |
| ResourceGroupName | Имя существующей группы ресурсов Azure. Как правило, используйте ту же RG, что и хранилище ключей. |
| TemplateFile | Полное имя пути к файлу VHDtoImage.json. |
| userStorageAccountName | Имя учетной записи хранения. |
| sNameForPublicIP | DNS название для общественной ИС; должны быть нижний регистр. |
| subscriptionId | Идентификатор подписки Azure. |
| Расположение | Географическое положение группы ресурсов Standard Azure. |
| vmName | Название виртуальной машины. |
| vaultName | Название хранилища ключей. |
| vaultResourceGroup | Группа ресурсов хранилища ключей. |
| certificateUrl | Веб-адрес (URL) сертификата, включая версию, хранящуюся в хранилище ключей, например: `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7`. |
| vhdUrl | Веб-адрес виртуального жесткого диска. |
| vmSize | Размер экземпляра виртуальной машины. |
| publicIPAddressName | Название публичного IP-адреса. |
| virtualNetworkName | Название виртуальной сети. |
| nicName | Название карты сетевого интерфейса для виртуальной сети. |
| adminUserName | Имя пользователя учетной записи администратора. |
| adminPassword | Пароль администратора. |
|   |   |

### <a name="deploy-an-azure-vm"></a>Развертывание Azure VM

Копировать и отсылать следующий `$storageaccount` сценарий, чтобы обеспечить значения для переменных и `$vhdUrl` переменных. Выполните его, чтобы создать ресурс виртуальной машины Azure из существующего универсального диска VHD.

```PowerShell

# storage account of existing generalized VHD

$storageaccount = "testwinrm11815"

# generalized VHD URL

$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD

New-AzResourceGroupDeployment -Name"dplisvvm$postfix" -ResourceGroupName"$rgName" -TemplateFile"C:\certLocation\VHDtoImage.json" -userStorageAccountName"$storageaccount" -dnsNameForPublicIP"$vmName" -subscriptionId"$mysubid" -location"$location" -vmName"$vmName" -vaultName"$kvname" -vaultResourceGroup"$rgName" -certificateUrl$objAzureKeyVaultSecret.Id  -vhdUrl"$vhdUrl" -vmSize"Standard\_A2" -publicIPAddressName"myPublicIP1" -virtualNetworkName"myVNET1" -nicName"myNIC1" -adminUserName"isv" -adminPassword$pwd

```

## <a name="run-validations"></a>Выполнить проверку

Существует два способа выполнения проверки развернутого изображения:

- Используйте инструмент сертификации для сертифицированных Azure
- Используйте API самотестирования

### <a name="download-and-run-the-certification-test-tool"></a>Загрузка и запуск средства проверки сертификации

Инструмент сертификационного тестирования для сертифицированных Azure работает на локальной машине Windows, но тестирует Windows или Linux VM на базе Azure. Он подтверждает, что изображение VM-пользователя может быть использовано в Microsoft Azure и что рекомендации и требования, связанные с подготовкой VHD, были выполнены. Выход инструмента представляет собой отчет о совместимости, который вы загрузите на портал Partner Center для запроса сертификации VM.

1. Скачайте и установите [средство проверки сертификации Azure](https://www.microsoft.com/download/details.aspx?id=44299) последней версии.
2. Откройте инструмент сертификации, а затем выберите **Start New Test.**
3. На экране **Test Information** (Сведения о проверке) введите **имя теста** для тестового запуска.
4. Выберите **платформу** для ВМ, windows Server или Linux. Выбор платформы влияет на остальные параметры.
5. Если ваш VM использует эту службу баз данных, выберите флажок **базы данных Azure S'L.**

### <a name="connect-the-certification-tool-to-a-vm-image"></a>Подключение средства сертификации к образу виртуальной машины

Инструмент подключается к VMs на базе Windows с [Azure PowerShell](https://docs.microsoft.com/powershell/) и подключается к Linux VMs [через SSH.Net.](https://www.ssh.com/ssh/protocol/)

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Подключение средства сертификации к образу виртуальной машины Linux

1. Выберите режим **проверки подлинности SSH:** Проверка подлинности пароля или проверка подлинности ключей.
2. При использовании проверки подлинности на основе паролей введите значения для **имени VM DNS,** **имени пользователя**и **пароля.** Вы также можете изменить номер **порта SSH** по умолчанию.

    ![Сертифицированный тестовый инструмент Azure, проверка пароля Linux VM Image](media/avm-cert2.png)

3. При использовании проверки подлинности на основе файла ключа введите значения **DNS-имени**, **имени пользователя** и расположение **закрытого ключа**. Вы также можете включить **Passphrase** или изменить номер **порта SSH** по умолчанию.

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Подключение средства сертификации к образу виртуальной машины Windows**

1. Введите полностью квалифицированное **имя VM DNS** (например, MyVMName.Cloudapp.net).
2. Введите значения для **имени пользователя** и **пароля**.

    ![Сертифицированный тестовый инструмент Azure, проверка пароля VM Image](media/avm-cert4.png)

### <a name="run-a-certification-test"></a>Выполнение теста сертификации

После того, как вы предоставили значения параметров для вашего изображения VM в инструменте сертификации, выберите **Test Connection** для создания действительного соединения с вашим VM. Когда подключение будет проверено, нажмите кнопку **Далее**, чтобы начать тестирование. Когда тест завершен, результаты теста отображаются в таблице. Столбец состояния показывает (Pass/Fail/Warning) для каждого теста. Если хотя бы один из тестов не будет пройден, образ _не будет_ сертифицирован. В этом случае просмотрите требования и сообщения о сбое, внедучите изменения и снова запустите тест.

После завершения автоматизированного теста предоставьте дополнительную информацию о изображении VM на двух вкладок экрана **анкеты,** **общей оценки** и **настройки ядра,** а затем выберите **следующий**.

Последний экран позволяет предоставить больше информации, например, sSH доступ к изображению Linux VM, и объяснение неудавшихся оценок, если вы ищете исключения.

Наконец, выберите **Generate Report** для загрузки результатов теста и журналфайлов для выполненных тестовых случаев вместе с ответами на вопросник. Сохраните результаты в том же контейнере, что и диски VHD.

## <a name="next-step"></a>Следующий шаг

- [Создание единых идентификаторов ресурсов (URI) для каждого VHD](https://aka.ms/AzureSASURI)
