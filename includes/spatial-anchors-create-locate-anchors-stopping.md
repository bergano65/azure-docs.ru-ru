---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67185008"
---
## <a name="pause-reset-or-stop-the-session"></a>Приостановка, сброс или остановка сеанса

Чтобы временно остановить сеанс, можно вызвать `Stop()`. Это приведет к прерыванию обработки наблюдателей и среды даже при вызове ProcessFrame(). Затем можно вызвать `Start()`, чтобы возобновить обработку. При возобновлении обработанные в сеансе данные сохраняются.
