---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 5360cbb7bdfbdc59e87366e73a891b5c2583672e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993083"
---
Когда вы создадите наблюдатель, для каждой запрошенной привязки будет срабатывать событие `AnchorLocated`. Это событие срабатывает, если привязка обнаружена или если ее не удалось найти. В последнем случае причина будет указана в области состояния. Когда для наблюдателя будут обработаны, найдены или не найдены все привязки, сработает событие `LocateAnchorsCompleted`. Максимальное количество идентификаторов на наблюдателя — 35. 
