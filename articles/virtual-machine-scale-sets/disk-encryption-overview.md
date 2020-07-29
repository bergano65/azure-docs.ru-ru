---
title: Включение шифрования дисков Azure для масштабируемых наборов виртуальных машин
description: В этой статье приведены инструкции по включению шифрования дисков Microsoft Azure для масштабируемых наборов виртуальных машин.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 6b9805d66149a18216a200bc89a79b3e06106c9d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83195108"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>Шифрование дисков Azure для масштабируемых наборов виртуальных машин

Шифрование дисков Azure обеспечивает шифрование томов для дисков ОС и данных виртуальных машин, помогая защитить данные и обеспечить их защиту в соответствии с требованиями к безопасности и соблюдению требований Организации. Дополнительные сведения см. в статье [Шифрование дисков Azure: виртуальные машины Linux](../virtual-machines/linux/disk-encryption-overview.md) и [Шифрование дисков Azure: виртуальные машины Windows.](../virtual-machines/windows/disk-encryption-overview.md)  

Шифрование дисков Azure также можно применить к масштабируемым наборам виртуальных машин Windows и Linux в следующих экземплярах:
- Масштабируемые наборы, созданные с помощью управляемых дисков. Шифрование дисков Azure не поддерживается для масштабируемых наборов дисков в машинном (или неуправляемом) наборе.
- ОС и тома данных в масштабируемых наборах Windows.
- Тома данных в масштабируемых наборах Linux. Шифрование дисков ОС в масштабируемых наборах Linux не поддерживается.

Основные сведения о шифровании дисков Azure для масштабируемых наборов виртуальных машин см. в статье [Шифрование масштабируемых наборов виртуальных машин с помощью Azure CLI](disk-encryption-cli.md) или [Шифрование масштабируемых наборов виртуальных машин с помощью учебников по Azure PowerShell](disk-encryption-powershell.md) .

## <a name="next-steps"></a>Дальнейшие шаги

- [Шифрование масштабируемых наборов виртуальных машин с помощью Azure Resource Manager](disk-encryption-azure-resource-manager.md)
- [Creating and configuring a key vault for Azure Disk Encryption](disk-encryption-key-vault.md) (Создание и настройка хранилища ключей для шифрования дисков Azure)
- [Use Azure Disk Encryption with virtual machine scale set extension sequencing](disk-encryption-extension-sequencing.md) (Использование шифрования дисков Azure с помощью виртуализации расширения масштабируемого набора виртуальных машин)
