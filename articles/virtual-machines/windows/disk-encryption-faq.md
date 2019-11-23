---
title: FAQ - Azure Disk Encryption for Windows VMs
description: This article provides answers to frequently asked questions about Microsoft Azure Disk Encryption for Windows IaaS VMs.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 11/01/2019
ms.custom: seodec18
ms.openlocfilehash: ea2a66a6b012664a9596a02ea32c1a0b677ee3ea
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384264"
---
# <a name="azure-disk-encryption-for-windows-vms-faq"></a>Azure Disk Encryption for Windows VMs FAQ

This article provides answers to frequently asked questions (FAQ) about Azure Disk Encryption for Windows VMs. For more information about this service, see [Azure Disk Encryption overview](disk-encryption-overview.md).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>В каких регионах предоставляется общедоступная версия шифрования дисков Azure?

Azure Disk Encryption is in general availability in all Azure public regions.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Какие возможности предоставляет шифрование дисков Azure?

Шифрование дисков Azure (общедоступная версия) поддерживает шаблоны Azure Resource Manager, Azure PowerShell и Azure CLI. Благодаря таким различным возможностям обеспечивается гибкость. You have three different options for enabling disk encryption for your VMs. For more information on the user experience and step-by-step guidance available in Azure Disk Encryption, see [Azure Disk Encryption scenarios for Windows](disk-encryption-windows.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Какова стоимость шифрования дисков Azure?

There's no charge for encrypting VM disks with Azure Disk Encryption, but there are charges associated with the use of Azure Key Vault. Дополнительные сведения о ценах на Azure Key Vault см. на [этой странице](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Как приступить к работе с шифрованием дисков Azure?

Чтобы приступить к работе, ознакомьтесь с [обзором шифрования дисков Azure](disk-encryption-overview.md).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>What VM sizes and operating systems support Azure Disk Encryption?

The [Azure Disk Encryption overview](disk-encryption-overview.md) article lists the [VM sizes](disk-encryption-overview.md#supported-vm-sizes) and [VM operating systems](disk-encryption-overview.md#supported-operating-systems) that support Azure Disk Encryption.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Можно ли с помощью шифрования дисков Azure зашифровать загрузочные тома и тома данных?

You can encrypt both boot and data volumes, but you can't encrypt the data without first encrypting the OS volume.

After you've encrypted the OS volume, disabling encryption on the OS volume isn't supported.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Can I encrypt an unmounted volume with Azure Disk Encryption?

No, Azure Disk Encryption only encrypts mounted volumes.

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>How do I rotate secrets or encryption keys?

To rotate secrets, just call the same command you used originally to enable disk encryption, specifying a different Key Vault. To rotate the key encryption key, call the same command you used originally to enable disk encryption, specifying the new key encryption. 

>[!WARNING]
> - If you have previously used [Azure Disk Encryption with Azure AD app](disk-encryption-windows-aad.md) by specifying Azure AD credentials to encrypt this VM, you will have to continue use this option to encrypt your VM. You can’t use Azure Disk Encryption on this encrypted VM as this isn’t a supported scenario, meaning switching away from AAD application for this encrypted VM isn’t supported yet.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>How do I add or remove a key encryption key if I didn't originally use one?

To add a key encryption key, call the enable command again passing the key encryption key parameter. To remove a key encryption key, call the enable command again without the key encryption key parameter.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Дает ли служба шифрования дисков Azure возможность создания собственных ключей (BYOK)?

Да, вы можете предоставить собственные ключи шифрования ключей. Эти ключи хранятся в Azure Key Vault, что представляет собой хранилище ключей для шифрования дисков Azure. For more information on the key encryption keys support scenarios, see [Creating and configuring a key vault for Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Можно ли использовать ключ шифрования ключей, созданный в Azure?

Да, можно использовать Azure Key Vault, чтобы создать ключ шифрования ключей для использования шифрования дисков Azure. Эти ключи хранятся в Azure Key Vault, что представляет собой хранилище ключей для шифрования дисков Azure. For more information on the key encryption key, see [Creating and configuring a key vault for Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Можно ли использовать локальную службу управления ключами или HSM для защиты ключей шифрования?

Использовать локальную службу управления ключами или HSM для защиты ключей шифрования с шифрованием дисков Azure нельзя. Для защиты ключей шифрования можно использовать только Azure Key Vault. For more information on the key encryption key support scenarios, see [Creating and configuring a key vault for Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Каковы предварительные требования для настройки шифрования дисков Azure?

Для использования шифрования дисков Azure существуют предварительные требования. See the [Creating and configuring a key vault for Azure Disk Encryption](disk-encryption-key-vault.md) article to create a new key vault, or set up an existing key vault for disk encryption access to enable encryption, and safeguard secrets and keys. For more information on the key encryption key support scenarios, see [Creating and configuring a key vault for Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Каковы предварительные требования для настройки шифрования дисков Azure с использованием приложения Azure AD (предыдущий выпуск)?

Для использования шифрования дисков Azure существуют предварительные требования. See the [Azure Disk Encryption with Azure AD](disk-encryption-windows-aad.md) content to create an Azure Active Directory application, create a new key vault, or set up an existing key vault for disk encryption access to enable encryption, and safeguard secrets and keys. For more information on the key encryption key support scenarios, see [Creating and configuring a key vault for Azure Disk Encryption with Azure AD](disk-encryption-key-vault-aad.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Поддерживается ли по-прежнему шифрование дисков Azure с помощью приложения Azure AD (предыдущий выпуск)?
Да. Шифрование дисков с помощью приложения Azure AD по-прежнему поддерживается. Тем не менее при шифровании новых виртуальных машин мы рекомендуем использовать новый метод, а не шифрование с помощью приложения Azure AD. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Можно ли перевести виртуальные машины, зашифрованные с помощью приложения Azure AD, на шифрование без приложения Azure AD?
  Сейчас для компьютеров, зашифрованных с помощью приложения Azure AD, нет прямого пути миграции к шифрованию без приложения Azure AD. Кроме того, нет прямого пути от шифрования без приложения Azure AD к шифрованию с помощью приложения AD. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Какую версию Azure PowerShell поддерживает шифрование дисков Azure?

Для настройки шифрования дисков Azure используйте последнюю версию пакета SDK для Azure PowerShell. Скачайте последнюю версию [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Шифрование дисков Azure *не* поддерживается в пакете SDK для Azure версии 1.1.0.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Что такое том BEK или /mnt/azure_bek_disk?

The "Bek volume" is a local data volume that securely stores the encryption keys for Encrypted Azure VMs.

> [!NOTE]
> Не удаляйте и не изменяйте содержимое на этом диске. Не отключайте диск, так как ключ шифрования необходим для любой операции шифрования на виртуальной машине IaaS.

## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Какой метод шифрования используется в шифровании дисков Azure?

Azure Disk Encryption selects the encryption method in BitLocker based on the version of Windows as follows:

| Windows Versions                 | Версия | Encryption Method        |
|----------------------------------|--------|--------------------------|
| Windows Server 2012, Windows 10, or greater  | >=1511 |XTS-AES 256 bit           |
| Windows Server 2012, Windows 8, 8.1, 10 | < 1511 |AES 256 bit *              |
| Windows Server 2008 R2            |        |AES 256 bit with Diffuser |

\* AES 256 bit with Diffuser isn't supported in Windows 2012 and later.

To determine Windows OS version, run the 'winver' tool in your virtual machine.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Если используется параметр EncryptFormatAll и указаны все типы томов, удалятся ли данные из уже зашифрованных дисков?
Нет. Данные не удаляются из дисков, которые уже зашифрованы с помощью службы шифрования дисков Azure. Так же как и в случае с диском ОС, параметр EncryptFormatAll не шифрует повторно уже зашифрованные диски данных. 

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Can I backup and restore an encrypted VM? 

Azure Backup provides a mechanism to backup and restore encrypted VM's within the same subscription and region.  For instructions, please see [Back up and restore encrypted virtual machines with Azure Backup](../../backup/backup-azure-vms-encryption.md).  Restoring an encrypted VM to a different region is not currently supported.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Где можно задать вопрос или оставить отзыв?

Задать вопрос или оставить отзыв можно на [форуме по шифрованию дисков Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Дальнейшие действия
Из этого документа вы получили ответы на самые распространенные вопросы, связанные с шифрованием дисков Azure. Дополнительные сведения об этой службе см. в следующих статьях:

- [Шифрование дисков Azure для виртуальных машин IaaS](disk-encryption-overview.md)
- [Шифрование диска в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Шифрование неактивных данных в Azure](../../security/fundamentals/encryption-atrest.md)
