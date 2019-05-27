---
ms.openlocfilehash: 26eff7b10542843be561264c316bdcf1a63271f7
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110719"
---
## <a name="setting-up-the-library"></a>Настройка библиотеки

Вызовите Start(), чтобы включить сеанс для среды обработки данных.

Чтобы обрабатывать события, вызванные сеанс, установите `delegate` свойство сеанса к объекту, такие как представление. Этот объект необходимо реализовать протокол SSCCloudSpatialAnchorSessionDelegate.
