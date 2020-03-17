---
title: Краткое руководство. Автоматизированное развертывание виртуальной машины с помощью службы "Конфигурация приложений Azure"
description: В этом кратком руководстве показано, как использовать модуль Azure PowerShell и шаблоны Azure Resource Manager для развертывания хранилища службы "Конфигурация приложений Azure". Затем используйте значения в хранилище для развертывания виртуальной машины.
author: lisaguthrie
ms.author: lcozzens
ms.date: 03/05/2020
ms.topic: quickstart
ms.service: azure-app-configuration
ms.custom:
- mvc
- subject-armqs
ms.openlocfilehash: c45f6855c33dff2790ced306fd7f049b98dd1387
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126382"
---
# <a name="quickstart-automated-vm-deployment-with-app-configuration-and-resource-manager-template"></a>Краткое руководство. Автоматизированное развертывание виртуальной машины с помощью службы "Конфигурация приложений" и шаблона Resource Manager

Модуль PowerShell используется для создания ресурсов Azure и управления ими с помощью командлетов или скриптов PowerShell. В этом кратком руководстве показано, как использовать Azure PowerShell и шаблоны Azure Resource Manager для развертывания хранилища службы "Конфигурация приложений Azure". Затем вы также узнаете, как использовать пары "ключ-значение" в хранилище для развертывания виртуальной машины.

Вы будете использовать необходимый шаблон, чтобы создать хранилище Конфигурации приложений, а затем добавите в него пары "ключ-значение" с помощью портала Azure или Azure CLI. Основной шаблон ссылается на имеющиеся конфигурации пар "ключ-значение" из существующего хранилища конфигураций. Извлеченные значения используются для установки свойств ресурсов, созданных шаблоном, например виртуальной машины в этом примере.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="before-you-begin"></a>Перед началом

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/).

* Для работы с этим кратким руководством требуется модуль Azure PowerShell. Запустите `Get-Module -ListAvailable Az`, чтобы найти версию, установленную на вашем локальном компьютере. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите в свою подписку Azure с помощью команды `Connect-AzAccount` и введите учетные данные Azure во всплывающем окне браузера.

```azurepowershell-interactive
# Connect to your Azure account
Connect-AzAccount
```

Если у вас есть несколько подписок, выберите ту, которую вы хотите использовать при работе с этим кратким руководством, запустив приведенные ниже командлеты. Не забудьте заменить `<your subscription name>` именем своей подписки:

```azurepowershell-interactive
# List all available subscriptions.
Get-AzSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzSubscription -SubscriptionName "<your subscription name>" | Select-AzSubscription
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов Azure с помощью командлета [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими.

```azurepowershell-interactive
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzResourceGroup `
    -Name $resourceGroup `
    -Location $location
```

## <a name="deploy-an-azure-app-configuration-store"></a>Развертывание хранилища службы "Конфигурация приложений Azure"

Перед применением к виртуальной машине пар "ключ-значение" у вас должно быть хранилище службы "Конфигурация приложений Azure". В этом разделе подробно описывается развертывание такого хранилища с помощью шаблона Azure Resource Manager. Если у вас уже есть хранилище конфигурации приложений, вы можете перейти к следующему разделу этой статьи. 

1. Скопируйте и вставьте приведенный ниже код JSON в новый файл *prereq.azuredeploy.json*.

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "configStoreName": {
        "type": "string",
        "metadata": {
          "description": "Specifies the name of the app configuration store."
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Specifies the Azure location where the app configuration store should be created."
        }
      },
      "skuName": {
        "type": "string",
        "defaultValue": "standard",
        "metadata": {
          "description": "Specifies the SKU of the app configuration store."
        }
      }
    },
    "resources": [
      {
        "type": "Microsoft.AppConfiguration/configurationStores",
        "name": "[parameters('configStoreName')]",
        "apiVersion": "2019-10-01",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('skuName')]"
        }
      }
    ]
   }
   ```

