---
title: Включение шифрования дисков Azure для масштабируемых наборов виртуальных машин
description: В этой статье приведены инструкции по включению шифрования дисков Microsoft Azure для масштабируемых наборов виртуальных машин.
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: article
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 0097d0e1d5ea7de092da14683d4bab3d673b2219
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177781"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>Шифрование дисков Azure для масштабируемых наборов виртуальных машин

Шифрование дисков Azure обеспечивает шифрование томов для дисков ОС и данных виртуальных машин, помогая защитить данные и обеспечить их защиту в соответствии с требованиями к безопасности и соблюдению требований Организации. Дополнительные сведения см. в статье [Шифрование дисков Azure: виртуальные машины Linux](../virtual-machines/linux/disk-encryption-overview.md) и [Шифрование дисков Azure: виртуальные машины Windows.](../virtual-machines/windows/disk-encryption-overview.md)  

Шифрование дисков Azure также можно применить к масштабируемым наборам виртуальных машин Windows и Linux в следующих экземплярах:
- Масштабируемые наборы, созданные с помощью управляемых дисков. Шифрование дисков Azure не поддерживается для масштабируемых наборов дисков в машинном (или неуправляемом) наборе.
- ОС и тома данных в масштабируемых наборах Windows.
- Тома данных в масштабируемых наборах Linux. Шифрование дисков ОС в масштабируемых наборах Linux не поддерживается.

Вы можете изучить основы шифрования дисков Azure для масштабируемых наборов виртуальных машин всего за несколько минут с помощью [шифрования масштабируемых наборов виртуальных машин, используя Azure CLI](disk-encryption-cli.md) или [шифровать масштабируемые наборы виртуальных машин с помощью Azure PowerShell](disk-encryption-powershell.md) учебных.

## <a name="next-steps"></a>Дальнейшие действия

- [Шифрование масштабируемых наборов виртуальных машин с помощью Azure Resource Manager](disk-encryption-azure-resource-manager.md)
- [Creating and configuring a key vault for Azure Disk Encryption](disk-encryption-key-vault.md) (Создание и настройка хранилища ключей для шифрования дисков Azure)
- [Use Azure Disk Encryption with virtual machine scale set extension sequencing](disk-encryption-extension-sequencing.md) (Использование шифрования дисков Azure с помощью виртуализации расширения масштабируемого набора виртуальных машин)
