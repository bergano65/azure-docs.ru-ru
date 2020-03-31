---
title: Включить шифрование azure диска для виртуальных наборов шкалы машин
description: В этой статье содержатся инструкции по включению шифрования дисков Microsoft Azure для наборов виртуальных машин
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 36eb83d99af299260ebbbd74fe56d69921ba389b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278967"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>Лазурное шифрование дисков для виртуальных наборов шкалы машин

Лазурное шифрование дисков обеспечивает громкое шифрование для ОС и дисков данных ваших виртуальных машин, помогая защитить и защитить ваши данные для выполнения организационных обязательств по обеспечению безопасности и соответствия требованиям. Чтобы узнать больше, смотрите [шифрование дисков Azure: Linux VMs](../virtual-machines/linux/disk-encryption-overview.md) и [шифрование дисков Azure: Windows VMs](../virtual-machines/windows/disk-encryption-overview.md)  

Шифрование azure Disk также может быть применено к наборам виртуальных машин Windows и Linux в следующих случаях:
- Наборы масштабирования, созданные с управляемыми дисками. Шифрование Azure Disk не поддерживается для нативных (или неуправляемых) наборов дисковых масштабов.
- ОС и объемы данных в наборах масштабов Windows.
- Объемы данных в наборах масштабов Linux. Шифрование диска ОС НЕ поддерживается в настоящее время для наборов масштаба Linux.

Вы можете узнать основы шифрования Azure Disk для виртуальных наборов масштабов машин всего за несколько минут с [помощью наборов масштабов виртуального масштаба шифрования с использованием Azure CLI](disk-encryption-cli.md) или [наборов виртуального масштаба машины с использованием учебников Azure PowerShell.](disk-encryption-powershell.md)

## <a name="next-steps"></a>Дальнейшие действия

- [Шифрование виртуальных наборов масштабов машины с помощью менеджера ресурсов Azure](disk-encryption-azure-resource-manager.md)
- [Creating and configuring a key vault for Azure Disk Encryption](disk-encryption-key-vault.md) (Создание и настройка хранилища ключей для шифрования дисков Azure)
- [Use Azure Disk Encryption with virtual machine scale set extension sequencing](disk-encryption-extension-sequencing.md) (Использование шифрования дисков Azure с помощью виртуализации расширения масштабируемого набора виртуальных машин)
