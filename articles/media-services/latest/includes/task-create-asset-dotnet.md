---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: dotnet
ms.openlocfilehash: 820353ffbb5e23e27c3039ad27a8f6507b9d2167
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88608893"
---
<!--Create a media services asset REST-->

Следующая команда Azure .NET создает новый ресурс служб мультимедиа. Замените значения `subscriptionID` , `resourceGroup` и значениями, `amsAccountName` с которыми вы работаете в данный момент. Присвойте имя ресурсу, указав `assetName` здесь.

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```
