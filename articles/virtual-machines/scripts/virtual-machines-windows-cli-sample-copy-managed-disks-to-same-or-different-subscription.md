---
title: Пример сценария Azure CLI. Копирование (перемещение) управляемых дисков в ту же или другую подписку | Microsoft Docs
description: Пример сценария Azure CLI. Копирование (перемещение) управляемых дисков в ту же или другую подписку
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: abb051e9646d547907384ed06413439845a29d5e
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57249677"
---
# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>Копирование управляемых дисков в ту же или другую подписку с помощью интерфейса командной строки

Этот сценарий копирует управляемый диск в ту же или другую подписку в одном регионе. Копирование работает только в том случае, если подписки входят в состав одного клиента AAD.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "Copy managed disk")]

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует приведенные ниже команды для создания управляемого диска в целевой подписке с помощью идентификатора исходного управляемого диска. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az disk show](https://docs.microsoft.com/cli/azure/disk) | Получает все свойства управляемого диска, используя имя и свойства группы ресурсов управляемого диска. Свойство идентификатора используется для копирования управляемого диска в другую подписку.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | Копирует управляемый диск путем создания нового управляемого диска в другой подписке, используя идентификатор и имя родительского управляемого диска.  |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).

Дополнительные примеры сценариев интерфейса командной строки для виртуальных машин и управляемых дисков см. в [документации по виртуальным машинам Azure под управлением Windows](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
