---
title: Совместное использование образов коллекций в клиентах в Azure
description: Узнайте, как совместно использовать образы виртуальных машин в клиентах Azure с помощью общих коллекций образов.
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.date: 04/05/2019
ms.reviewer: akjosh
ms.custom: akjosh
ms.openlocfilehash: 6dad6e161518ba1f6a4f6fccb21b0f2a53f8e3a4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87083537"
---
# <a name="share-gallery-vm-images-across-tenants-in-azure"></a>Совместное использование образов виртуальных машин коллекции для клиентов в Azure

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>Создание масштабируемого набора с помощью Azure CLI

Войдите в субъект-службу для клиента 1, используя appID, ключ приложения и идентификатор клиента 1. При необходимости можно использовать `az account show --query "tenantId"` для получения идентификаторов клиентов.

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
