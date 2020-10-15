---
ms.openlocfilehash: a165061b2f927d6302504819ef27c846d76d0f30
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "83006472"
---
## <a name="pause-reset-or-stop-the-session"></a>Приостановка, сброс или остановка сеанса

Чтобы временно остановить сеанс, можно вызвать `Stop()`. Это приведет к прерыванию обработки наблюдателей и среды даже при вызове `ProcessFrame()`. Затем можно вызвать `Start()`, чтобы возобновить обработку. При возобновлении обработанные в сеансе данные сохраняются.
