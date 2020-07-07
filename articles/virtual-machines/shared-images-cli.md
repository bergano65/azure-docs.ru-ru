---
title: Создавайте общие галереи изображений с помощью Azure CLI
description: В этой статье вы узнаете, как с помощью Azure CLI создать общий образ виртуальной машины Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: d64b5f94dae7ad0213fa231b5603064ad3647da1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82796698"
---
# <a name="create-a-shared-image-gallery-with-the-azure-cli"></a>Создание коллекции общих образов с помощью Azure CLI

[Коллекция общих образов](./linux/shared-image-galleries.md) упрощает обмен пользовательскими образами в вашей организации. Пользовательские образы похожи на образы магазина, однако их можно создавать самостоятельно. Пользовательские образы можно использовать для начальной загрузки конфигураций, например при предварительной загрузке приложений, конфигураций приложений и других конфигураций операционной системы. 

Общая коллекция образов позволяет предоставить другим пользователям общий доступ к пользовательским образам. Выберите образы, к которым нужно предоставить общий доступ, регионы, где они будут доступны, и пользователей, которым будет доступно совместное использование. 

[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>Дальнейшие действия

Создание версии образа из [виртуальной машины](image-version-vm-cli.md)или [управляемого образа](image-version-managed-image-cli.md) с помощью Azure CLI.

Дополнительные сведения о коллекциях общих образов см. в [обзорной статье](./linux/shared-image-galleries.md). Если вы столкнетесь с проблемами, обратитесь к статье об [устранении неполадок c коллекциями общих образов](troubleshooting-shared-images.md).
