---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/23/2019
ms.author: rgarcia
ms.openlocfilehash: 9a02dfbf3f2976489c10ccfeb935915a65d8dc84
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "71180071"
---
Когда вы создадите наблюдатель, для каждой запрошенной привязки будет срабатывать событие `AnchorLocated`. Это событие срабатывает, если привязка обнаружена или если ее не удалось найти. В последнем случае причина будет указана в области состояния. Когда для наблюдателя будут обработаны, найдены или не найдены все привязки, сработает событие `LocateAnchorsCompleted`. Максимальное количество идентификаторов на наблюдателя — 35. 
