---
title: 'PowerShell: Включение BYOK TDE для Управляемого экземпляра Базы данных SQL Azure | Документация Майкрософт'
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
ms.date: 04/19/2019
ms.openlocfilehash: d8f0f4a8e603a9040d166b00682077cff23abd8d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569692"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault-preview"></a>Управление прозрачное шифрование данных в Управляемый экземпляр с помощью собственного ключа из Azure Key Vault (Предварительная версия)

Этот пример сценария PowerShell настраивает прозрачное шифрование данных (TDE) в сценарии создание собственных ключей (Предварительная версия) для Управляемый экземпляр Azure SQL с помощью ключа из Azure Key Vault. См. дополнительные сведения об [использовании TDE с поддержкой создания собственных ключей (BYOK) для Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites"></a>Предварительные требования

- Существующий Управляемый экземпляр. См. раздел [Использование PowerShell для создания управляемого экземпляра базы данных SQL Azure](sql-database-create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Для локального использования PowerShell или использования Azure Cloud Shell требуется AZ PowerShell 1.1.1-Preview или более поздняя версия. Если необходимо выполнить обновление, обратитесь к разделу [установка Azure PowerShell Module](/powershell/azure/install-az-ps)или выполните приведенный ниже пример скрипта, чтобы установить модуль.

`Install-Module -Name Az.Sql -RequiredVersion 1.1.1-preview -AllowPrerelease -Force`

Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzAccount`, чтобы создать подключение к Azure.

## <a name="sample-scripts"></a>Примеры сценариев

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев PowerShell для базы данных SQL Azure можно найти [здесь](../sql-database-powershell-samples.md).
