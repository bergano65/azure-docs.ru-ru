---
title: Получение управляемой группы ресурсов и изменение размера виртуальных машин в Azure PowerShell
description: Здесь представлен пример скрипта Azure PowerShell, с помощью которого можно получить управляемую группу ресурсов для управляемого приложения Azure. Скрипт позволяет изменять размеры виртуальных машин.
author: tfitzmac
ms.devlang: powershell
ms.topic: sample
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: 695023f4700370cbe5e9b345f513e38d1cf1fc0c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75648961"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-powershell"></a>Получение ресурсов из управляемой группы ресурсов и изменение размера виртуальных машин с помощью Azure PowerShell

С помощью этого скрипта можно получить ресурсы из управляемой группы ресурсов и изменить размер виртуальных машин в этой группе.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../../powershell_scripts/managed-applications/get-application/get-application.ps1 "Get application")]


## <a name="script-explanation"></a>Описание скрипта

В этом скрипте используются следующие команды для развертывания управляемого приложения. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [Get-AzManagedApplication](https://docs.microsoft.com/powershell/module/az.resources/get-azmanagedapplication) | Перечисление управляемых приложений Azure. Укажите имя группы ресурсов, чтобы отфильтровать результаты. |
| [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) | Перечисление ресурсов. Укажите группу и тип ресурсов, чтобы отфильтровать результаты. |
| [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) | Обновление размера виртуальной машины. |


## <a name="next-steps"></a>Дальнейшие действия

* Общие сведения об управляемых приложениях Azure см. в [этой статье](../overview.md).
* Дополнительные сведения см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
