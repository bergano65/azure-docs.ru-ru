---
title: Совместное использование образов из коллекции клиентов в Azure | Документация Майкрософт
description: Узнайте, как совместно использовать образы виртуальных Машин в Azure клиентов, использующих общий галерей изображений.
services: virtual-machines-linux
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: 88596a4bda3c9ebae99403e95b3539f068cad665
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465476"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Совместное использование коллекции образов виртуальных Машин в Azure клиентов

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> Чтобы развернуть виртуальную Машину из образа в другом клиенте azure нельзя использовать портал. Чтобы создать виртуальную Машину из образа совместно несколькими клиентами, необходимо использовать Azure CLI или [Powershell](../windows/share-images-across-tenants.md).

## <a name="create-a-vm-using-azure-cli"></a>Создание виртуальной Машины с помощью Azure CLI

Войдите в субъекта-службы для клиента 1, используя идентификатор приложения, ключ приложения и идентификатор клиента 1. Можно использовать `az account show --query "tenantId"` получение идентификаторов клиента, при необходимости.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Для входа субъекта-службы для клиента 2, используя идентификатор приложения, ключ приложения и идентификатор клиента 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Создайте виртуальную машину. Замените сведения в примере на собственные.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Дальнейшие действия

Если вы столкнетесь с проблемами, обратитесь к статье об [устранении неполадок с коллекциями общих образов](troubleshooting-shared-images.md).