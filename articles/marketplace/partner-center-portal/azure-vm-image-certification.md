---
title: Проверка образа виртуальной машины Azure — Azure Marketplace
description: Сведения о том, как тестировать предложение виртуальной машины и отправлять ее образ на коммерческой платформе.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: fd8f41f88b6184eee15477c460dc9d2e521d25e6
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2020
ms.locfileid: "89144193"
---
# <a name="azure-virtual-machine-image-validation"></a>Проверка образа виртуальной машины Azure

В этой статье описывается, как выполнить тестирование и отправку образа виртуальной машины на коммерческой платформе, гарантировав ее соответствие актуальным требованиям к публикации в Azure Marketplace.

Перед отправкой предложения виртуальной машины выполните следующие шаги:

- Разверните виртуальную машину Azure с помощью универсального образа.
- Выполните проверки.

## <a name="deploy-an-azure-vm-using-your-generalized-image"></a>Развертывание виртуальной машины Azure из универсального образа

В этом разделе описывается, как развернуть обобщенный образ виртуального жесткого диска (VHD) для создания нового ресурса виртуальной машины Azure. Для этого процесса мы будем использовать указанный шаблон Azure Resource Manager и скрипт Azure PowerShell.

### <a name="prepare-an-azure-resource-manager-template"></a>Подготовка шаблона Azure Resource Manager

В этом разделе описано создание и развертывание образа виртуальной машины (VM) пользователя. Это можно сделать, предоставив образы VHD операционной системы и диска данных с помощью виртуального жесткого диска, развернутого в Azure. Эти действия используются для развертывания виртуальной машины с помощью обобщенного виртуального жесткого диска.

