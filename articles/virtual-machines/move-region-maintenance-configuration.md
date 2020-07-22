---
title: Перемещение конфигурации обслуживания в другой регион Azure
description: Узнайте, как переместить конфигурацию обслуживания виртуальной машины в другой регион Azure.
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 4cff7eb4a69005f2e74747b6e58447f100c69b60
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86501608"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>Перемещение конфигурации управления обслуживанием в другой регион

Выполните инструкции из этой статьи, чтобы переместить конфигурацию управления обслуживанием в другой регион Azure. Может потребоваться переместить конфигурацию по ряду причин. Например, чтобы воспользоваться преимуществами нового региона, можно развернуть компоненты или службы, доступные в определенном регионе, в соответствии с требованиями к внутренней политике и управлению, а также в ответ на планирование ресурсов.

Управление обслуживанием с пользовательскими конфигурациями обслуживания позволяет управлять применением обновлений платформы к виртуальным машинам [Windows](./maintenance-control-cli.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) и [Linux](./maintenance-control-cli.md?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) , а также к выделенным узлам Azure. Существует несколько сценариев перемещения управления обслуживанием в разных регионах:

- Чтобы переместить конфигурацию управления обслуживанием, но не ресурсы, связанные с конфигурацией, следуйте инструкциям в этой статье.
- Чтобы переместить ресурсы, связанные с конфигурацией обслуживания, но не самой конфигурацией, выполните следующие [инструкции](move-region-maintenance-configuration-resources.md).
- Чтобы переместить конфигурацию обслуживания и связанные с ней ресурсы, сначала следуйте инструкциям в этой статье. Затем выполните [эти инструкции](move-region-maintenance-configuration-resources.md).

## <a name="prerequisites"></a>Предварительные требования

Перед началом перемещения конфигурации управления обслуживанием выполните следующие действия.

- Конфигурации обслуживания связаны с виртуальными машинами Azure или выделенными узлами Azure. Прежде чем начать, убедитесь, что ресурсы виртуальной машины и узла существуют в новом регионе.
- Identify: 
    - Существующие конфигурации управления обслуживанием.
    - Группы ресурсов, в которых в настоящее время находятся существующие конфигурации. 
    - Группы ресурсов, к которым будут добавляться конфигурации после перемещения в новый регион. 
    - Ресурсы, связанные с конфигурацией обслуживания, которую необходимо переместить.
    - Убедитесь, что ресурсы в новом регионе совпадают с ресурсами, связанными с текущими конфигурациями обслуживания. Конфигурации могут иметь те же имена в новом регионе, что и в старом, но это не обязательно.

## <a name="prepare-and-move"></a>Подготовка и перемещение 

1. Извлеките все конфигурации обслуживания в каждой подписке. Выполните команду CLI [AZ Maintenance Configuration List](/cli/azure/ext/maintenance/maintenance/configuration?view=azure-cli-latest#ext-maintenance-az-maintenance-configuration-list) , чтобы сделать это, заменив $SUBID на идентификатор подписки.

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. Проверьте список возвращенных таблиц записей конфигурации в подписке. Пример приведен ниже. Список будет содержать значения для конкретной среды.

    **имя**; | **Расположение** | **Группа ресурсов**
    --- | --- | ---
    Пропустить обслуживание | eastus2 | Настройка-группа ресурсов
    игнитедемоконфиг | eastus2 | Настройка-группа ресурсов
    Дефаултмаинтенанцеконфигуратион — eastus | eastus | конфигурация тестирования
    

3. Сохраните список для справки. При перемещении конфигураций можно убедиться, что все перемещено.
4. В качестве ссылки Сопоставьте каждую конфигурацию или группу ресурсов с новой группой ресурсов в новом регионе.
5. Создайте новые конфигурации обслуживания в новом регионе с помощью [PowerShell](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration)или [CLI](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration).
6. Свяжите конфигурации с ресурсами в новом регионе с помощью [PowerShell](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration)или [CLI](../virtual-machines/maintenance-control-cli.md#assign-the-configuration).


## <a name="verify-the-move"></a>Проверка перемещения

После перемещения конфигураций Сравните конфигурации и ресурсы в новом регионе с подготовленным списком таблиц.


## <a name="clean-up-source-resources"></a>Очистка исходных ресурсов

После перемещения рассмотрите возможность удаления перемещенных конфигураций обслуживания в исходном регионе, [PowerShell](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration)или [CLI](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration).


## <a name="next-steps"></a>Дальнейшие действия

Если необходимо переместить ресурсы, связанные с конфигурациями обслуживания, следуйте [этим инструкциям](move-region-maintenance-configuration-resources.md) . 
