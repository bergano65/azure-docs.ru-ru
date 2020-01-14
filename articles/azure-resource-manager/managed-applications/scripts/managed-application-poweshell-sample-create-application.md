---
title: Пример скрипта Azure PowerShell. Развертывание управляемого приложения
description: Здесь представлен пример скрипта Azure PowerShell, с помощью которого можно развернуть определение управляемого приложения в подписке.
author: tfitzmac
ms.devlang: powershell
ms.topic: sample
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: e076128a5be453028635b1657bf3f90980863148
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75648941"
---
# <a name="deploy-a-managed-application-for-a-service-catalog-with-powershell"></a>Развертывание управляемого приложения для каталога служб с помощью Azure PowerShell

С помощью этого скрипта определение управляемого приложения развертывается из каталога служб.


[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../../powershell_scripts/managed-applications/create-application/create-application.ps1 "Create application")]


## <a name="script-explanation"></a>Описание скрипта

В этом скрипте используется следующая команда для развертывания управляемого приложения. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [New-AzManagedApplication](https://docs.microsoft.com/powershell/module/az.resources/new-azmanagedapplication) | Позволяет создать управляемое приложение. Укажите идентификатор определения и параметры для шаблона. |


## <a name="next-steps"></a>Дальнейшие действия

* Общие сведения об управляемых приложениях Azure см. в [этой статье](../overview.md).
* Дополнительные сведения см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
