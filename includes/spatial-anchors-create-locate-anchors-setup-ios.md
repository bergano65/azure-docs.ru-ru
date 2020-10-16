---
ms.openlocfilehash: e7e3a31663e6c166b912ba20166ba0f0f769008a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "83006494"
---
## <a name="setting-up-the-library"></a>Настройка библиотеки

Вызовите `Start()`, чтобы разрешить обработку данных среды в сеансе.

Чтобы обрабатывать события, вызванные сеансом, задайте для свойства `delegate` сеанса объект, например представление. В этом объекте должен реализовываться протокол `SSCCloudSpatialAnchorSessionDelegate`.
