---
title: "PowerShell: Включить BYOK TDE - Azure S'L База данных Управляемый экземпляр "
description: Узнайте, как настроить для Управляемого экземпляра Базы данных SQL BYOK TDE для прозрачного шифрования неактивных данных с помощью PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 11/05/2019
ms.openlocfilehash: ddffda5229c9c0d33c563e3ae7b4a884f0f92dff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73691408"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Управление прозрачным шифрованием данных в Управляемом экземпляре с использованием собственного ключа из Azure Key Vault

Этот пример сценария PowerShell настраивает transparent Data Encryption (TDE) с управляемым клиентом ключом для управляемого экземпляра Azure S'L с использованием ключа из Хранилища ключей Azure. Это часто называют сценарием Bring Your Own Key для TDE. Чтобы узнать больше о TDE с [TDE Bring Your Own Key to Azure SQL](../transparent-data-encryption-byok-azure-sql.md)помощью ключа, управляемого клиентами, см.

## <a name="prerequisites"></a>Предварительные требования

- Существующая Управляемая инстанция. [См. Используйте PowerShell для создания управляемого экземпляра базы данных Azure S'L.](sql-database-create-configure-managed-instance-powershell.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Для использования как PowerShell локально, так и с помощью Azure Cloud Shell требуется АЗ PowerShell 2.3.2 или более поздняя версия. Если вам нужно обновить, смотрите [модуль Install Azure PowerShell](/powershell/azure/install-az-ps)или запустите нижепримерный сценарий для установки модуля для текущего пользователя:

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzAccount`, чтобы создать подключение к Azure.

## <a name="sample-scripts"></a>Примеры сценариев

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев PowerShell для базы данных SQL Azure можно найти [здесь](../sql-database-powershell-samples.md).
