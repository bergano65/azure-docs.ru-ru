---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2020
ms.locfileid: "67185008"
---
## <a name="pause-reset-or-stop-the-session"></a>Приостановка, сброс или остановка сеанса

Чтобы временно остановить сеанс, можно вызвать `Stop()`. Это приведет к прерыванию обработки наблюдателей и среды даже при вызове ProcessFrame(). Затем можно вызвать `Start()`, чтобы возобновить обработку. При возобновлении обработанные в сеансе данные сохраняются.
