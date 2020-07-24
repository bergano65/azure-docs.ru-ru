---
title: Включение сквозного шифрования с помощью шифрования на управляемых дисках Host-портал Azure
description: Используйте шифрование на узле, чтобы включить сквозное шифрование на управляемых дисках Azure.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/23/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 96506e0ca09e9491135faa69d0b690b91ccc8177
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136331"
---
# <a name="enable-end-to-end-encryption-using-encryption-at-host---azure-portal"></a>Включение сквозного шифрования с помощью шифрования на узле портал Azure

При включении шифрования на узле данные, хранящиеся на узле виртуальной машины, шифруются в неактивных потоках и передаются в зашифрованном виде в службу хранилища. Концептуальные сведения о шифровании на узле, а также о других типах шифрования управляемых дисков см. [в статье шифрование на основе шифрования узла для данных виртуальной машины](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

[!INCLUDE [virtual-machines-disks-encryption-at-host-portal](../../../includes/virtual-machines-disks-encryption-at-host-portal.md)]

## <a name="next-steps"></a>Дальнейшие действия

[Примеры шаблонов Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)