1. Скопируйте и вставьте приведенный ниже код JSON в новый файл *prereq.azuredeploy.parameters.json*. Замените командлет **GET-UNIQUE** уникальным именем для хранилища конфигураций.

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "configStoreName": {
        "value": "GET-UNIQUE"
      }
    }
   }
   ```

1. В окне PowerShell выполните приведенную ниже команду, чтобы развернуть хранилище службы "Конфигурация приложений Azure". Не забудьте заменить имя группы ресурсов, путь к файлу шаблона и путь к файлу параметров шаблона.

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>" 
       -TemplateFile "<path to prereq.azuredeploy.json>" `
       -TemplateParameterFile "<path to prereq.azuredeploy.parameters.json>"
   ```

## <a name="add-vm-configuration-key-values"></a>Добавление пары "ключ-значение" в конфигурацию виртуальной машины

Вы можете создать хранилище Конфигурации приложений с помощью шаблона Azure Resource Manager, но пары "ключ-значение" необходимо добавить, используя портал Azure или Azure CLI. В этом кратком руководстве вы добавите пары "ключ-значение" с помощью портала Azure.

1. После развертывания необходимо перейти к созданному хранилищу Конфигурации приложений на [портале Azure](https://portal.azure.com).

1. Последовательно выберите элементы **Параметры** > **Ключи доступа**. Запишите первичный ключ строки подключения только для чтения. Она пригодится позже для настройки обмена данными между приложением и созданным хранилищем Конфигурации приложений.

1. Выберите **Обозреватель конфигураций** > **Создать**, чтобы добавить указанные ниже пары "ключ-значение".

   |Клавиши|Значение|
   |-|-|
   |windowsOsVersion|2019-Datacenter|
   |diskSizeGB|1023|
  
   Введите *название шаблона* для параметра **Метка**, но ничего не вводите для параметра **Тип содержимого**.

## <a name="deploy-vm-using-stored-key-values"></a>Развертывание виртуальной машины с использованием сохраненных пар "ключ-значение"

Теперь, когда вы добавили пары "ключ-значение" в хранилище, вы можете развернуть виртуальную машину с помощью шаблона Azure Resource Manager. Шаблон ссылается на созданные ключи **windowsOsVersion** и **diskSizeGB**.

1. Скопируйте и вставьте приведенный ниже код JSON в новый файл *azuredeploy.json* или скачайте файл из [шаблонов быстрого запуска Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.json).

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminUsername": {
            "type": "string",
            "metadata": {
                "description": "Admin user name."
            }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
                "description": "Password for the Virtual Machine."
            }
        },
        "appConfigStoreName": {
            "type": "string",
            "metadata": {
                "description": "App configuration store name."
            }
        },
        "appConfigStoreResourceGroup": {
            "type": "string",
            "metadata": {
                "description": "Name of the resource group for the app config store."
            }
        },
        "domainNameLabel": {
            "type": "string",
            "metadata": {
                "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "vmSize": {
            "type": "string",
            "defaultValue": "Standard_D2_v3",
            "metadata": {
                "description": "Size of the VM"
            }
        },
        "vmSkuKey": {
            "type": "string",
            "metadata": {
                "description": "Name of the key in the app config store for the VM windows sku"
            }
        },
        "diskSizeKey": {
            "type": "string",
            "metadata": {
                "description": "Name of the key in the app config store for the VM disk size"
            }
        },
        "storageAccountName": {
            "type": "string",
            "metadata": {
                "description": "The name of the storage account."
            }
        }
    },
    "variables": {
        "nicName": "myVMNic",
        "addressPrefix": "10.0.0.0/16",
        "subnetName": "Subnet",
        "subnetPrefix": "10.0.0.0/24",
        "publicIPAddressName": "myPublicIP",
        "vmName": "SimpleWinVM",
        "virtualNetworkName": "MyVNET",
        "subnetRef": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('virtualNetworkName'), variables('subnetName'))]",
        "appConfigRef": "[resourceId(parameters('appConfigStoreResourceGroup'), 'Microsoft.AppConfiguration/configurationStores', parameters('appConfigStoreName'))]",
        "windowsOSVersionParameters": {
            "key": "[parameters('vmSkuKey')]",
            "label": "template"
        },
        "diskSizeGBParameters": {
            "key": "[parameters('diskSizeKey')]",
            "label": "template"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2018-11-01",
            "name": "[parameters('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        },
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2018-11-01",
            "name": "[variables('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('domainNameLabel')]"
                }
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2018-11-01",
            "name": "[variables('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[variables('subnetName')]",
                        "properties": {
                            "addressPrefix": "[variables('subnetPrefix')]"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2018-11-01",
            "name": "[variables('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
                "[resourceId('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
                            },
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2018-10-01",
            "name": "[variables('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
                "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computerName": "[variables('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "imageReference": {
                        "publisher": "MicrosoftWindowsServer",
                        "offer": "WindowsServer",
                        "sku": "[listKeyValue(variables('appConfigRef'), '2019-10-01', variables('windowsOSVersionParameters')).value]",
                        "version": "latest"
                    },
                    "osDisk": {
                        "createOption": "FromImage"
                    },
                    "dataDisks": [
                        {
                            "diskSizeGB": "[listKeyValue(variables('appConfigRef'), '2019-10-01', variables('diskSizeGBParameters')).value]",
                            "lun": 0,
                            "createOption": "Empty"
                        }
                    ]
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                        }
                    ]
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))).primaryEndpoints.blob]"
                    }
                }
            }
        }
    ],
    "outputs": {
        "hostname": {
            "type": "string",
            "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]"
        }
    }
   }
   ```

