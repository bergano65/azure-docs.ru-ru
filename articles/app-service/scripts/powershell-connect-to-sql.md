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
ms.openlocfilehash: b008ef47ba530affd65e4d0e5e293312cfe74b69
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "66136528"
---
# <a name="connect-an-app-service-app-to-a-sql-database"></a>Подключение приложения Службы приложений к базе данных SQL

В этой статье вы узнаете, как создать базу данных SQL Azure и приложение Службы приложений. Затем вы свяжете базу данных SQL с приложением, используя параметры приложения.

При необходимости установите Azure PowerShell с помощью инструкции, приведенной в [руководстве Azure PowerShell](/powershell/azure/overview), а затем выполните команду `Connect-AzAccount`, чтобы создать подключение к Azure.

## <a name="sample-script"></a>Пример скрипта

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/connect-to-sql/connect-to-sql.ps1?highlight=13 "Connect an app to a SQL database")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполнив пример сценария, вы можете удалить группу ресурсов, приложение Службы приложений и все связанные ресурсы с помощью следующей команды.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Создает план службы приложений. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Создает приложение Службы приложений. |
| [New-AzSQLServer](/powershell/module/az.sql/new-azsqlserver) | Создает сервер базы данных SQL. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Создает правило брандмауэра для сервера Базы данных SQL. |
| [New-AzSQLDatabase](/powershell/module/az.sql/new-azsqldatabase) | Создает базу данных или эластичную базу данных. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Изменяет конфигурацию приложения Службы приложений. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев Azure PowerShell для Службы приложений Azure доступны [здесь](../samples-powershell.md).
