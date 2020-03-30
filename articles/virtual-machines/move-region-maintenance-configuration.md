---
title: Переместите конфигурацию обслуживания в другой регион Azure
description: Узнайте, как переместить конфигурацию обслуживания VM в другой регион Azure
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: fe03bead238d3fb7bda3ee685bd5587c3e0dbc58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304463"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>Перемещение конфигурации управления обслуживанием в другой регион

Следуйте этой статье, чтобы переместить конфигурацию управления обслуживанием в другой регион Azure. Возможно, необходимо переместить конфигурацию по ряду причин. Например, использовать преимущества нового региона, развертывать функции или службы, доступные в конкретном регионе, удовлетворять требованиям внутренней политики и управления или в ответ на планирование потенциала.

Управление обслуживанием с настраиваемыми конфигурациями обслуживания позволяет контролировать, как обновления платформприменяюти применяются к [Windows](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) и [Linux](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) VMs, а также к выделенным хостам Azure. Существует несколько сценариев перемещения управления обслуживанием в разных регионах:

- Чтобы переместить конфигурацию управления обслуживанием, но не ресурсы, связанные с конфигурацией, следуйте инструкциям в этой статье.
- Для перемещения ресурсов, связанных с конфигурацией обслуживания, но не самой [конфигурации, следуйте этим инструкциям.](move-region-maintenance-configuration-resources.md)
- Чтобы переместить конфигурацию обслуживания и связанные с ней ресурсы, сначала следуйте инструкциям в этой статье. Затем следуйте [этим инструкциям.](move-region-maintenance-configuration-resources.md)

## <a name="prerequisites"></a>Предварительные требования

Перед тем, как приступить к перемещению конфигурации управления обслуживанием:

- Конфигурации обслуживания связаны с VMs-ми зайчиками Azure или выделенными хостами Azure. Убедитесь, что ресурсы VM/host существуют в новом регионе перед началом.
- Identify: 
    - Существующие конфигурации управления обслуживанием.
    - Группы ресурсов, в которых в настоящее время находятся существующие конфигурации. 
    - Группы ресурсов, к которым конфигурации будут добавлены после переезда в новый регион. 
    - Ресурсы, связанные с конфигурацией обслуживания, которые необходимо переместить.
    - Убедитесь, что ресурсы в новом регионе такие же, как и ресурсы, связанные с текущими конфигурациями обслуживания. Конфигурации могут иметь те же имена в новом регионе, что и в старом, но это не требуется.

## <a name="prepare-and-move"></a>Подготовка и перемещение 

1. Извлеките все конфигурации обслуживания в каждой подписке. Для этого запустите команду [списка конфигураций обслуживания](https://docs.microsoft.com/cli/azure/ext/maintenance/maintenance/configuration?view=azure-cli-latest#ext-maintenance-az-maintenance-configuration-list) CLI az, заменив $subId идентификатором подписки.

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. Просмотрите список возвращенных таблиц записей конфигурации в подписке. Ниже приведен пример. Список будет содержать значения для вашей конкретной среды.

    **Название** | **Расположение** | **Группа ресурсов**
    --- | --- | ---
    Пропустить техническое обслуживание | eastus2 | конфигурация-ресурсная группа
    IgniteDemoConfig | eastus2 | конфигурация-ресурсная группа
    по умолчаниюОбслуживаниеКонфигурация-восток | eastus | тест-конфигурация
    

3. Сохраните список для справки. При перемещении конфигураций, это поможет вам проверить, что все было перемещено.
4. В качестве ссылки накарту каждой группы конфигурации/ресурсов с новой группой ресурсов в новом регионе.
5. Создавайте новые конфигурации обслуживания в новом регионе с помощью [PowerShell](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration)или [CLI.](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration)
6. Связать конфигурации с ресурсами в новом регионе, используя [PowerShell](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration), или [CLI](../virtual-machines/maintenance-control-cli.md#assign-the-configuration).


## <a name="verify-the-move"></a>Проверить ход

После перемещения конфигураций сравните конфигурации и ресурсы в новом регионе со подготовленным списком таблиц.


## <a name="clean-up-source-resources"></a>Очистка исходных ресурсов

После переезда рассмотрите возможность удалять перемещенные конфигурации обслуживания в исходной области, [PowerShell](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration)или [CLI.](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration)


## <a name="next-steps"></a>Дальнейшие действия

Следуйте [этим инструкциям,](move-region-maintenance-configuration-resources.md) если вам нужно переместить ресурсы, связанные с конфигурациями обслуживания. 
