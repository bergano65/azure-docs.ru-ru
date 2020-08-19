---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: c902ea001d18504dabffc8431acafe33822792e1
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88539150"
---
<!--Create a media services account -->

Указанная ниже команда Azure CLI создает новую учетную запись Служб мультимедиа. Можно заменить следующие значения: `amsaccount` `storageaccountforams` (должно соответствовать значению, указанному для учетной записи хранения) и `amsResourceGroup` (должно совпадать со значением, назначенным для группы ресурсов).  

```azurecli
az ams account create --name amsaccount \
   -g amsResourceGroup --storage-account storageaccountforams \
   -l westus2 
```