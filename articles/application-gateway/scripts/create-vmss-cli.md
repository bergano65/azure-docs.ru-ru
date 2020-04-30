---
title: Пример скрипта Azure CLI. Управление веб-трафиком | Документация Майкрософт
description: Пример скрипта Azure CLI. Управление веб-трафиком с помощью шлюза приложений и масштабируемого набора виртуальных машин.
services: application-gateway
documentationcenter: networking
author: vhorne
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 6251ab1484c7243bf0b22c7c4faf8b560ef0abb6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81457813"
---
# <a name="manage-web-traffic-using-the-azure-cli"></a>Управление веб-трафиком с помощью Azure CLI

С помощью этого скрипта создается шлюз приложений, в котором используется масштабируемый набор виртуальных машин для внутренних серверов. Затем шлюз приложений можно настроить для управления веб-трафиком. Выполнив скрипт, вы можете проверить шлюз приложений с помощью его общедоступного IP-адреса.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/application-gateway/create-vmss/create-vmss.sh "Create application gateway")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните следующую команду, чтобы удалить группу ресурсов, шлюз приложений и все связанные ресурсы.

```azurecli-interactive 
az group delete --name myResourceGroupAG --yes
```

## <a name="script-explanation"></a>Описание скрипта

Чтобы создать развертывание, скрипт использует следующие команды. Для каждого элемента в таблице приведены ссылки на документацию по команде.

| Get-Help | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | Создает виртуальную сеть. |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) | Создает подсеть в виртуальной сети. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest) | Создает общедоступный IP-адрес для шлюза приложений. |
| [az network application-gateway create](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest) | Создание шлюза приложений. |
| [az vmss create](https://docs.microsoft.com/cli/azure/vmss) | Создает масштабируемый набор виртуальных машин. |
| [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip) | Получает общедоступный IP-адрес для шлюза приложений. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов CLI шлюза приложений для виртуальных машин см. в [документации по виртуальным машинам Azure под управлением Windows](../cli-samples.md).