1. Войдите на [портал Azure](https://portal.azure.com/).
2. Передайте обобщенные виртуальные жесткие диски с операционной системой и VHD-диски данных в учетную запись хранения Azure.
3. На домашней странице выберите **создать ресурс**, выполните поиск по запросу "развертывание шаблона" и выберите **создать**.
4. Выберите **Создать собственный шаблон в редакторе**.

    :::image type="content" source="media/vm/template-deployment.png" alt-text="Отображает выбор шаблона.":::

5. Вставьте следующий шаблон JSON в редактор и нажмите кнопку **сохранить**.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "userStorageAccountName": {
            "type": "String"
        },
        "userStorageContainerName": {
            "defaultValue": "vhds",
            "type": "String"
        },
        "dnsNameForPublicIP": {
            "type": "String"
        },
        "adminUserName": {
            "defaultValue": "isv",
            "type": "String"
        },
        "adminPassword": {
            "defaultValue": "",
            "type": "SecureString"
        },
        "osType": {
            "defaultValue": "windows",
            "allowedValues": [
                "windows",
                "linux"
            ],
            "type": "String"
        },
        "subscriptionId": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "vmSize": {
            "type": "String"
        },
        "publicIPAddressName": {
            "type": "String"
        },
        "vmName": {
            "type": "String"
        },
        "virtualNetworkName": {
            "type": "String"
        },
        "nicName": {
            "type": "String"
        },
        "vhdUrl": {
            "type": "String",
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
        "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
        "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
    },
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2015-06-15",
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
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2015-06-15",
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
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2015-06-15",
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
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2015-06-15",
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
                    "adminPassword": "[parameters('adminPassword')]"
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
                }
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "apiVersion": "2015-06-15",
            "name": "[concat(parameters('vmName'),'/WinRMCustomScriptExtension')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "typeHandlerVersion": "1.4",
                "settings": {
                    "fileUris": [
                        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1",
                        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe",
                        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd"
                    ],
                    "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -file ConfigureWinRM.ps1 ',variables('hostDNSNameScriptArgument'))]"
                }
            }
        }
    ]
}
```

<br>

6. Укажите значения параметров на страницах свойств Настраиваемое развертывание.

| ResourceGroupName | Имя существующей группы ресурсов Azure. Обычно используется та же группа репликации, что и для хранилища ключей. |
| --- | --- |
| TemplateFile | Полный путь к файлу VHDtoImage.json. |
| userStorageAccountName | Имя учетной записи хранения. |
| dnsNameForPublicIP | DNS-имя общедоступного IP-адреса в нижнем регистре. |
| subscriptionId | Идентификатор подписки Azure. |
| Расположение | Стандартное географическое расположение Azure для группы ресурсов. |
| vmName | Имя виртуальной машины. |
| vhdUrl | Веб-адрес виртуального жесткого диска. |
| vmSize | Размер экземпляра виртуальной машины. |
| publicIPAddressName | Имя общедоступного IP-адреса. |
| virtualNetworkName | Имя виртуальной сети. |
| nicName | Имя сетевого адаптера для виртуальной сети. |
| adminUserName | Имя пользователя учетной записи администратора. |
| adminPassword | Пароль администратора. |
|

7. После ввода этих значений выберите **Покупка**.

Azure начнет развертывание. Будет создана виртуальная машина с указанным неуправляемым VHD в определенном пути к учетной записи хранения. На портале Azure можно отслеживать ход выполнения, щелкнув **Виртуальные машины** в левой части. При создании виртуальной машины состояние "Запускается" изменится на "Выполняется".

#### <a name="for-generation-2-vm-deployment-use-this-template"></a>Для развертывания виртуальных машин поколения 2 Используйте следующий шаблон:

```JSON
{
   "$schema":"https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "parameters":{
      "userStorageAccountName":{
         "type":"String"
      },
      "userStorageContainerName":{
         "type":"String",
         "defaultValue":"vhds-86350-720-f4efbbb2-611b-4cd7-ad1e-afdgfuadfluad"
      },
      "dnsNameForPublicIP":{
         "type":"String"
      },
      "adminUserName":{
         "defaultValue":"isv",
         "type":"String"
      },
      "adminPassword":{
         "type":"securestring",
         "defaultValue":"Certcare@86350"
      },
      "osType":{
         "type":"string",
         "defaultValue":"linux",
         "allowedValues":[
            "windows",
            "linux"
         ]
      },
      "subscriptionId":{
         "type":"string"
      },
      "location":{
         "type":"string"
      },
      "vmSize":{
         "type":"string"
      },
      "publicIPAddressName":{
         "type":"string"
      },
      "vmName":{
         "type":"string"
      },
      "vNetNewOrExisting":{
         "defaultValue":"existing",
         "allowedValues":[
            "new",
            "existing"
         ],
         "type":"String",
         "metadata":{
            "description":"Specify whether to create a new or existing virtual network for the VM."
         }
      },
      "virtualNetworkName":{
         "type":"String",
         "defaultValue":""
      },
      "SubnetName":{
         "defaultValue":"subnet-5",
         "type":"String"
      },
      "SubnetPrefix":{
         "defaultValue":"10.0.5.0/24",
         "type":"String"
      },
      "nicName":{
         "type":"string"
      },
      "vhdUrl":{
         "type":"string",
         "metadata":{
            "description":"VHD Url..."
         }
      },
      "Datadisk1":{
         "type":"string",
         "metadata":{
            "description":"datadisk1 Url..."
         }
      },
      "Datadisk2":{
         "type":"string",
         "metadata":{
            "description":"datadisk2 Url..."
         }
      },
      "Datadisk3":{
         "type":"string",
         "metadata":{
            "description":"datadisk2 Url..."
         }
      }
   },
   "variables":{
      "addressPrefix":"10.0.0.0/16",
      "subnet1Name":"[parameters('SubnetName')]",
      "subnet1Prefix":"[parameters('SubnetPrefix')]",
      "publicIPAddressType":"Static",
      "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
      "subnet1Ref":"[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
      "osDiskVhdName":"[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]",
      "dataDiskVhdName":"[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'datadisk')]",
      "imageName":"[concat(parameters('vmName'), '-image')]"
   },
   "resources":[
      {
         "apiVersion":"2015-05-01-preview",
         "type":"Microsoft.Network/publicIPAddresses",
         "name":"[parameters('publicIPAddressName')]",
         "location":"[parameters('location')]",
         "properties":{
            "publicIPAllocationMethod":"[variables('publicIPAddressType')]",
            "dnsSettings":{
               "domainNameLabel":"[parameters('dnsNameForPublicIP')]"
            }
         }
      },
      {
         "type":"Microsoft.Compute/images",
         "apiVersion":"2019-12-01",
         "name":"[variables('imageName')]",
         "location":"[parameters('location')]",
         "properties":{
            "storageProfile":{
               "osDisk":{
                  "osType":"[parameters('osType')]",
                  "osState":"Generalized",
                  "blobUri":"[parameters('vhdUrl')]",
                  "storageAccountType":"Standard_LRS"
               },
               "dataDisks":[
                  {
                     "lun":0,
                     "blobUri":"[parameters('Datadisk1')]",
                     "storageAccountType":"Standard_LRS"
                  },
                  {
                     "lun":1,
                     "blobUri":"[parameters('Datadisk2')]",
                     "storageAccountType":"Standard_LRS"
                  },
                  {
                     "lun":2,
                     "blobUri":"[parameters('Datadisk3')]",
                     "storageAccountType":"Standard_LRS"
                  }
               ]
            },
            "hyperVGeneration":"V2"
         }
      },
      {
         "apiVersion":"2015-05-01-preview",
         "type":"Microsoft.Network/virtualNetworks",
         "name":"[parameters('virtualNetworkName')]",
         "location":"[parameters('location')]",
         "properties":{
            "addressSpace":{
               "addressPrefixes":[
                  "[variables('addressPrefix')]"
               ]
            },
            "subnets":[
               {
                  "name":"[variables('subnet1Name')]",
                  "properties":{
                     "addressPrefix":"[variables('subnet1Prefix')]"
                  }
               }
            ]
         },
         "condition":"[equals(parameters('vNetNewOrExisting'), 'new')]"
      },
      {
         "apiVersion":"2016-09-01",
         "type":"Microsoft.Network/networkInterfaces",
         "name":"[parameters('nicName')]",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
            "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
         ],
         "properties":{
            "ipConfigurations":[
               {
                  "name":"ipconfig1",
                  "properties":{
                     "privateIPAllocationMethod":"Dynamic",
                     "publicIPAddress":{
                        "id":"[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                     },
                     "subnet":{
                        "id":"[variables('subnet1Ref')]"
                     }
                  }
               }
            ]
         }
      },
      {
         "apiVersion":"2019-12-01",
         "type":"Microsoft.Compute/virtualMachines",
         "name":"[parameters('vmName')]",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]",
            "[variables('imageName')]"
         ],
         "properties":{
            "hardwareProfile":{
               "vmSize":"[parameters('vmSize')]"
            },
            "osProfile":{
               "computername":"[parameters('vmName')]",
               "adminUsername":"[parameters('adminUsername')]",
               "adminPassword":"[parameters('adminPassword')]"
            },
            "storageProfile":{
               "imageReference":{
                  "id":"[resourceId('Microsoft.Compute/images', variables('imageName'))]"
               },
               "dataDisks":[
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk0')]",
                     "lun":0,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  },
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk1')]",
                     "lun":1,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  },
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk2')]",
                     "lun":2,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  }
               ],
               "osDisk":{
                  "name":"[concat(parameters('vmName'),'-OSDisk')]",
                  "createOption":"FromImage",
                  "managedDisk":{
                     "storageAccountType":"Standard_LRS"
                  }
               }
            },
            "networkProfile":{
               "networkInterfaces":[
                  {
                     "id":"[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                  }
               ]
            }
         }
      }
   ]
}
```

### <a name="deploy-an-azure-vm-using-powershell"></a>Развертывание виртуальной машины Azure с помощью PowerShell

Скопируйте и измените приведенный ниже скрипт, указав нужные значения переменных `$storageaccount` и `$vhdUrl`. Выполните его, чтобы создать ресурс виртуальной машины Azure из существующего универсального диска VHD.

```PowerShell
# storage account of existing generalized VHD

