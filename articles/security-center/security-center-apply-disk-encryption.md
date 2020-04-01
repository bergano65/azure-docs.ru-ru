---
title: Шифрование диска в центре безопасности Azure | Документация Майкрософт
description: В этом документе показано, как реализовать рекомендацию Центра безопасности Azure **Приоритизируйте шифрование диска.**
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6cc7824a-8d6b-4a5f-ab40-e3bbaebc4a91
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: e0677ecc63573d04e384a0104ee0e4e28b4d5e3b
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473277"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Шифрование диска в центре безопасности Azure

Центр безопасности Azure рекомендует использовать шифрование azure Disk на незашифрованных дисках как на Windows, так и на Linux VM. Шифрование дисков позволяет шифровать диски виртуальных машин IaaS Windows и Linux.  Шифровать рекомендуется как том операционной системы, так и том данных на виртуальной машине.

Дисковое шифрование использует стандартные для отрасли функции ([BitLocker](https://technet.microsoft.com/library/cc732774.aspx) в Windows и [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) в Linux). Эти функции обеспечивают шифрование ОС и данных, помогая защитить информацию в соответствии с применимыми требованиями к безопасности и стандартами. Это решение интегрировано с [хранилищем ключей Azure](https://azure.microsoft.com/documentation/services/key-vault/) и позволяет управлять ключами и секретами дискового шифрования через подписку хранилища ключей. Шифрование выполняется для всех данных на дисках виртуальных машин в [хранилище Azure](https://azure.microsoft.com/documentation/services/storage/).

Список поддерживаемых версий Windows и Linux [можно](../virtual-machines/windows/disk-encryption-overview.md#supported-vms-and-operating-systems) узнать в документации по шифрованию Azure Disk.

## <a name="implement-the-recommendation"></a>Выполнение рекомендаций
1. На странице **Рекомендации** необходимо **наносить шифрование диска на виртуальных машинах.**
2. Из **нездоровых ресурсов**выберите VM, для которого рекомендуется шифрование диска.
3. Следуйте инструкциям, чтобы применить шифрование дисков к этим виртуальным машинам.

![Применение шифрования дисков](./media/security-center-apply-disk-encryption/apply-disk-encryption.png)

Для шифрования виртуальных машин Azure, которые определены центром безопасности как требующие шифрования, мы рекомендуем выполнить следующие действия.

* Установите и настройте Azure PowerShell. Это позволит вам выполнять команды PowerShell, чтобы установить необходимые компоненты для шифрования виртуальных машин Azure.
* Получите и выполните сценарий Azure PowerShell с необходимыми компонентами для шифрования дисков Azure.
* Зашифруйте свои виртуальные машины.

[Шифрование Windows IaaS VM с Azure PowerShell](../virtual-machines/windows/disk-encryption-powershell-quickstart.md) - Прогулки вас через эти шаги и предполагает, что вы используете клиентскую машину Windows, из которой вы можете настроить шифрование диска.

Есть разные подходы, используемые при работе с виртуальными машинами Azure. Если вы уже хорошо разбираетесь в Azure PowerShell или Azure CLI, то вы можете использовать альтернативные подходы. См. дополнительные сведения о [дисковом шифровании Azure](../security/fundamentals/encryption-overview.md).

## <a name="see-also"></a>См. также
В этом документе объясняется, как выполнить рекомендацию Центра безопасности "Применение шифрования дисков". Чтобы узнать больше о шифровании диска, см.:

* [Шифрование и управление ключами с Azure Key Vault](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (видео, 36 мин 39 сек)--Узнайте, как использовать управление шифрованием дисков для IaaS VMs и Azure Key Vault, чтобы помочь защитить и защитить ваши данные.
* [Шифрование диска Azure](../security/fundamentals/encryption-overview.md) (документ)-Подробнее включить шифрование дисков для Windows и Linux VMs.