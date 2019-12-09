---
title: Развертывание среды выполнения интеграции Azure SSIS с помощью PowerShell
description: Этот скрипт PowerShell создает среду выполнения интеграции Azure SSIS, которая может запускать пакеты SSIS в облаке.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.author: sawinark
author: swinarko
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 09/12/2017
ms.openlocfilehash: c6d9a9299fa25013a440ee6ac45f5eae407225b8
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929822"
---
# <a name="powershell-script---deploy-azure-ssis-integration-runtime"></a>Скрипт PowerShell. Развертывание среды выполнения интеграции Azure SSIS

В этом примере сценария PowerShell создается среда выполнения интеграции Azure SSIS, которая может запускать пакеты SSIS в Azure.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/data-factory/deploy-azure-ssis-integration-runtime/deploy-azure-ssis-integration-runtime.ps1 "Deploy Azure-SSIS Integration Runtime")]

## <a name="clean-up-deployment"></a>Очистка развертывания

После выполнения примера скрипта можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Чтобы удалить фабрику данных из группы ресурсов, выполните следующую команду: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды:

| Команда | Заметки |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Создали фабрику данных. |
| [Set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) | Создает среду выполнения интеграции Azure SSIS, которая может запускать пакеты SSIS в облаке. |
| [Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) | Запускает среду выполнения интеграции Azure SSIS. |
| [Get-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/get-Azdatafactoryv2integrationruntime) | Получает сведения о среде выполнения интеграции Azure SSIS. |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов со всеми вложенными ресурсами. |
|||

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры сценариев PowerShell для Azure Data Factory приведены [здесь](../samples-powershell.md).
