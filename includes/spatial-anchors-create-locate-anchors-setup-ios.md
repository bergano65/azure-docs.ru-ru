---
ms.openlocfilehash: 26eff7b10542843be561264c316bdcf1a63271f7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67184988"
---
## <a name="setting-up-the-library"></a>Настройка библиотеки

Вызовите Start(), чтобы разрешить обработку данных среды в сеансе.

Чтобы обрабатывать события, вызванные сеансом, задайте для свойства `delegate` сеанса объект, например представление. Этот объект должен реализовывать протокол SSCCloudSpatialAnchorSessionDelegate.
