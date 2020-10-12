---
title: Рекомендации по использованию Key Vault-Azure Key Vault | Документация Майкрософт
description: Узнайте о рекомендациях по Azure Key Vault, включая управление доступом, использование отдельных хранилищ ключей, резервное копирование, ведение журнала и параметры восстановления.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: cec3ad4e113fd6ee3f4e30ad2a6877b886a958e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88189879"
---
# <a name="best-practices-to-use-key-vault"></a>Рекомендации по использованию Key Vault

## <a name="control-access-to-your-vault"></a>Управление доступом к хранилищу

Azure Key Vault — это облачная служба, которая обеспечивает защиту ключей шифрования и секретов (например, сертификатов, строк подключения и паролей). Так как это критически важные для бизнеса конфиденциальные данные, следует обеспечить защиту доступа к хранилищу ключей, чтобы доступ могли получить только авторизованные приложения и пользователи. В этой [статье](secure-your-key-vault.md) представлен обзор модели доступа Key Vault. Здесь приводится описание процессов аутентификации и авторизации, а также содержатся сведения о том, как защитить доступ к вашему хранилищу ключей.

Ниже приведены рекомендации по управлению доступом к хранилищу.
1. Блокировка доступа к подписке, группе ресурсов и хранилищам ключей (RBAC)
2. Создание политик доступа для каждого хранилища
3. Использование субъекта доступа с минимальными правами для предоставления доступа
4. Включить [конечные точки службы брандмауэра и виртуальной сети](overview-vnet-service-endpoints.md)

## <a name="use-separate-key-vault"></a>Использовать отдельные Key Vault

Мы рекомендуем использовать хранилище для каждого приложения в каждой среде (разработка, подготовка и Рабочая среда). Это позволяет не обмениваться секретами в разных средах, а также снизить угрозу в случае нарушения.

## <a name="backup"></a>Резервное копирование

Убедитесь, что вы регулярно создаете резервные копии хранилища на стороне обновления, удаления или создания объектов в хранилище.

### <a name="azure-powershell-backup-commands"></a>Azure PowerShell команды резервного копирования

* [Сертификат резервной копии](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultCertificate?view=azurermps-6.13.0)
* [Резервная копия ключа](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultKey?view=azurermps-6.13.0)
* [Секрет резервного копирования](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret?view=azurermps-6.13.0)

### <a name="azure-cli-backup-commands"></a>Azure CLI команды резервного копирования

* [Сертификат резервной копии](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-backup)
* [Резервная копия ключа](https://docs.microsoft.com/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-backup)
* [Секрет резервного копирования](https://docs.microsoft.com/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-backup)


## <a name="turn-on-logging"></a>Включить ведение журнала

[Включите ведение журнала](logging.md) для хранилища. Также Настройте оповещения.

## <a name="turn-on-recovery-options"></a>Включение параметров восстановления

1. Включите [обратимое удаление](soft-delete-overview.md).
2. Включите защиту от очистки, если хотите защититься от принудительного удаления секрета или хранилища даже после включения обратимого удаления.
