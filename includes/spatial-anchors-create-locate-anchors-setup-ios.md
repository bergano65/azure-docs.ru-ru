---
ms.openlocfilehash: 26eff7b10542843be561264c316bdcf1a63271f7
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184988"
---
## <a name="setting-up-the-library"></a>Настройка библиотеки

Вызовите Start(), чтобы включить сеанс для среды обработки данных.

Чтобы обрабатывать события, вызванные сеанс, установите `delegate` свойство сеанса к объекту, такие как представление. Этот объект необходимо реализовать протокол SSCCloudSpatialAnchorSessionDelegate.
