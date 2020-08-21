---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: a1ed4a9e123459a697f0894ff05430c63988a70b
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585425"
---
<!--Create a media services asset cLI-->

Указанная ниже команда Azure CLI создает ресурс Служб мультимедиа. Замените значения `assetName`, `amsAccountName` и `resourceGroup` значениями, с которыми вы работаете в данный момент.

```azurecli
az ams asset create -n assetName -a amsAccountName -g resourceGroup
```