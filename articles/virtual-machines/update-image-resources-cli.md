---
title: Перечисление, обновление и удаление ресурсов изображений с помощью Azure CLI
description: Перечисление, обновление и удаление ресурсов изображений в общей коллекции образов с помощью Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6099afc82e76ed28e8557ac0eee3e64cb292a715
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98882014"
---
# <a name="list-update-and-delete-image-resources"></a>Перечисление, обновление и удаление ресурсов изображений 

Вы можете управлять ресурсами коллекции общих образов с помощью Azure CLI.

[!INCLUDE [virtual-machines-common-gallery-list-cli.md](../../includes/virtual-machines-common-gallery-list-cli.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-cli](../../includes/virtual-machines-common-shared-images-update-delete-cli.md)]

## <a name="next-steps"></a>Дальнейшие действия

С помощью [построителя образов Azure (Предварительная версия)](./image-builder-overview.md) можно автоматизировать создание версий изображений, а также использовать его для обновления и [создания новой версии образа из существующей версии образа](./linux/image-builder-gallery-update-image-version.md).