$storageaccount = "testwinrm11815" # generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl
$objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD

New-AzResourceGroupDeployment -Name"dplisvvm$postfix" -ResourceGroupName"$rgName" -TemplateFile"C:\certLocation\VHDtoImage.json" - userStorageAccountName"$storageaccount" -dnsNameForPublicIP"$vmName" -subscriptionId"$mysubid" -location"$location" - vmName"$vmName" -vaultName"$kvname" -vaultResourceGroup"$rgName" -certificateUrl$objAzureKeyVaultSecret.Id -vhdUrl"$vhdUrl" - vmSize"Standard\_A2" -publicIPAddressName"myPublicIP1" -virtualNetworkName"myVNET1" -nicName"myNIC1" -adminUserName"isv" - adminPassword$pwd
```

## <a name="run-validations"></a>Выполнение проверок

Выполнять проверки на развернутом образе можно двумя способами.

### <a name="use-certification-test-tool-for-azure-certified"></a>с помощью средств проверки сертификации для Azure Certitfied;

#### <a name="download-and-run-the-certification-test-tool"></a>Загрузка и запуск средства проверки сертификации

Средство проверки сертификации для Azure Certified выполняется на локальном компьютере Windows, но тестирует виртуальную машину Azure на базе Windows или Linux. Оно проверяет, можно ли использовать пользовательский образ виртуальной машины в Microsoft Azure и выполнены ли рекомендации и требования по подготовке виртуального жесткого диска.

1. Скачайте и установите [средство проверки сертификации Azure](https://www.microsoft.com/download/details.aspx?id=44299) последней версии.
2. Откройте средство сертификации и щелкните **Запустить новую проверку**.
3. На экране Test Information (Сведения о проверке) введите **имя теста** для тестового запуска.
4. Выберите платформу для виртуальной машины: **Windows Server** или **Linux**. Выбор платформы влияет на остальные параметры.
5. Если ваша виртуальная машина использует эту службу базы данных, установите флажок **Test for Azure SQL Database** (Тестирование для Базы данных SQL Azure).

#### <a name="connect-the-certification-tool-to-a-vm-image"></a>Подключение средства сертификации к образу виртуальной машины

1. Выберите режим SSH Authentication (Аутентификация по SSH): Аутентификация по паролю или по файлу ключа.
2. Если используется проверка подлинности на основе пароля, введите значения для **DNS-имени виртуальной машины**, **имени пользователя**и **пароля**. Вы также можете изменить указанный по умолчанию SSH Port (Порт SSH).

    :::image type="content" source="media/vm/azure-vm-cert-2.png" alt-text="Отображает выбор сведений о тесте виртуальной машины.":::

3. При использовании проверки подлинности на основе файла ключа введите значения DNS-имени, имени пользователя и расположение закрытого ключа. Также можно предоставить значение Passphrase (Парольная фраза) или изменить указанный по умолчанию SSH Port (Порт SSH).
4. Введите полное доменное имя виртуальной машины (например, MyVMName.ClOudapp.net).
5. Введите **имя пользователя** и **пароль**.

    :::image type="content" source="media/vm/azure-vm-cert-4.png" alt-text="Отображает выбор имени пользователя и пароля виртуальной машины.":::

6. Нажмите **Далее**.

#### <a name="run-a-certification-test"></a>Выполнение теста сертификации

После того как вы задаете значения параметров для образа виртуальной машины в средстве сертификации, выберите Проверить подключение, чтобы создать допустимое подключение к виртуальной машине. Когда подключение будет проверено, нажмите кнопку **Далее**, чтобы начать тестирование. По завершении теста результаты отображаются в таблице. В столбце "Состояние" для каждого теста отображается одно из значений: Pass (Успех), Fail (Неудача) или Warning (Предупреждение). Если хотя бы один из тестов не будет пройден, образ не будет сертифицирован. В этом случае ознакомьтесь с требованиями и сообщениями об ошибках, внесите предложенные изменения и повторно выполните тест.

После завершения автоматической проверки укажите дополнительную информацию об этом образе виртуальной машины, перейдя в окне Questionnaire (Анкета) на вкладки General Assessment (Общая оценка) и Kernel Customization (Настройка ядра), затем щелкните Далее.

Последний экран позволяет предоставить дополнительные сведения, такие как сведения о доступе к SSH для образа виртуальной машины Linux, а также пояснения к неудачным оценкам, если вы ищете исключения.

Наконец, щелкните Generate Report (Сформировать отчет), чтобы скачать результаты проверки и файлы журналов по всем выполненным тестам в комплекте с ответами на вопросы анкеты. Сохраните результаты в том же контейнере, что и диски VHD.

## <a name="how-to-use-powershell-to-consume-the-self-test-api"></a>Использование PowerShell для использования API самотестирования

### <a name="on-linux-os"></a>В ОС Linux

Вызовите API в PowerShell:

1. Для вызова API используйте команду Invoke-WebRequest.
2. Укажите метод Post и тип содержимого в формате JSON, как показано ниже в примере кода и на снимке экрана.
3. Укажите параметры Body в формате JSON.

В следующем примере показан вызов PowerShell для API:

```POWERSHELL
$accesstoken = “token”
$headers = New-Object “System.Collections.Generic.Dictionary[[String],[String]]”
$headers.Add(“Authorization”, “Bearer $accesstoken”)
$DNSName = “\&lt;\&lt;Machine DNS Name\&gt;\&gt;”
$UserName = “\&lt;\&lt;User ID\&gt;\&gt;”
$Password = “\&lt;\&lt;Password\&gt;\&gt;”
$OS = “Linux”
$PortNo = “22”
$CompanyName = “ABCD”
$AppID = “\&lt;\&lt;Application ID\&gt;\&gt;”
$TenantId = “\&lt;\&lt;Tenant ID\&gt;\&gt;”

