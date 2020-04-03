---
title: Часто задаваемые вопросы - Шифрование azure disk для Windows VMs
description: В этой статье содержатся ответы на часто задаваемые вопросы о шифровании дисков Microsoft Azure для Windows IaaS VMs.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 11/01/2019
ms.custom: seodec18
ms.openlocfilehash: 5cc6a3d8d736209f762959bca6f8ad379c14203f
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582710"
---
# <a name="azure-disk-encryption-for-windows-vms-faq"></a>Лазурный диск шифрование для Windows VMs часто задаваемые вопросы

В этой статье содержатся ответы на часто задаваемые вопросы (Часто задаваемые вопросы) о шифровании дисков Azure для Windows VMs. Для получения дополнительной информации об этой службе смотрите [обзор шифрования дисков Azure](disk-encryption-overview.md).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>В каких регионах предоставляется общедоступная версия шифрования дисков Azure?

Шифрование Azure Disk в целом доступно во всех общедоступных регионах Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Какие возможности предоставляет шифрование дисков Azure?

Шифрование дисков Azure (общедоступная версия) поддерживает шаблоны Azure Resource Manager, Azure PowerShell и Azure CLI. Благодаря таким различным возможностям обеспечивается гибкость. У вас есть три различных варианта включения шифрования диска для ваших ВМ. Для получения дополнительной информации об пользовательском опыте и пошаговых [Azure Disk Encryption scenarios for Windows](disk-encryption-windows.md)рекомендациях, доступных в шифровании дисков Azure, см.

## <a name="how-much-does-azure-disk-encryption-cost"></a>Какова стоимость шифрования дисков Azure?

