---
title: Развертывание виртуальной машины Azure из пользовательского виртуального жесткого диска | Документация Майкрософт
description: В этой статье описывается развертывание пользовательского образа виртуального жесткого диска для создания экземпляра виртуальной машины Azure.
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
ms.openlocfilehash: 9c163ddf7859246fcdaa28edfd4b598a24a32be2
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53196256"
---
# <a name="deploy-an-azure-vm-from-a-user-vhd"></a>Развертывание виртуальной машины Azure из виртуального жесткого диска пользователя

В этой статье описывается развертывание универсального образа виртуального жесткого диска для создания нового ресурса виртуальной машины Azure, используя указанный шаблон Azure Resource Manager и сценарий Azure PowerShell.


## <a name="vhd-deployment-template"></a>Шаблон развертывания VHD

Скопируйте шаблон Azure Resource Manager для [развертывания виртуального жесткого диска](cpp-deploy-json-template.md) в локальный файл с именем `VHDtoImage.json`.  Измените этот файл, чтобы задать значения для следующих параметров. 

|  **Параметр**             |   **Описание**                                                              |
|  -------------             |   ---------------                                                              |
| ResourceGroupName          | Имя существующей группы ресурсов Azure.  Обычно используется та же группа репликации, которая связана с хранилищем ключей  |
| TemplateFile               | Полный путь к файлу `VHDtoImage.json`                                    |
| userStorageAccountName     | Имя учетной записи хранения                                                    |
| sNameForPublicIP           | DNS-имя для общедоступного IP-адреса. Используются только строчные символы                                  |
| subscriptionId             | Идентификатор подписки Azure                                                  |
| Расположение                   | Стандартное географическое расположение Azure для группы ресурсов                       |
| vmName                     | Имя виртуальной машины                                                    |
| vaultName                  | Имя хранилища ключей.                                                          |
| vaultResourceGroup         | Группа ресурсов хранилища ключей.
| certificateUrl             | URL-адрес сертификата, включая версию, хранящуюся в хранилище ключей, например: https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7 |
| vhdUrl                     | URL-адрес виртуального жесткого диска                                                   |
| vmSize                     | Размер экземпляра виртуальной машины                                           |
| publicIPAddressName        | Имя общедоступного IP-адреса                                                  |
| virtualNetworkName         | Имя виртуальной сети                                                    |
| nicName                    | Имя сетевого адаптера для виртуальной сети                     |
| adminUserName              | Имя пользователя учетной записи администратора                                          |
| adminPassword              | Пароль администратора                                                          |
|  |  |


## <a name="powershell-script"></a>Сценарий PowerShell

Скопируйте и измените приведенный ниже сценарий, чтобы задать значения переменных `$storageaccount` и `$vhdUrl`.  Выполните его, чтобы создать ресурс виртуальной машины Azure из существующего универсального диска VHD.

```powershell
# storage account of existing generalized VHD 
$storageaccount = "testwinrm11815"

# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzureRMResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

#deploying VM with existing VHD
New-AzureRMResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd 

```

## <a name="next-steps"></a>Дополнительная информация

После развертывания виртуальной машины вы сможете [сертифицировать образ своей виртуальной машины](./cpp-certify-vm.md).