1. Скопируйте и вставьте приведенный ниже код JSON в новый файл *azuredeploy.parameters.json* или скачайте файл из [шаблонов быстрого запуска Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.parameters.json).

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "adminPassword": {
        "value": "GEN-PASSWORD"
      },
      "appConfigStoreName":{
        "value": "GEN-APPCONFIGSTORE-NAME"
      },
      "appConfigStoreResourceGroup": {
         "value": "GEN-APPCONFIGSTORE-RESOURCEGROUP-NAME"
      },
      "vmSkuKey":{
        "value": "GEN-APPCONFIGSTORE-WINDOWSOSVERSION"
      },
      "diskSizeKey" :{
         "value": "GEN-APPCONFIGSTORE-DISKSIZEGB"
      },
      "adminUsername":{
        "value": "GEN-UNIQUE"
      },
      "storageAccountName":{
        "value": "GEN-UNIQUE"
      },
      "domainNameLabel":{
        "value": "GEN-UNIQUE"
      }
    }
   }
   ```

   Замените значения параметров в шаблоне приведенными ниже значениями.

   |Параметр|Значение|
   |-|-|
   |adminPassword|Пароль администратора для виртуальной машины.|
   |appConfigStoreName|Имя хранилища службы "Конфигурация приложений Azure".|
   |appConfigStoreResourceGroup|Группа ресурсов, содержащая хранилище Конфигурации приложений.|
   |vmSkuKey|*windowsOSVersion*|
   |diskSizeKey|*diskSizeGB*|
   |adminUsername|Имя администратора для виртуальной машины.|
   |storageAccountName|Уникальное имя для учетной записи хранения, которая связана с виртуальной машиной.|
   |domainNameLabel|Уникальное доменное имя.|

1. В окне PowerShell выполните приведенную ниже команду, чтобы развернуть хранилище службы "Конфигурация приложений Azure". Не забудьте заменить имя группы ресурсов, путь к файлу шаблона и путь к файлу параметров шаблона.

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>" 
       -TemplateFile "<path to prereq.azuredeploy.json>" `
       -TemplateParameterFile "<path to prereq.azuredeploy.parameters.json>"
   ```

Поздравляем! Вы развернули виртуальную машину с помощью конфигураций, которые хранятся в службе "Конфигурация приложений Azure".

## <a name="clean-up-resources"></a>Очистка ресурсов

Ставшие ненужными группу ресурсов, хранилище Конфигурации приложений, виртуальную машину и все связанные ресурсы можно удалить. Если вы планируете использовать хранилище Конфигурации приложений или виртуальную машину в дальнейшем, не удаляйте их. Если вам больше не нужно это задание, удалите все ресурсы, созданные в ходе работы с этим кратким руководством, выполнив следующий командлет.

```azurepowershell-interactive
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

В рамках этого краткого руководства вы развернули виртуальную машину с помощью шаблона Azure Resource Manager и пар "ключ-значение" из службы "Конфигурация приложений Azure".

Сведения о создании других приложений с помощью службы "Конфигурация приложений Azure" см. в этой статье:

> [!div class="nextstepaction"]
> [Краткое руководство. Создание приложения ASP.NET Core с помощью службы "Конфигурация приложений Azure"](quickstart-aspnet-core-app.md)
