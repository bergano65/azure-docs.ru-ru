---
title: Удаление хранилища восстановления сайта Azure
description: Узнайте, как удалить хранилище Служб восстановления, настроенного для Azure Site Recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/05/2019
ms.author: rajanaki
ms.openlocfilehash: 0e409ffdedbac822aedf48833f2dd85f8e04afa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894980"
---
# <a name="delete-a-site-recovery-services-vault"></a>Удаление хранилища Служб восстановления для Site Recovery

В этой статье описывается, как удалить хранилище служб восстановления для восстановления сайта. Чтобы удалить хранилище, используемое в Azure Backup, см. сведения в статье [Удаление резервного хранилища в Azure](../backup/backup-azure-delete-vault.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="before-you-start"></a>Перед началом работы

Прежде чем удалить хранилище, необходимо удалить зарегистрированные серверы и предметы в хранилище. То, что необходимо удалить, зависит от развернутых сценариев репликации. 


## <a name="delete-a-vault-azure-vm-to-azure"></a>Удалить VM хранилища-Azure в Azure

1. Следуйте [этим инструкциям,](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure) чтобы удалить все защищенные VMs.
2. Затем удалите хранилище.

## <a name="delete-a-vault-vmware-vm-to-azure"></a>Удалить хранилище VMware VM в Azure

1. Следуйте [этим инструкциям,](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) чтобы удалить все защищенные VMs.
2. Выполните [следующие действия,](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) чтобы удалить все политики репликации.
3. Удалите ссылки на vCenter, используя [эти шаги.](vmware-azure-manage-vcenter.md#delete-a-vcenter-server)
4. Следуйте [этим инструкциям](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server) для вывода из эксплуатации сервера конфигурации.
5. Затем удалите хранилище.


## <a name="delete-a-vault-hyper-v-vm-with-vmm-to-azure"></a>Удалить хранилище-Hyper-V VM (с VMM) в Azure

1. Выполните [следующие действия,](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario) чтобы удалить Hyper-V VMs, управляемые System Center VMM.
2. Отмежеваться и удалять все политики репликации. Сделайте это в хранилище > **инфраструктура** > восстановления сайта для**системного центра VMM** > **Репликационные политики.**
3. Выполните [следующие действия,](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) чтобы отменить регистрацию подключенного VMM-сервера.
4. Затем удалите хранилище.

## <a name="delete-a-vault-hyper-v-vm-to-azure"></a>Удалить хранилище-Hyper-V VM в Azure

1. Выполните [следующие действия,](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure) чтобы удалить все защищенные VMs.
2. Отмежеваться и удалять все политики репликации. Сделайте это в хранилище > **инфраструктура** > восстановления сайта для**Hyper-V сайтов** > **Репликационные политики.**
3. Следуйте [этим инструкциям,](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site) чтобы отменить регистрацию хоста Hyper-V.
4. Удалите сайт Hyper-V.
5. Затем удалите хранилище.


## <a name="use-powershell-to-force-delete-the-vault"></a>Принудительное удаление хранилища с помощью PowerShell 

> [!Important]
> Если вы тестируете продукт и вас не беспокоит возможная потеря данных, используйте метод принудительного удаления для быстрого удаления хранилища и всех его зависимостей.
> Команда PowerShell удаляет все содержимое хранилища и не является **обратимой.**

Используйте приведенные ниже команды, чтобы удалить хранилище Site Recovery, даже если оно содержит защищенные элементы.

    Connect-AzAccount

    Select-AzSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzRecoveryServicesVault -Name "vaultname"

    Remove-AzRecoveryServicesVault -Vault $vault

Узнайте больше о [Get-AzRecoveryRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)и [Удалить-AzRecoveryRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault).
