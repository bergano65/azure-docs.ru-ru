---
title: Совместное использование образов коллекций в клиентах в Azure
description: Узнайте, как совместно использовать образы виртуальных машин в клиентах Azure с помощью общих коллекций образов.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: a29999102ad8a10d8965145b31a7d804675e0e57
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76276338"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Совместное использование образов виртуальных машин коллекции в клиентах Azure

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>Создание масштабируемого набора с помощью Azure CLI

Войдите в субъект-службу для клиента 1, используя appID, ключ приложения и идентификатор клиента 1. При необходимости можно `az account show --query "tenantId"` использовать для получения идентификаторов клиентов.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Войдите в субъект-службу для клиента 2, используя appID, ключ приложения и идентификатор клиента 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Создайте масштабируемый набор. Замените сведения в примере собственными.

```azurecli-interactive
az vmss create \
  -g myResourceGroup \
  -n myScaleSet \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Дальнейшие действия

Если вы столкнетесь с проблемами, обратитесь к статье об [устранении неполадок с коллекциями общих образов](troubleshooting-shared-images.md).
