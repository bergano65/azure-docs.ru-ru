---
title: Шифрование дисков Azure для виртуальных машин и масштабируемых наборов виртуальных машин
description: В этой статье приводятся общие сведения о шифровании дисков Azure.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: c881b2b9743766e4d35e6cb05f6f3469803850bc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80062126"
---
# <a name="azure-disk-encryption-for-virtual-machines-and-virtual-machine-scale-sets"></a>Шифрование дисков Azure для виртуальных машин и масштабируемых наборов виртуальных машин

Шифрование дисков Azure можно применять к виртуальным машинам Linux и Windows, а также к масштабируемым наборам виртуальных машин. 

## <a name="linux-virtual-machines"></a>Виртуальные машины Linux

В следующих статьях содержатся рекомендации по шифрованию виртуальных машин Linux.

### <a name="current-version-of-azure-disk-encryption"></a>Текущая версия шифрования дисков Azure

- [Общие сведения о шифровании дисков Azure для виртуальных машин Linux](../../virtual-machines/linux/disk-encryption-overview.md)
- [Azure Disk Encryption scenarios on Linux VMs](../../virtual-machines/linux/disk-encryption-linux.md) (Сценарии шифрования дисков Azure для виртуальных машин Linux)
- [Создание и шифрование виртуальной машины Linux с помощью Azure CLI](../../virtual-machines/linux/disk-encryption-cli-quickstart.md)
- [Создание и шифрование виртуальной машины Linux с помощью Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)
- [Создание и шифрование виртуальной машины Linux на портале Azure](../../virtual-machines/linux/disk-encryption-portal-quickstart.md)
- [Схема расширения шифрования дисков Azure для Linux](../../virtual-machines/extensions/azure-disk-enc-linux.md)
- [Создание и настройка хранилища ключей для шифрования дисков Azure](../../virtual-machines/linux/disk-encryption-key-vault.md)
- [Примеры скриптов шифрования дисков Azure](../../virtual-machines/linux/disk-encryption-sample-scripts.md)
- [Устранение неполадок с шифрованием дисков Azure](../../virtual-machines/linux/disk-encryption-troubleshooting.md)
- [Часто задаваемые вопросы о шифровании дисков Azure](../../virtual-machines/linux/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Шифрование дисков Azure с помощью Azure AD (Предыдущая версия)

- [Общие сведения о шифровании дисков Azure с помощью Azure AD для виртуальных машин Linux](../../virtual-machines/linux/disk-encryption-overview-aad.md)
- [Шифрование дисков Azure с помощью сценариев Azure AD на виртуальных машинах Linux](../../virtual-machines/linux/disk-encryption-linux.md)
- [Создание и Настройка хранилища ключей для шифрования дисков Azure с помощью Azure AD (предыдущий выпуск)](../../virtual-machines/linux/disk-encryption-key-vault-aad.md)

## <a name="windows-virtual-machines"></a>Виртуальные машины Windows

В следующих статьях содержатся рекомендации по шифрованию виртуальных машин Windows.

### <a name="current-version-of-azure-disk-encryption"></a>Текущая версия шифрования дисков Azure

- [Общие сведения о шифровании дисков Azure для виртуальных машин Windows](../../virtual-machines/windows/disk-encryption-overview.md)
- [Сценарии шифрования дисков Azure для виртуальных машин Windows](../../virtual-machines/windows/disk-encryption-windows.md)
- [Создание и шифрование виртуальной машины Windows с помощью Azure CLI](../../virtual-machines/windows/disk-encryption-cli-quickstart.md)
- [Создание и шифрование виртуальной машины Windows с помощью Azure PowerShell](../../virtual-machines/windows/disk-encryption-powershell-quickstart.md)
- [Создание и шифрование виртуальной машины Windows с помощью портала Azure](../../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- [Схема расширения шифрования дисков Azure для Windows](../../virtual-machines/extensions/azure-disk-enc-windows.md)
- [Создание и настройка хранилища ключей для шифрования дисков Azure](../../virtual-machines/windows/disk-encryption-key-vault.md)
- [Примеры скриптов шифрования дисков Azure](../../virtual-machines/windows/disk-encryption-sample-scripts.md)
- [Устранение неполадок с шифрованием дисков Azure](../../virtual-machines/windows/disk-encryption-troubleshooting.md)
- [Часто задаваемые вопросы о шифровании дисков Azure](../../virtual-machines/windows/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Шифрование дисков Azure с помощью Azure AD (Предыдущая версия)

- [Общие сведения о шифровании дисков Azure с помощью Azure AD для виртуальных машин Windows](../../virtual-machines/windows/disk-encryption-overview-aad.md)
- [Шифрование дисков Azure с помощью сценариев Azure AD на виртуальных машинах Windows](../../virtual-machines/windows/disk-encryption-windows.md)
- [Создание и Настройка хранилища ключей для шифрования дисков Azure с помощью Azure AD (предыдущий выпуск)](../../virtual-machines/windows/disk-encryption-key-vault-aad.md)

## <a name="virtual-machine-scale-sets"></a>Масштабируемые наборы виртуальных машин

В следующих статьях содержатся рекомендации по шифрованию масштабируемых наборов виртуальных машин.

- [Общие сведения о шифровании дисков Azure для масштабируемых наборов виртуальных машин](../../virtual-machine-scale-sets/disk-encryption-overview.md) 
- [Encrypt OS and attached data disks in a virtual machine scale set with the Azure CLI](../../virtual-machine-scale-sets/disk-encryption-cli.md) (Шифрование ОС и подключенных дисков данных в масштабируемом наборе виртуальных машин с помощью Azure CLI) 
- [Шифрование масштабируемых наборов виртуальных машин с помощью Azure PowerShell](../../virtual-machine-scale-sets/disk-encryption-powershell.md).
- [Шифрование масштабируемых наборов виртуальных машин с помощью Azure Resource Manager](../../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
- [Creating and configuring a key vault for Azure Disk Encryption](../../virtual-machine-scale-sets/disk-encryption-key-vault.md) (Создание и настройка хранилища ключей для шифрования дисков Azure)
- [Use Azure Disk Encryption with virtual machine scale set extension sequencing](../../virtual-machine-scale-sets/disk-encryption-extension-sequencing.md) (Использование шифрования дисков Azure с помощью виртуализации расширения масштабируемого набора виртуальных машин)

## <a name="next-steps"></a>Дальнейшие шаги

- [Общие сведения о шифровании Azure](encryption-overview.md)
- [Шифрование неактивных данных](encryption-atrest.md)
- [Рекомендации по шифрованию и обеспечению защиты данных](data-encryption-best-practices.md)
