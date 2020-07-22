---
ms.openlocfilehash: e7e3a31663e6c166b912ba20166ba0f0f769008a
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006494"
---
## <a name="setting-up-the-library"></a>Настройка библиотеки

Вызовите `Start()`, чтобы разрешить обработку данных среды в сеансе.

Чтобы обрабатывать события, вызванные сеансом, задайте для свойства `delegate` сеанса объект, например представление. В этом объекте должен реализовываться протокол `SSCCloudSpatialAnchorSessionDelegate`.
