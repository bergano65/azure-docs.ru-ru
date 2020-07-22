---
title: Портал Azure. Включение управляемых пользователем ключей с помощью управляемых дисков SSE
description: Включите управляемые пользователем ключи на управляемых дисках с помощью портал Azure.
author: roygara
ms.date: 07/10/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 0128d2b88d6eb2464ef95824330cfab07ee3703d
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86236145"
---
# <a name="enable-customer-managed-keys-with-server-side-encryption---managed-disks---portal"></a>Включение управляемых пользователем ключей с помощью шифрования на стороне сервера — на портале

Хранилище дисков Azure позволяет управлять собственными ключами при использовании шифрования на стороне сервера (SSE) для управляемых дисков, если вы решили. Основные сведения о SSE с управляемыми клиентами ключами, а также о других типах шифрования дисков см. в разделе " [ключи, управляемые клиентом](disk-encryption.md#customer-managed-keys) " статьи о шифровании дисков.

## <a name="restrictions"></a>Ограничения

В настоящее время ключи, управляемые клиентом, имеют следующие ограничения.

- Если эта функция включена для диска, ее нельзя отключить.
    Если нужно обойти это ограничение, необходимо [скопировать все данные](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) на другой управляемый диск, не использующий ключи, управляемые клиентом.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> Управляемые клиентом ключи используют управляемые удостоверения для ресурсов Azure — функцию Azure Active Directory (Azure AD). При настройке управляемых пользователем ключей управляемое удостоверение автоматически назначается вашим ресурсам. Если впоследствии вы перемещаете подписку, группу ресурсов или управляемый диск из одного каталога Azure AD в другой, управляемое удостоверение, связанное с управляемыми дисками, не передается в новый клиент, поэтому управляемые клиентом ключи могут перестать работать. Дополнительные сведения см. в статье [Передача подписки между каталогами Azure AD](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="next-steps"></a>Дальнейшие действия

- [Изучите шаблоны Azure Resource Manager для создания зашифрованных дисков с помощью управляемых клиентом ключей](https://github.com/ramankumarlive/manageddiskscmkpreview).)
- [Репликация компьютеров, использующих диски с ключами, управляемыми клиентом](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Настройка аварийного восстановления виртуальных машин VMware в Azure с помощью PowerShell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Настройка аварийного восстановления виртуальных машин Hyper-V в Azure с помощью PowerShell и Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
