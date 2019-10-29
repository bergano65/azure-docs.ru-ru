---
title: Создание и шифрование масштабируемого набора виртуальных машин с помощью шаблонов Azure Resource Manager
description: С помощью этого краткого руководства вы узнаете, как создать и зашифровать масштабируемый набор виртуальных машин, используя шаблоны Azure Resource Manager
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: quickstart
ms.date: 10/10/2019
ms.openlocfilehash: aeba9aee8dd10b1245fb0a1e1cc98a1951382c72
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530942"
---
# <a name="encrypt-virtual-machine-scale-sets-with-azure-resource-manager"></a>Шифрование масштабируемых наборов виртуальных машин с помощью Azure Resource Manager

Масштабируемые наборы виртуальных машин Linux можно шифровать и расшифровывать с помощью шаблонов Azure Resource Manager.

## <a name="deploying-templates"></a>Развертывание шаблонов

Сначала выберите шаблон, соответствующий вашему сценарию.

- [Включение шифрования диска в запущенном масштабируемом наборе виртуальных машин Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [Включение шифрования диска в запущенном масштабируемом наборе виртуальных машин Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

  - [Развертывание масштабируемого набора виртуальных машин Linux с помощью jumpbox и включение шифрования в этих масштабируемых наборах Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

  - [Развертывание масштабируемого набора виртуальных машин Windows с помощью jumpbox и включение шифрования в этих масштабируемых наборах Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [Отключение шифрования диска в запущенном масштабируемом наборе виртуальных машин Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [Отключение шифрования диска в запущенном масштабируемом наборе виртуальных машин Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

Затем выполните следующие действия.

     1. Click **Deploy to Azure**.
     2. Fill in the required fields then agree to the terms and conditions.
     3. Click **Purchase** to deploy the template.

## <a name="next-steps"></a>Дополнительная информация

- [Azure Disk Encryption for Virtual Machine Scale Sets](disk-encryption-overview.md) (Шифрование дисков Azure для масштабируемых наборов виртуальных машин)
- [Encrypt OS and attached data disks in a virtual machine scale set with the Azure CLI](disk-encryption-cli.md) (Шифрование ОС и подключенных дисков данных в масштабируемом наборе виртуальных машин с помощью Azure CLI)
- [Encrypt OS and attached data disks in a virtual machine scale set with Azure PowerShell](disk-encryption-powershell.md) (Шифрование ОС и подключенных дисков данных в масштабируемом наборе виртуальных машин с помощью Azure PowerShell)
- [Creating and configuring a key vault for Azure Disk Encryption](disk-encryption-key-vault.md) (Создание и настройка хранилища ключей для шифрования дисков Azure)
- [Use Azure Disk Encryption with virtual machine scale set extension sequencing](disk-encryption-extension-sequencing.md) (Использование шифрования дисков Azure с помощью виртуализации расширения масштабируемого набора виртуальных машин)