$body =
@{
DNSName = $DNSName
UserName = $UserName
Password = $Password
OS = $OS
PortNo = $PortNo
CompanyName = $CompanyName
AppID = $AppID
TenantId = $TenantId
}| ConvertTo-Json

$body

$uri = “URL”

$res = (Invoke-WebRequest -Method “Post” -Uri $uri -Body $body -ContentType “application/json” -Headers $headers).Content
```

<br>Ниже приведен пример вызова API в PowerShell.

[![Пример с экраном для вызова API в PowerShell.](media/vm/call-api-in-powershell.png)](media/vm/call-api-in-powershell.png#lightbox)

<br>Выполнив предыдущий пример, вы получите ответ в формате JSON, анализ которого даст вам следующие сведения.

```PowerShell
$resVar=$res|ConvertFrom-Json

$actualresult =$resVar.Response |ConvertFrom-Json

Write-Host”OSName: $($actualresult.OSName)”Write-Host”OSVersion: $($actualresult.OSVersion)”Write-Host”Overall Test Result: $($actualresult.TestResult)”For ($i=0; $i -lt$actualresult.Tests.Length; $i++){ Write-Host”TestID: $($actualresult.Tests[$i].TestID)”Write-Host”TestCaseName: $($actualresult.Tests[$i].TestCaseName)”Write-Host”Description: $($actualresult.Tests[$i].Description)”Write-Host”Result: $($actualresult.Tests[$i].Result)”Write-Host”ActualValue: $($actualresult.Tests[$i].ActualValue)”}
```

<br>На этом образце экрана отображаются `$res.Content` подробные сведения о результатах теста в формате JSON:

[![Пример экрана для вызова API в PowerShell с подробными сведениями о результатах теста.](media/vm/call-api-in-powershell-details.png)](media/vm/call-api-in-powershell-details.png#lightbox)

<br>Ниже приведен пример результатов теста JSON, просматриваемых в интерактивном средстве просмотра JSON (например, [Code беаутифи](https://codebeautify.org/jsonviewer) или [JSON Viewer](https://jsonformatter.org/json-viewer)).

![Дополнительные результаты тестирования в интерактивном средстве просмотра JSON.](media/vm/test-results-json-viewer-1.png)

### <a name="on-windows-os"></a>В ОС Windows

Вызовите API в PowerShell:

1. Для вызова API используйте команду Invoke-WebRequest.
2. Метод является POST, а тип содержимого — JSON, как показано в следующем примере кода и на образце экрана.
3. Создайте параметры текста в формате JSON.

В этом примере кода показан вызов PowerShell для API:

```PowerShell
$accesstoken = “Get token for your Client AAD App”$headers = New-Object”System.Collections.Generic.Dictionary[[String],[String]]”$headers.Add(“Authorization”, “Bearer $accesstoken”)$Body = @{ “DNSName” = “XXXX.westus.cloudapp.azure.com”“UserName” = “XXX”“Password” = “XXX@123456”“OS” = “Windows”“PortNo” = “5986”“CompanyName” = “ABCD” “AppID” = “XXXX-XXXX-XXXX” “TenantId” = “XXXX-XXXX-XXXX” } | ConvertTo-Json$res = Invoke-WebRequest -Method”Post” -Uri$uri -Body$Body -ContentType”application/json” –Headers $headers;$Content = $res | ConvertFrom-Json
```

На этих примерах экранов показан пример вызова API в PowerShell:

**С помощью ключа SSH**:

 :::image type="content" source="media/vm/call-api-with-ssh-key.png" alt-text="Вызов API в PowerShell с помощью ключа SSH.":::

**С паролем**:

 :::image type="content" source="media/vm/call-api-with-password.png" alt-text="Вызов API в PowerShell с паролем.":::

<br>Выполнив предыдущий пример, вы получите ответ в формате JSON, анализ которого даст вам следующие сведения.

```PowerShell
$resVar=$res|ConvertFrom-Json

