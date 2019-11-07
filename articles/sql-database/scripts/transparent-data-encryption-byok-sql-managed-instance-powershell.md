---
title: 'PowerShell: enable BYOK TDE-Управляемый экземпляр Базы данных SQL Azure '
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
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691408"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Управление прозрачным шифрованием данных в Управляемом экземпляре с использованием собственного ключа из Azure Key Vault

Этот пример сценария PowerShell настраивает прозрачное шифрование данных (TDE) с ключом, управляемым клиентом, для Управляемый экземпляр SQL Azure, используя ключ из Azure Key Vault. Часто это называется создание собственных ключейным сценарием для TDE. Дополнительные сведения о TDE с ключом, управляемым клиентом, см. в разделе [TDE создание собственных ключей to Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites"></a>Предварительные требования

- Существующий Управляемый экземпляр. См. раздел [Использование PowerShell для создания управляемого экземпляра базы данных SQL Azure](sql-database-create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Для локального использования PowerShell или использования Azure Cloud Shell требуется AZ PowerShell 2.3.2 или более поздней версии. Если необходимо выполнить обновление, обратитесь к разделу [установка Azure PowerShell Module](/powershell/azure/install-az-ps)или выполните приведенный ниже пример скрипта, чтобы установить модуль для текущего пользователя:

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzAccount`, чтобы создать подключение к Azure.

## <a name="sample-scripts"></a>Примеры сценариев

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев PowerShell для базы данных SQL Azure можно найти [здесь](../sql-database-powershell-samples.md).
