---
ms.openlocfilehash: 26eff7b10542843be561264c316bdcf1a63271f7
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56751930"
---
## <a name="setting-up-the-library"></a>Настройка библиотеки

Вызовите Start(), чтобы включить сеанс для среды обработки данных.

Чтобы обрабатывать события, вызванные сеанс, установите `delegate` свойство сеанса к объекту, такие как представление. Этот объект необходимо реализовать протокол SSCCloudSpatialAnchorSessionDelegate.