$actualresult =$resVar.Response |ConvertFrom-Json

Write-Host”OSName: $($actualresult.OSName)”Write-Host”OSVersion: $($actualresult.OSVersion)”Write-Host”Overall Test Result: $($actualresult.TestResult)”For ($i=0; $i -lt$actualresult.Tests.Length; $i++){ Write-Host”TestID: $($actualresult.Tests[$i].TestID)”Write-Host”TestCaseName: $($actualresult.Tests[$i].TestCaseName)”Write-Host”Description: $($actualresult.Tests[$i].Description)”Write-Host”Result: $($actualresult.Tests[$i].Result)”Write-Host”ActualValue: $($actualresult.Tests[$i].ActualValue)”}
```

<br>На этом экране отображаются `$res.Content` подробные сведения о результатах теста в формате JSON:

 :::image type="content" source="media/vm/test-results-json-format.png" alt-text="Подробные сведения о результатах теста в формате JSON.":::

<br>Ниже приведен пример результатов теста, просматриваемых в интерактивном средстве просмотра JSON (например, [Code беаутифи](https://codebeautify.org/jsonviewer) или [JSON Viewer](https://jsonformatter.org/json-viewer)).

![Результаты теста в интерактивном средстве просмотра JSON.](media/vm/test-results-json-viewer-2.png)

## <a name="how-to-use-curl-to-consume-the-self-test-api-on-linux-os"></a>Использование функции "перелистывание" для использования API самотестирования в ОС Linux

Вызовите API в фигурных скобках:

1. Используйте команду curl для вызова API.
2. Укажите метод Post и тип содержимого JSON, как показано в фрагменте кода ниже.

```JSON
CURL POST -H “Content-Type:application/json”

-H “Authorization: Bearer XXXXXX-Token-XXXXXXXX”

[https://isvapp.azure-api.net/selftest-vm](https://isvapp.azure-api.net/selftest-vm)

-d ‘{ “DNSName”:”XXXX.westus.cloudapp.azure.com”, “UserName”:”XXX”, “Password”:”XXXX@123456”, “OS”:”Linux”, “PortNo”:”22”, “CompanyName”:”ABCD”, “AppId”:”XXXX-XXXX-XXXX”, “TenantId “XXXX-XXXX-XXXX”}’
```

<br>Ниже приведен пример использования функции «ИЗОГНУТие» для вызова API:

![Пример использования функции перелистывания для вызова API.](media/vm/use-curl-call-api.png)

<br>Ниже приведены результаты JSON из вызова с ФИГУРным вызовом:

![Результаты JSON из вызова с ФИГУРным вызовом.](media/vm/test-results-json-viewer-3.png)

## <a name="next-step"></a>Следующий шаг

- Считывает [распространенные проблемы и исправления URI SAS](common-sas-uri-issues.md).
