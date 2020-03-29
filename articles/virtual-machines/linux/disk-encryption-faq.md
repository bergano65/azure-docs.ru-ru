---
title: Часто задаваемые вопросы - Шифрование дисков Azure для Linux VMs
description: В этой статье содержатся ответы на часто задаваемые вопросы о шифровании дисков Microsoft Azure для Linux IaaS VMs.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: b285daa465c8d069b359e94c9203c1ffbea24c06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970684"
---
# <a name="azure-disk-encryption-for-iaas-vms-faq"></a>Часто задаваемые вопросы о шифровании дисков Azure для виртуальных машин IaaS

В этой статье содержатся ответы на часто задаваемые вопросы (Часто задаваемые вопросы) о шифровании дисков Azure для Linux VMs. Для получения дополнительной информации об этой службе смотрите [обзор шифрования дисков Azure](disk-encryption-overview.md).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>В каких регионах предоставляется общедоступная версия шифрования дисков Azure?

Шифрование Azure Disk для Linux VMs в целом доступно во всех общедоступных регионах Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Какие возможности предоставляет шифрование дисков Azure?

Шифрование дисков Azure (общедоступная версия) поддерживает шаблоны Azure Resource Manager, Azure PowerShell и Azure CLI. Благодаря таким различным возможностям обеспечивается гибкость. У вас есть три различных варианта включения шифрования диска для ваших ВМ. Для получения дополнительной информации об опыте работы с пользователями [Azure Disk Encryption scenarios for Linux](disk-encryption-linux.md)и пошаговых рекомендациях, доступных в шифровании дисков Azure, см.

## <a name="how-much-does-azure-disk-encryption-cost"></a>Какова стоимость шифрования дисков Azure?

