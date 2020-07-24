---
title: Включение двойного шифрования на дисках, управляемых портал Azureами неактивных дисков
description: Включите двойное шифрование неактивных данных для управляемых дисков с помощью портал Azure.
author: roygara
ms.date: 07/23/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: b244ef36e4e1a15327c053402b28ee2272fc71f0
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136325"
---
# <a name="azure-portal---enable-double-encryption-at-rest-on-your-managed-disks"></a>Портал Azure-включить двойное шифрование неактивных на управляемых дисках

Хранилище дисков Azure поддерживает двойное шифрование при хранении для управляемых дисков. Концептуальные сведения о двойном шифровании неактивных данных и других типах шифрования дисков см. в разделе [двойное шифрование при](disk-encryption.md#double-encryption-at-rest) хранении статьи о шифровании диска.

[!INCLUDE [virtual-machines-double-encryption-at-rest-portal](../../../includes/virtual-machines-disks-double-encryption-at-rest-portal.md)]

## <a name="next-steps"></a>Дальнейшие действия

- [Azure PowerShell — включить управляемые клиентом ключи с помощью управляемых дисков с шифрованием на стороне сервера](disks-enable-customer-managed-keys-powershell.md)
- [Примеры шаблонов Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)