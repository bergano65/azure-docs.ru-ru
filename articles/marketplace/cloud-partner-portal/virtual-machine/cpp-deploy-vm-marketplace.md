---
title: Развертывание виртуальной машины из Azure Marketplace | Документация Майкрософт
description: В этой статье описывается развертывание виртуальной машины из предварительно настроенной виртуальной машины в Azure Marketplace.
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
ms.openlocfilehash: 3ec4e0b047e94dc7481c51390c6e4370fdea5efa
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258808"
---
# <a name="deploy-a-virtual-machine-from-the-azure-marketplace"></a>Развертывание виртуальной машины из Azure Marketplace

В этой статье объясняется, как развернуть предварительно настроенную виртуальную машину из Azure Marketplace, используя скрипт Azure PowerShell.  Этот скрипт также предоставляет на виртуальной машине конечные точки WinRM HTTP и HTTPS.  Для работы скрипта необходимо заранее передать сертификат в Azure Key Vault, как описано в статье [Создание сертификатов для Azure Key Vault](./cpp-create-key-vault-cert.md). 


## <a name="vm-deployment-template"></a>Шаблон развертывания виртуальной машины

Шаблон для быстрого развертывания виртуальной машины Azure доступен в виде файла [azuredeploy.json](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json).  Он содержит следующие параметры:

|  **Параметр**        |   **Описание**                                 |
|  -------------        |   ---------------                                 |
| newStorageAccountName | Имя учетной записи хранения                       |
| dnsNameForPublicIP    | DNS-имя общедоступного IP-адреса. Используются только строчные символы.    |
| adminUserName         | Имя пользователя администратора.                          |
| adminPassword         | Пароль администратора.                          |
| imagePublisher        | Издатель образа.                                   |
| imageOffer            | Предложение образа.                                       |
| imageSKU              | Номер SKU образа.                                         |
| vmSize                | Размер виртуальной машины.                                    |
| vmName                | Имя виртуальной машины.                                    |
| vaultName             | Имя хранилища ключей.                             |
| vaultResourceGroup    | Группа ресурсов хранилища ключей.                   |
| certificateUrl        | URL-адрес сертификата, включая версию в хранилище ключей, например `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7`. |
|  |  |


## <a name="deployment-script"></a>Скрипт развертывания

Измените приведенный ниже скрипт Azure PowerShell и выполните его, чтобы развернуть указанную виртуальную машину из Azure Marketplace.

```powershell

New-AzureRmResourceGroupDeployment -Name "dplvm$postfix" -ResourceGroupName "$rgName" -TemplateUri "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json" -newStorageAccountName "test$postfix" -dnsNameForPublicIP $vmName -adminUserName "isv" -adminPassword $pwd -vmSize "Standard_A2" -vmName $vmName -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id 

```


## <a name="next-steps"></a>Дополнительная информация

Развернув предварительно настроенную виртуальную машину, вы можете настроить включенные в нее решения и службы или использовать ее для дальнейшей разработки. 