За шифрование VM-дисков с помощью azure Disk Encryption плата не взимается, но есть расходы, связанные с использованием Azure Key Vault. Дополнительные сведения о ценах на Azure Key Vault см. на [этой странице](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Как приступить к работе с шифрованием дисков Azure?

Чтобы приступить к работе, ознакомьтесь с [обзором шифрования дисков Azure](disk-encryption-overview.md).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Какие размеры VM и операционные системы поддерживают шифрование Azure Disk?

В статье, опубликованной в обзоре [шифрования azure Disk,](disk-encryption-overview.md) [перечислены размеры VM](disk-encryption-overview.md#supported-vm-sizes) и [операционные системы VM,](disk-encryption-overview.md#supported-operating-systems) поддерживающие шифрование Azure Disk.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Можно ли с помощью шифрования дисков Azure зашифровать загрузочные тома и тома данных?

Да, вы можете шифровать как объемы загрузки, так и объемы данных, или вы можете шифровать объем данных без необходимости шифрования объема ОС в первую очередь. 

После шифрования объема ОС отключение шифрования на томе ОС не поддерживается. Для Linux VMs в наборе масштабов можно шифровать только объем данных.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Могу ли я шифровать неустановленный том с помощью шифрования дисков Azure?

Нет, шифрование Azure Disk только шифрует установленные тома.

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Как повернуть секреты или ключи шифрования?

Чтобы повернуть секреты, просто позвоните по той же команде, что и первоначально для включения шифрования диска, указывая другой Key Vault. Чтобы повернуть ключ шифрования, позвоните той же команде, что и первоначально для включения шифрования диска, с указанием нового шифрования ключа. 

>[!WARNING]
> - Если ранее вы использовали [шифрование Azure Disk с приложением Azure AD,](disk-encryption-linux-aad.md) указав учетные данные Azure AD для шифрования этого VM, вам придется продолжать использовать эту опцию для шифрования VM. На этой зашифрованной виртуальной машине нельзя использовать шифрование дисков Azure, так как этот сценарий не работает — переключение из приложения AAD для данной зашифрованной виртуальной машины сейчас не поддерживается.

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

Для использования шифрования дисков Azure существуют предварительные требования. Просмотреть шифрование Azure Disk с контентом [Azure AD,](disk-encryption-linux-aad.md) чтобы создать приложение Active Directory Azure, создать новое хранилище ключей или создать существующее хранилище ключей для доступа к шифрованию дисков для шифрования, а также сохранить секреты и ключи. Для получения дополнительной информации о ключевых сценариях поддержки ключей шифрования см. [Создание и настройка хранилища ключей для шифрования Azure Disk с Azure AD.](disk-encryption-key-vault-aad.md)

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Поддерживается ли по-прежнему шифрование дисков Azure с помощью приложения Azure AD (предыдущий выпуск)?
Да. Шифрование дисков с помощью приложения Azure AD по-прежнему поддерживается. Тем не менее при шифровании новых виртуальных машин мы рекомендуем использовать новый метод, а не шифрование с помощью приложения Azure AD. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Можно ли перевести виртуальные машины, зашифрованные с помощью приложения Azure AD, на шифрование без приложения Azure AD?
  Сейчас для компьютеров, зашифрованных с помощью приложения Azure AD, нет прямого пути миграции к шифрованию без приложения Azure AD. Кроме того, нет прямого пути от шифрования без приложения Azure AD к шифрованию с помощью приложения AD. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Какую версию Azure PowerShell поддерживает шифрование дисков Azure?

Для настройки шифрования дисков Azure используйте последнюю версию пакета SDK для Azure PowerShell. Скачайте последнюю версию [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Шифрование дисков Azure *не* поддерживается в пакете SDK для Azure версии 1.1.0.

> [!NOTE]
> Расширение предварительного провиста шифрования дисков Linux Azure "Microsoft.OSTCExtension.AzureDiskEncryptionForLinux" является устаревшим. Это расширение было опубликовано для предварительного релиза шифрования дисков Azure. Не следует использовать предварительную версию расширения при тестировании или развертывании.

> Для сценариев развертывания, таких как Azure Resource Manager (ARM), где вам необходимо развернуть расширение шифрования диска Azure для Linux VM для включения шифрования на Вашем Linux IaaS VM, необходимо использовать расширение с поддержкой шифрования диска Azure " Microsoft.Azure.Security.AzureDiskEncryptionForLinux".

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Можно ли применять шифрование дисков Azure в пользовательском образе Linux?

Шифрование дисков Azure применить в пользовательском образе Linux нельзя. Поддерживаются только образы Linux из коллекции поддерживаемых дистрибутивов, приведенных выше. Пользовательские образы Linux сейчас не поддерживаются.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Можно ли применить обновления к виртуальной машине Linux Red Hat с использованием команды yum update?

Да, вы можете выполнить yum обновление на Red Hat Linux VM.  Для получения дополнительной информации смотрите [шифрование дисков Azure в изолированной сети.](disk-encryption-isolated-network.md)

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Какие действия по шифрованию дисков Azure рекомендуется использовать для Linux?

Для достижения наилучших результатов в Linux рекомендуется такая последовательность действий:
* Для начала выберите неизмененный готовый образ из коллекции, соответствующий требуемому дистрибутиву и версии операционной системы.
* Создайте резервные копии всех подключенных дисков, которые будут зашифрованы.  Это позволит выполнить восстановление в случае сбоя, например при перезагрузке виртуальной машины до завершения шифрования.
* Выполните шифрование (этот процесс может занять несколько часов или даже дней в зависимости от характеристик виртуальной машины и размера всех подключенных дисков данных).
* При необходимости настройте образ и добавьте к нему программное обеспечение.

Если эти действия невозможно выполнить, в качестве альтернативы шифрованию всего диска с помощью dm-crypt можно воспользоваться [шифрованием службы хранилища](../../storage/common/storage-service-encryption.md) (SSE) на уровне учетной записи хранения платформы.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Что такое том BEK или /mnt/azure_bek_disk?

"Бек том" — это локальный объем данных, который надежно хранит ключи шифрования для зашифрованных VMs Azure.
> [!NOTE]
> Не удаляйте и не изменяйте содержимое на этом диске. Не отключайте диск, так как ключ шифрования необходим для любой операции шифрования на виртуальной машине IaaS.


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Какой метод шифрования используется в шифровании дисков Azure?

Лазурное шифрование диска использует расшифровку по умолчанию aes-xts-plain64 с 256-битным ключом master.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Если используется параметр EncryptFormatAll и указаны все типы томов, удалятся ли данные из уже зашифрованных дисков?
Нет. Данные не удаляются из дисков, которые уже зашифрованы с помощью службы шифрования дисков Azure. Так же как и в случае с диском ОС, параметр EncryptFormatAll не шифрует повторно уже зашифрованные диски данных. Дополнительные сведения см. в разделе [Использование параметра EncryptFormatAll с Azure CLI](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).        

## <a name="is-xfs-filesystem-supported"></a>Поддерживается ли файловая система XFS?
Объемы XFS поддерживаются для шифрования дисков данных только с помощью EncryptFormatAll. Это позволит переформатировать объем, сняв все данные, которые ранее были там. Дополнительные сведения см. в разделе [Использование параметра EncryptFormatAll с Azure CLI](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Могу ли я создать резервную и восстановить зашифрованный VM? 

Резервное копирование Azure обеспечивает механизм резервного копирования и восстановления зашифрованных VM в пределах одной подписки и региона.  Для получения инструкций, пожалуйста, [см. Резервное копирование и восстановление зашифрованных виртуальных машин с Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).  Восстановление зашифрованного VM в другой области в настоящее время не поддерживается.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Где можно задать вопрос или оставить отзыв?

Задать вопрос или оставить отзыв можно на [форуме по шифрованию дисков Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Дальнейшие действия
Из этого документа вы получили ответы на самые распространенные вопросы, связанные с шифрованием дисков Azure. Дополнительные сведения об этой службе см. в следующих статьях:

- [Обзор шифрования дисков Azure](disk-encryption-overview.md)
- [Шифрование диска в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Шифрование неактивных данных в Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
