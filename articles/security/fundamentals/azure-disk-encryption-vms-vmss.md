---
title: Лазурное шифрование дисков для виртуальных машин и виртуальных наборов масштабов машин
description: В этой статье представлен обзор шифрования azure Disk
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: c881b2b9743766e4d35e6cb05f6f3469803850bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062126"
---
# <a name="azure-disk-encryption-for-virtual-machines-and-virtual-machine-scale-sets"></a>Лазурное шифрование дисков для виртуальных машин и виртуальных наборов масштабов машин

Шифрование Azure Disk может быть применено как к виртуальным машинам Linux и Windows, так и к виртуальным наборам масштабов машин. 

## <a name="linux-virtual-machines"></a>Виртуальные машины Linux

Следующие статьи содержат рекомендации по шифрованию виртуальных машин Linux.

### <a name="current-version-of-azure-disk-encryption"></a>Текущая версия шифрования azure disk

- [Общие сведения о шифровании дисков Azure для виртуальных машин Linux](../../virtual-machines/linux/disk-encryption-overview.md)
- [Azure Disk Encryption scenarios on Linux VMs](../../virtual-machines/linux/disk-encryption-linux.md) (Сценарии шифрования дисков Azure для виртуальных машин Linux)
- [Создание и шифрование виртуальной машины Linux с помощью Azure CLI](../../virtual-machines/linux/disk-encryption-cli-quickstart.md)
- [Создание и шифрование Linux VM с помощью Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)
- [Создание и шифрование виртуальной машины Linux на портале Azure](../../virtual-machines/linux/disk-encryption-portal-quickstart.md)
- [Схема расширения шифрования дисков Azure для Linux](../../virtual-machines/extensions/azure-disk-enc-linux.md)
- [Создание и настройка хранилища ключей для шифрования дисков Azure](../../virtual-machines/linux/disk-encryption-key-vault.md)
- [Примеры скриптов шифрования дисков Azure](../../virtual-machines/linux/disk-encryption-sample-scripts.md)
- [Устранение неполадок с шифрованием дисков Azure](../../virtual-machines/linux/disk-encryption-troubleshooting.md)
- [Часто задаваемые вопросы о шифровании дисков Azure](../../virtual-machines/linux/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Шифрование диска Azure с Azure AD (предыдущая версия)

- [Обзор шифрования azure Disk с Azure AD для виртуальных машин Linux](../../virtual-machines/linux/disk-encryption-overview-aad.md)
- [Шифрование дисков Azure с сценариями Azure AD на Linux VMs](../../virtual-machines/linux/disk-encryption-linux.md)
- [Создание и настройка хранилища ключей для шифрования дисков Azure с помощью Azure AD (предыдущий релиз)](../../virtual-machines/linux/disk-encryption-key-vault-aad.md)

## <a name="windows-virtual-machines"></a>Виртуальные машины Windows

Следующие статьи содержат рекомендации по шифрованию виртуальных компьютеров Windows.

### <a name="current-version-of-azure-disk-encryption"></a>Текущая версия шифрования azure disk

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

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Шифрование диска Azure с Azure AD (предыдущая версия)

- [Обзор шифрования azure Disk с Azure AD для виртуальных машин Windows](../../virtual-machines/windows/disk-encryption-overview-aad.md)
- [Шифрование azure disk с сценариями Azure AD на Windows VMs](../../virtual-machines/windows/disk-encryption-windows.md)
- [Создание и настройка хранилища ключей для шифрования дисков Azure с помощью Azure AD (предыдущий релиз)](../../virtual-machines/windows/disk-encryption-key-vault-aad.md)

## <a name="virtual-machine-scale-sets"></a>Масштабируемые наборы виртуальных машин

Следующие статьи содержат рекомендации по шифрованию виртуальных наборов масштабов машин.

- [Обзор шифрования azure Disk для виртуальных наборов масштабов машин](../../virtual-machine-scale-sets/disk-encryption-overview.md) 
- [Encrypt OS and attached data disks in a virtual machine scale set with the Azure CLI](../../virtual-machine-scale-sets/disk-encryption-cli.md) (Шифрование ОС и подключенных дисков данных в масштабируемом наборе виртуальных машин с помощью Azure CLI) 
- [Шифрование виртуальных наборов масштабов машины с помощью Azure Powershell.](../../virtual-machine-scale-sets/disk-encryption-powershell.md)
- [Шифрование виртуальных наборов масштабов машины с помощью менеджера ресурсов Azure](../../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
- [Creating and configuring a key vault for Azure Disk Encryption](../../virtual-machine-scale-sets/disk-encryption-key-vault.md) (Создание и настройка хранилища ключей для шифрования дисков Azure)
- [Use Azure Disk Encryption with virtual machine scale set extension sequencing](../../virtual-machine-scale-sets/disk-encryption-extension-sequencing.md) (Использование шифрования дисков Azure с помощью виртуализации расширения масштабируемого набора виртуальных машин)

## <a name="next-steps"></a>Дальнейшие действия

- [Обзор шифрования Azure](encryption-overview.md)
- [Шифрование данных в состоянии покоя](encryption-atrest.md)
- [Рекомендации по шифрованию и обеспечению защиты данных](data-encryption-best-practices.md)
