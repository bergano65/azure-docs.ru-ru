---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: d567a4f7d9b9429887d6396cb2b03dfc34c6ac93
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88602279"
---
<!-- Create a resource group -->

Используйте следующую команду, чтобы создать группу ресурсов. Выберите географический регион, который будет использоваться для хранения записей мультимедиа и метаданных для учетной записи служб мультимедиа. Этот регион будет использоваться для обработки и потоковой передачи мультимедиа.

```azurecli
az group create --name amsResourceGroup --location westus2
```
