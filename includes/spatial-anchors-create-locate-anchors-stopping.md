---
ms.openlocfilehash: a165061b2f927d6302504819ef27c846d76d0f30
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006472"
---
## <a name="pause-reset-or-stop-the-session"></a>Приостановка, сброс или остановка сеанса

Чтобы временно остановить сеанс, можно вызвать `Stop()`. Это приведет к прерыванию обработки наблюдателей и среды даже при вызове `ProcessFrame()`. Затем можно вызвать `Start()`, чтобы возобновить обработку. При возобновлении обработанные в сеансе данные сохраняются.
