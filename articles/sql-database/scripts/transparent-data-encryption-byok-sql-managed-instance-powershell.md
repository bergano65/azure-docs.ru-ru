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
manager: craigg
ms.date: 04/19/2019
ms.openlocfilehash: c2c4bd7bffd923430d0817cb6ea975f4c1596623
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66729169"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault-preview"></a>Управление прозрачным шифрованием данных в управляемом экземпляре, с использованием собственного ключа из хранилища ключей Azure (Предварительная версия)

Этот пример сценария PowerShell настраивает прозрачное шифрование данных (TDE) в сценарии создания собственных ключей (Предварительная версия) для управляемого экземпляра SQL Azure, с помощью ключа из хранилища ключей Azure. См. дополнительные сведения об [использовании TDE с поддержкой создания собственных ключей (BYOK) для Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites"></a>Технические условия

- Существующий управляемый экземпляр. См. в разделе [использование PowerShell для создания базы данных SQL Azure управляемый экземпляр](sql-database-create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Локально с помощью обоих PowerShell или Azure Cloud Shell требуется AZ PowerShell 1.1.1-preview или более поздней версии предварительной версии. Если необходимо выполнить обновление, см. в разделе [установке модуля Azure PowerShell](/powershell/azure/install-az-ps), или запустите ниже пример сценария для установки модуля.

`Install-Module -Name Az.Sql -RequiredVersion 1.1.1-preview -AllowPrerelease -Force`

Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzAccount`, чтобы создать подключение к Azure.

## <a name="sample-scripts"></a>Примеры скриптов

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев PowerShell для базы данных SQL Azure можно найти [здесь](../sql-database-powershell-samples.md).
