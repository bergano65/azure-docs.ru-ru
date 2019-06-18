---
ms.openlocfilehash: 26eff7b10542843be561264c316bdcf1a63271f7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66110719"
---
## <a name="setting-up-the-library"></a>Настройка библиотеки

Вызовите Start(), чтобы включить сеанс для среды обработки данных.

Чтобы обрабатывать события, вызванные сеанс, установите `delegate` свойство сеанса к объекту, такие как представление. Этот объект необходимо реализовать протокол SSCCloudSpatialAnchorSessionDelegate.
