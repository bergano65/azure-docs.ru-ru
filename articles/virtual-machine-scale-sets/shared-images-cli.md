---
title: Использование образов общих виртуальных машин для создания масштабируемого набора в Azure CLI
description: Узнайте, как с помощью Azure CLI создать общие образы виртуальных машин для развертывания масштабируемых наборов виртуальных машин в Azure.
author: axayjo
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: c056db21eea2d87471c80ea7c4811eed418fcbd0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323470"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Создание и использование общих образов для масштабируемых наборов виртуальных машин с помощью Azure CLI 2.0

Создавая масштабируемый набор, вы указываете образ для использования при развертывании экземпляров виртуальных машин. [Коллекция общих образов](shared-image-galleries.md) упрощает обмен пользовательскими образами в вашей организации. Пользовательские образы похожи на образы магазина, однако их можно создавать самостоятельно. Пользовательские образы можно использовать для начальной загрузки конфигураций, например при предварительной загрузке приложений, конфигураций приложений и других конфигураций операционной системы. 

Коллекция общих образов позволяет обмениваться изображениями с другими пользователями. Выберите образы, к которым нужно предоставить общий доступ, регионы, где они будут доступны, и пользователей, которым будет доступно совместное использование. 


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>Дальнейшие шаги

Создание версии образа из [виртуальной машины](../virtual-machines/image-version-vm-cli.md)или [управляемого образа](../virtual-machines/image-version-managed-image-cli.md).

Дополнительные сведения о коллекциях общих образов см. в [обзорной статье](shared-image-galleries.md). Если вы столкнетесь с проблемами, обратитесь к статье об [устранении неполадок c коллекциями общих образов](../virtual-machines/troubleshooting-shared-images.md).
