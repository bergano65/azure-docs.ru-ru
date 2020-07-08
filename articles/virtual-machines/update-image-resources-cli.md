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
ms.openlocfilehash: 4126b7e0b48b455d9e5fcf65d6707f7e04de13ef
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82797101"
---
# <a name="list-update-and-delete-image-resources"></a>Перечисление, обновление и удаление ресурсов изображений 

Вы можете управлять ресурсами коллекции общих образов с помощью Azure CLI.

[!INCLUDE [virtual-machines-common-gallery-list-cli.md](../../includes/virtual-machines-common-gallery-list-cli.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-cli](../../includes/virtual-machines-common-shared-images-update-delete-cli.md)]

## <a name="next-steps"></a>Дальнейшие шаги

С помощью [построителя образов Azure (Предварительная версия)](./linux/image-builder-overview.md) можно автоматизировать создание версий изображений, а также использовать его для обновления и [создания новой версии образа из существующей версии образа](./linux/image-builder-gallery-update-image-version.md). 