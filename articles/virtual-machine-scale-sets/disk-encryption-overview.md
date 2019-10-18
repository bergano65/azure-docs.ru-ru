---
title: Включение шифрования дисков Azure для масштабируемых наборов виртуальных машин
description: В этой статье приведены инструкции по включению шифрования дисков Microsoft Azure для масштабируемых наборов виртуальных машин.
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: article
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 69197b9c0d2a60e1833d03a3f12802221a8307e2
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530840"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>Шифрование дисков Azure для масштабируемых наборов виртуальных машин

Шифрование дисков Azure обеспечивает шифрование томов для дисков ОС и данных виртуальных машин, помогая защитить данные и обеспечить их защиту в соответствии с требованиями к безопасности и соблюдению требований Организации. Дополнительные сведения см. в статье [Шифрование дисков Azure: виртуальные машины Linux](../virtual-machines/linux/disk-encryption-overview.md) и [Шифрование дисков Azure: виртуальные машины Windows.](../virtual-machines/windows/disk-encryption-overview.md)  

Шифрование дисков Azure также можно применить к масштабируемым наборам виртуальных машин Windows и Linux в следующих экземплярах:
- Масштабируемые наборы, созданные с помощью управляемых дисков. Шифрование дисков Azure не поддерживается для масштабируемых наборов дисков в машинном (или неуправляемом) наборе.
- ОС и тома данных в масштабируемых наборах Windows.
- Тома данных в масштабируемых наборах Linux. Шифрование дисков ОС в масштабируемых наборах Linux не поддерживается.

Основные сведения о шифровании дисков Azure для масштабируемых наборов виртуальных машин см. в статье [Шифрование масштабируемых наборов виртуальных машин с помощью Azure CLI](disk-encryption-cli.md) или [Шифрование масштабируемых наборов виртуальных машин с помощью учебников по Azure CLI](disk-encryption-powershell.md) .

## <a name="next-steps"></a>Дальнейшие действия

- [Шифрование масштабируемых наборов виртуальных машин с помощью Azure Resource Manager](disk-encryption-azure-resource-manager.md)
- [Создание и Настройка хранилища ключей для шифрования дисков Azure](disk-encryption-key-vault.md)
- [Использование шифрования дисков Azure с виртуализацией расширения масштабируемого набора виртуальных машин](disk-encryption-extension-sequencing.md)