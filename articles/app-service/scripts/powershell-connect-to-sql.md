---
title: Пример сценария Azure PowerShell. Подключение приложения к базе данных SQL | Документация Майкрософт
description: Пример сценария Azure PowerShell для подключения приложения Службы приложений к базе данных SQL
services: app-service\web
documentationcenter: ''
author: syntaxc4
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 055440a9-fff1-49b2-b964-9c95b364e533
ms.service: app-service
ms.devlang: multiple
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: fc0046f16222fe20a7b11901690acccaae382a6c
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/20/2018
ms.locfileid: "53650126"
---
# <a name="connect-an-app-service-app-to-a-sql-database"></a>Подключение приложения Службы приложений к базе данных SQL

В этой статье вы узнаете, как создать базу данных SQL Azure и приложение Службы приложений. Затем вы свяжете базу данных SQL с приложением, используя параметры приложения.

При необходимости установите Azure PowerShell с помощью инструкции, приведенной в [руководстве Azure PowerShell](/powershell/azure/overview), а затем выполните команду `Connect-AzureRmAccount`, чтобы создать подключение к Azure.

## <a name="sample-script"></a>Пример скрипта

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/connect-to-sql/connect-to-sql.ps1?highlight=13 "Connect an app to a SQL database")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполнив пример сценария, вы можете удалить группу ресурсов, приложение Службы приложений и все связанные ресурсы с помощью следующей команды.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [New-AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | Создает план службы приложений. |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Создает приложение Службы приложений. |
| [New-AzureRMSQLServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | Создает сервер базы данных SQL. |
| [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) | Создает правило брандмауэра для сервера базы данных SQL. |
| [New-AzureRMSQLDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) | Создает базу данных или эластичную базу данных. |
| [Set-AzureRmWebApp](/powershell/module/azurerm.websites/set-azurermwebapp) | Изменяет конфигурацию приложения Службы приложений. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев Azure PowerShell для Службы приложений Azure доступны [здесь](../samples-powershell.md).
