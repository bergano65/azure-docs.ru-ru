---
title: Перемещение ресурсов, связанных с конфигурацией обслуживания, в другой регион
description: Узнайте, как переместить ресурсы, связанные с конфигурацией обслуживания VM, в другой регион Azure
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 3e271e2467b495e79a93ce5eab5edee36e65e619
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304450"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>Перемещение ресурсов в конфигурации управления обслуживанием в другой регион

Следуйте этой статье, чтобы переместить ресурсы, связанные с конфигурацией управления обслуживанием, в другой регион Azure. Возможно, необходимо переместить конфигурацию по ряду причин. Например, использовать преимущества нового региона, развертывать функции или службы, доступные в конкретном регионе, удовлетворять требованиям внутренней политики и управления или в ответ на планирование потенциала.

Управление обслуживанием с настраиваемыми конфигурациями обслуживания позволяет контролировать, как обновления платформприменяюти применяются к [Windows](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) и [Linux](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) VMs, а также к выделенным хостам Azure. Существует несколько сценариев перемещения управления обслуживанием в разных регионах:

- Для перемещения ресурсов, связанных с конфигурацией обслуживания, но не самой конфигурации, следуйте этой статье.
- Чтобы переместить конфигурацию управления обслуживанием, но не ресурсы, связанные с конфигурацией, следуйте [этим инструкциям.](move-region-maintenance-configuration.md)
- Чтобы переместить конфигурацию обслуживания и связанные с ней ресурсы, сначала следуйте [этим инструкциям.](move-region-maintenance-configuration.md) Затем следуйте инструкциям в этой статье.

## <a name="prerequisites"></a>Предварительные требования

Перед началом перемещения ресурсов, связанных с конфигурацией управления обслуживанием:

- Убедитесь, что ресурсы, которые вы перемещаете, существуют в новом регионе, прежде чем начать.
- Проверьте конфигурации управления обслуживанием, связанные с мизантами Azure и выделенными хостами Azure, которые вы хотите переместить. Проверьте каждый ресурс по отдельности. В настоящее время нет способа получения конфигураций для нескольких ресурсов.
- При извлечении конфигураций для ресурса:
    - Убедитесь, что идентификатор подписки для учетной записи, а не идентификатор выделенного хоста Azure.
    - CLI: Параметр таблицы вывода используется только для читаемости и может быть удален или изменен.
    - PowerShell: Параметр названия формата-таблицы используется только для читаемости и может быть удален или изменен.
    - Если вы используете PowerShell, вы получите ошибку, если вы попытаетесь перечислить конфигурации для ресурса, который не имеет каких-либо связанных конфигураций. Ошибка будет аналогична: "Операция не удалось со статусом: 'Не найдено'. Подробности: 404 Ошибка клиента: не найдено для URL".

    
## <a name="prepare-to-move"></a>Подготовка к переезду

1. Перед тем, как начать, определите эти переменные. Мы предоставили пример для каждого из них.

    **Переменной** | **Подробно** | **Пример**
    --- | ---
    $subId | Идентификатор для подписки, содержащий конфигурации обслуживания | "наш-подписка-ID"
    $rsrcGroupName | Имя группы ресурсов (Azure VM) | "ВМРесурсГрупп"
    $vmName | Имя ресурса VM |  "myVM"
    $adhRsrcGroupName |  Группа ресурсов (Выделенные хосты) | "HostResourceGroup"
    $adh | Специальное имя хозяина | "мойхозяин"
    $adhParentName | Имя родительского ресурса | "ХостГрупп"
    
2. Для получения конфигураций обслуживания с помощью команды PowerShell [Get-AzConfigurationAssignment:](https://docs.microsoft.com/powershell/module/az.maintenance/Get-AzConfigurationAssignment?view=azps-3.5.0)

    - Для выделенных узлов Azure запустите:
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - Для Пазуры ВМ, запуск:

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. Для получения конфигураций обслуживания с помощью команды [назначения задания обслуживания](https://docs.microsoft.com/cli/azure/ext/maintenance/maintenance/assignment?view=azure-cli-latest) CLI az:

    - Для выделенных узлов Azure:

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - Для Вымотка Azure:

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>Переместить 

1. [Следуйте этим инструкциям,](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) чтобы переместить ВМ Azure в новый регион.
2. После перемещения ресурсов повторно ездое количество конфигураций обслуживания в новом регионе по мере необходимости применяется в зависимости от того, перемещаются ли конфигурации обслуживания. Конфигурацию обслуживания можно применить к ресурсу с помощью [PowerShell](../virtual-machines/maintenance-control-powershell.md) или [CLI.](../virtual-machines/maintenance-control-cli.md)


## <a name="verify-the-move"></a>Проверить ход

Проверка ресурсов в новом регионе и проверка связанных конфигураций для ресурсов в новом регионе. 

## <a name="clean-up-source-resources"></a>Очистка исходных ресурсов

После переезда рассмотрите возможность удалять перемещенные ресурсы в исходной области.


## <a name="next-steps"></a>Дальнейшие действия

Следуйте [этим инструкциям,](move-region-maintenance-configuration.md) если вам нужно переместить конфигурации обслуживания. 
