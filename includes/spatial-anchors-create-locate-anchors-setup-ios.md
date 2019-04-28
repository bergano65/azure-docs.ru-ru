---
ms.openlocfilehash: 26eff7b10542843be561264c316bdcf1a63271f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232676"
---
## <a name="setting-up-the-library"></a>Настройка библиотеки

Вызовите Start(), чтобы включить сеанс для среды обработки данных.

Чтобы обрабатывать события, вызванные сеанс, установите `delegate` свойство сеанса к объекту, такие как представление. Этот объект необходимо реализовать протокол SSCCloudSpatialAnchorSessionDelegate.
