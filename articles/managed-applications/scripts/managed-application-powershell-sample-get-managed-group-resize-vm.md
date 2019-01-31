---
title: Пример скрипта Azure PowerShell. Получение ресурсов из управляемой группы ресурсов и изменение размера виртуальных машин | Документация Майкрософт
description: Пример скрипта Azure PowerShell. Получение ресурсов из управляемой группы ресурсов и изменение размера виртуальных машин
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: baad12379c01ac955f1b0e036398e5889f3681b2
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55203204"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-powershell"></a>Получение ресурсов из управляемой группы ресурсов и изменение размера виртуальных машин с помощью Azure PowerShell

С помощью этого скрипта можно получить ресурсы из управляемой группы ресурсов и изменить размер виртуальных машин в этой группе.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/managed-applications/get-application/get-application.ps1 "Get application")]


## <a name="script-explanation"></a>Описание скрипта

В этом скрипте используются следующие команды для развертывания управляемого приложения. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [Get-AzureRmManagedApplication](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermmanagedapplication) | Перечисление управляемых приложений Azure. Укажите имя группы ресурсов, чтобы отфильтровать результаты. |
| [Get-AzureRmResource](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermresource) | Перечисление ресурсов. Укажите группу и тип ресурсов, чтобы отфильтровать результаты. |
| [Update-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvm) | Обновление размера виртуальной машины. |


## <a name="next-steps"></a>Дополнительная информация

* Общие сведения об управляемых приложениях Azure см. в [этой статье](../overview.md).
* Дополнительные сведения см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