За шифрование VM-дисков с помощью azure Disk Encryption плата не взимается, но есть расходы, связанные с использованием Azure Key Vault. Дополнительные сведения о ценах на Azure Key Vault см. на [этой странице](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Как приступить к работе с шифрованием дисков Azure?

Чтобы приступить к работе, ознакомьтесь с [обзором шифрования дисков Azure](disk-encryption-overview.md).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Какие размеры VM и операционные системы поддерживают шифрование Azure Disk?

В статье, опубликованной в обзоре [шифрования azure Disk,](disk-encryption-overview.md) [перечислены размеры VM](disk-encryption-overview.md#supported-vms) и [операционные системы VM,](disk-encryption-overview.md#supported-operating-systems) поддерживающие шифрование Azure Disk.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Можно ли с помощью шифрования дисков Azure зашифровать загрузочные тома и тома данных?

Вы можете шифровать как загрузку, так и объемы данных, но вы не можете шифровать данные без предварительного шифрования объема ОС.

После шифрования объема ОС отключение шифрования на томе ОС не поддерживается.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Могу ли я шифровать неустановленный том с помощью шифрования дисков Azure?

Нет, шифрование Azure Disk только шифрует установленные тома.

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Как повернуть секреты или ключи шифрования?

Чтобы повернуть секреты, просто позвоните по той же команде, что и первоначально для включения шифрования диска, указывая другой Key Vault. Чтобы повернуть ключ шифрования, позвоните той же команде, что и первоначально для включения шифрования диска, с указанием нового шифрования ключа. 

>[!WARNING]
> - Если ранее вы использовали [шифрование Azure Disk с приложением Azure AD,](disk-encryption-windows-aad.md) указав учетные данные Azure AD для шифрования этого VM, вам придется продолжать использовать эту опцию для шифрования VM. Вы не можете использовать шифрование Azure Disk на этом зашифрованном VM, так как это не поддерживается сценарий, то есть отключение от приложения AAD для этого зашифрованного VM еще не поддерживается.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Как добавить или удалить ключ шифрования, если я изначально его не использовал?

Чтобы добавить ключ шифрования, позвоните в команду включить снова, передавая ключевой параметр ключа шифрования. Чтобы удалить ключ шифрования, позвоните в команду включить снова без параметра ключа шифрования.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Дает ли служба шифрования дисков Azure возможность создания собственных ключей (BYOK)?

Да, вы можете предоставить собственные ключи шифрования ключей. Эти ключи хранятся в Azure Key Vault, что представляет собой хранилище ключей для шифрования дисков Azure. Для получения дополнительной информации о сценариях поддержки ключей шифрования [см.](disk-encryption-key-vault.md)

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Можно ли использовать ключ шифрования ключей, созданный в Azure?

Да, можно использовать Azure Key Vault, чтобы создать ключ шифрования ключей для использования шифрования дисков Azure. Эти ключи хранятся в Azure Key Vault, что представляет собой хранилище ключей для шифрования дисков Azure. Для получения дополнительной информации о ключе шифрования можно увидеть [Создание и настройку хранилища ключей для шифрования Azure Disk.](disk-encryption-key-vault.md)

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Можно ли использовать локальную службу управления ключами или HSM для защиты ключей шифрования?

Использовать локальную службу управления ключами или HSM для защиты ключей шифрования с шифрованием дисков Azure нельзя. Для защиты ключей шифрования можно использовать только Azure Key Vault. Для получения дополнительной информации о ключевых сценариях поддержки ключей шифрования [см.](disk-encryption-key-vault.md)

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Каковы предварительные требования для настройки шифрования дисков Azure?

Для использования шифрования дисков Azure существуют предварительные требования. Просмотрите [создание и настройку хранилища ключей для](disk-encryption-key-vault.md) статьи по шифрованию azure Disk Encryption для создания нового хранилища ключей или навязайте существующее хранилище ключей для доступа к шифрованию дисков для шифрования и защиты секретов и ключей. Для получения дополнительной информации о ключевых сценариях поддержки ключей шифрования [см.](disk-encryption-key-vault.md)

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Каковы предварительные требования для настройки шифрования дисков Azure с использованием приложения Azure AD (предыдущий выпуск)?

Для использования шифрования дисков Azure существуют предварительные требования. Просмотреть шифрование Azure Disk с контентом [Azure AD,](disk-encryption-windows-aad.md) чтобы создать приложение Active Directory Azure, создать новое хранилище ключей или создать существующее хранилище ключей для доступа к шифрованию дисков для шифрования, а также сохранить секреты и ключи. Для получения дополнительной информации о ключевых сценариях поддержки ключей шифрования см. [Создание и настройка хранилища ключей для шифрования Azure Disk с Azure AD.](disk-encryption-key-vault-aad.md)

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Поддерживается ли по-прежнему шифрование дисков Azure с помощью приложения Azure AD (предыдущий выпуск)?
Да. Шифрование дисков с помощью приложения Azure AD по-прежнему поддерживается. Тем не менее при шифровании новых виртуальных машин мы рекомендуем использовать новый метод, а не шифрование с помощью приложения Azure AD. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Можно ли перевести виртуальные машины, зашифрованные с помощью приложения Azure AD, на шифрование без приложения Azure AD?
  Сейчас для компьютеров, зашифрованных с помощью приложения Azure AD, нет прямого пути миграции к шифрованию без приложения Azure AD. Кроме того, нет прямого пути от шифрования без приложения Azure AD к шифрованию с помощью приложения AD. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Какую версию Azure PowerShell поддерживает шифрование дисков Azure?

Для настройки шифрования дисков Azure используйте последнюю версию пакета SDK для Azure PowerShell. Скачайте последнюю версию [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Шифрование дисков Azure *не* поддерживается в пакете SDK для Azure версии 1.1.0.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Что такое том BEK или /mnt/azure_bek_disk?

"Бек том" — это локальный объем данных, который надежно хранит ключи шифрования для зашифрованных VMs Azure.

> [!NOTE]
> Не удаляйте и не изменяйте содержимое на этом диске. Не отключайте диск, так как ключ шифрования необходим для любой операции шифрования на виртуальной машине IaaS.

## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Какой метод шифрования используется в шифровании дисков Azure?

Лазурное шифрование выбирает метод шифрования в BitLocker на основе версии Windows следующим образом:

| Версии для Windows                 | Version | Метод шифрования        |
|----------------------------------|--------|--------------------------|
| Windows Server 2012, Windows 10 или больше  | >No1511 |XTS-AES 256 бит           |
| Windows Server 2012, Windows 8, 8.1, 10 | < 1511 г. |AES 256 бит              |
| Windows Server 2008 R2            |        |AES 256 бит с диффузором |

\*AES 256 бит с Diffuser не поддерживается в Windows 2012 и позже.

Чтобы определить версию Windows OS, запустите инструмент 'winver' в вашей виртуальной машине.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Если используется параметр EncryptFormatAll и указаны все типы томов, удалятся ли данные из уже зашифрованных дисков?
Нет. Данные не удаляются из дисков, которые уже зашифрованы с помощью службы шифрования дисков Azure. Так же как и в случае с диском ОС, параметр EncryptFormatAll не шифрует повторно уже зашифрованные диски данных. 

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Могу ли я создать резервную и восстановить зашифрованный VM? 

Резервное копирование Azure обеспечивает механизм резервного копирования и восстановления зашифрованных VM в пределах одной подписки и региона.  Для получения инструкций, пожалуйста, [см. Резервное копирование и восстановление зашифрованных виртуальных машин с Azure Backup](../../backup/backup-azure-vms-encryption.md).  Восстановление зашифрованного VM в другой области в настоящее время не поддерживается.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Где можно задать вопрос или оставить отзыв?

Задать вопрос или оставить отзыв можно на [форуме по шифрованию дисков Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Следующие шаги
Из этого документа вы получили ответы на самые распространенные вопросы, связанные с шифрованием дисков Azure. Дополнительные сведения об этой службе см. в следующих статьях:

- [Обзор шифрования дисков Azure](disk-encryption-overview.md)
- [Шифрование диска в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Шифрование неактивных данных в Azure](../../security/fundamentals/encryption-atrest.md)
