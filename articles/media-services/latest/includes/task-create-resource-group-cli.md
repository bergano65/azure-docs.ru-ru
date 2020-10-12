---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: d567a4f7d9b9429887d6396cb2b03dfc34c6ac93
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88602279"
---
<!-- Create a resource group -->

Используйте следующую команду, чтобы создать группу ресурсов. Выберите географический регион, который будет использоваться для хранения записей мультимедиа и метаданных для учетной записи служб мультимедиа. Этот регион будет использоваться для обработки и потоковой передачи мультимедиа.

```azurecli
az group create --name amsResourceGroup --location westus2
```
